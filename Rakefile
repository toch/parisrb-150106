require 'slippery'
Slippery::RakeTasks.new do |s|

    s.options = {
      type: :reveal_js,
      theme: 'night',

    }

    s.processor 'head' do |head|
      head <<= H[:title, 'Zombie, Braaaaaain, and Ruby']
    end

    s.include_assets
    s.add_highlighting
end