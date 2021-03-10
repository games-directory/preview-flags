# Insider Preview

This is a placeholder repository for an upcoming Rails engine that allows you to create preview flags in your app.
The system is currently being developed and battle tested in [games.directory](https://games.directory) and [games.directory(repo)](https://github.com/games-directory/games.directory) and inspired by the [XBOX Live Insider Preview](https://news.xbox.com/en-us/2019/08/16/xbox-insider-rings/) program.

Our main goal is to achieve a very simple, ring based system, for your Rails app where you can enable/disable certain features based on a User's ring.
```ruby
class DirectoryFeature < ApplicationRecord
  serialize :current_status

  # Enums
  #
  enum ring: [:'alpha-skip', :alpha, :beta, :delta, :omega]

  def available?(user)
    return user.admin?
    return user.ring == ring
  end
end
```
For example, given that we have this feature,
```ruby
DirectoryFeature.create(
  name: 'gaming_session_reflex',
  ring: :beta,
  label: 'Gaming Sessions',
  description: 'This feature allows a User to start a gaming session, or let games.directory automatically detect one, and gather some informations about it such as Time Played, Trophies etc..',
  released_at: DateTime.new(2020, 1, 1),
  next_ring_release_at: DateTime.new(2020, 5, 1),
  final_release_planned_at: DateTime.new(2020, 12, 1),
  current_status: {
    date: DateTime.now,
    description: "We're still thinkering with finding out the best way of releasing this to a larger userbase without affecting system performance and hitting API rate limits."
  }
)
```
and then we can check if the current_user has access to this feature through:
```ruby
@feature = DirectoryFeature.find('gaming_session_reflex')

- if @feature.available?(user.ring)

  = link_to('Start Session', '#', data: { reflex: 'click->Session#check', 'reflex-root': dom_id(current_user, 'session') }, class: 'btn -purple -small -round')

- else

  p.text-sm.text-white.font-light
    = "only available to #{ feature.ring } users. We're fine tuning some things before releasing to more users"

```
