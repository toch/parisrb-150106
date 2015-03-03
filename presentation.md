![Zombie](images/zombie.jpg)

---

# Zombie, Braaaaaain, and Ruby

Christophe Philemotte, Paris.rb, 6 January 2015

---

### About me

* Developer ([@toch on GitHub](https://github.com/toch), [@_toch on Twitter](https://twitter.com/_toch))
* Author on [blog.8thcolor.com](http://blog.8thcolor.com)
* Founder of [PullReview.com](https://pullreview.com)

<br/>
<img src="images/PullReview-logo.png" width="150"/>

---

## Intro

---

### What's agent based modeling?

![ABM](images/ABM.jpg)

---

### why it's important?

* Complex Phenomena
* Macro is not enough
* Grasp Network

---

### Which use case?

---

![flu](images/flu-flight-routes-map.jpg)

---

![traffic](images/traffic-simulation-models.jpg) 

---

![electric grid](images/electric-grig-europe.jpg)

---

![internet](images/internet1.jpeg)

---

![social](images/agent_based_social_circle.png)

---

## We are humans, we are not alone

![Agent](images/agent_smith.jpg)

---

### Model of a human, and its demography

![Human](images/human-body.jpg)

---

```Ruby
class Agent
  attr_reader :state, :state_age, :current_action
  attr_accessor :position

  def initialize(map, stm)
    # ...
  end

  def walk(direction)
    # ...
  end

  def perceive
    # ...
  end

  def act
    # ...
  end

  def age
    # ...
  end

  def commit
    # ...
  end
end
```

---

### Model of Environment and Interactions

![World](images/World.png)

---

```Ruby
class Map
  attr_reader :width, :height
  def initialize(width, height, point_klass = Point)
    # ...
  end

  def point(x, y)
    # ...
  end

  def free_random_position
    # ...
  end
end
```

---

## Be Healthy, Dead, or a Zombie

```Ruby
class State
  attr_reader :name
  def initialize(name, action_strategy = ->(agent){ :stay })
  end

  def add_transition(target, check, priority = 0)
    # ...
  end

  def trigger_transition(agent)
    # ...
  end

  def decide_action_for(agent)
    # ...
  end
end
```

---

### Model of a State transition machine

![SIZX](images/sizx.jpg)

---

```Ruby
class StateTransitionMachine
  attr_reader :states
  def initialize(rand_klass = Random)
    @prng = rand_klass.new
    @states = {
      susceptible: State.new(
                     :susceptible,
                     ->(agent) {
                       [:walk, :stay, :fight].sample
                     }
                   ),
      infected: State.new(
                  :infected,
                  ->(agent) {
                    [:walk, :stay, :fight].sample
                  }
                ),
      zombie: State.new(
                :zombie,
                ->(agent) {
                  [:walk, :fight].sample
                }
              ),
      dead: State.new(:dead)
    }

    @states[:susceptible].add_transition(
      @states[:infected],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )

    @states[:infected].add_transition(
      @states[:zombie],
      ->(state, agent) {
        agent.state_age > 120
      }
    )

    @states[:susceptible].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      },
      10
    )


    @states[:infected].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )

    @states[:zombie].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            [:susceptible, :infected].include?(position.contents.state.name) &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )
  end

  def default_state
    return @states[:zombie] if @prng.rand(100) < 20
    @states[:susceptible]
  end
end
```

---

## Zombie swarm and Survival Strategies

---

```Ruby
class StateTransitionMachine
  attr_reader :states
  def initialize(rand_klass = Random)
    @prng = rand_klass.new
    @states = {
      susceptible: State.new(
                     :susceptible,
                     ->(agent) {
                       [:walk, :stay, :fight].sample
                     }
                   ),
      infected: State.new(
                  :infected,
                  ->(agent) {
                    [:walk, :stay, :fight].sample
                  }
                ),
      zombie: State.new(
                :zombie,
                ->(agent) {
                  [:walk, :fight].sample
                }
              ),
      dead: State.new(:dead)
    }

    @states[:susceptible].add_transition(
      @states[:infected],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )

    @states[:infected].add_transition(
      @states[:zombie],
      ->(state, agent) {
        agent.state_age > 120
      }
    )

    @states[:susceptible].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      },
      10
    )


    @states[:infected].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            position.contents.state.name == :zombie &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )

    @states[:zombie].add_transition(
      @states[:dead],
      ->(state, agent) {
        agent.position.neighborhood.each do |_, position|
          return true if
            position &&
            !position.empty? &&
            [:susceptible, :infected].include?(position.contents.state.name) &&
            position.contents.current_action == :fight &&
            @prng.rand(100) < 10
        end
        false
      }
    )
  end

  def default_state
    return @states[:zombie] if @prng.rand(100) < 20
    @states[:susceptible]
  end
end
```

---

### Calibration

![calib](images/maxresdefault.jpg)

---

### Simulation

---

![win](images/zombie_epidemic_1420498864.gif)

---

![fail](images/zombie_epidemic_1420500631.gif)

---

## Outro

* Unit Test
* Each part is simple
* Interactions is complex
* Calibration is the key
