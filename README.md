# ActiveRecord::Precount [![Build Status](https://travis-ci.org/k0kubun/activerecord-precount.svg?branch=master)](https://travis-ci.org/k0kubun/activerecord-precount)

N+1 count query killer for ActiveRecord.  
ActiveRecord::Precount allows you to cache count of associated records by eager loading.

## Why ActiveRecord::Precount?
Rails provides a way to resolve N+1 count query, which is [belongs\_to's counter\_cache option](http://guides.rubyonrails.org/association_basics.html#counter-cache).  
It requires a column to cache the count. But adding a column just for count cache is overkill.  
  
Thus this plugin enables you to preload counts in the same way as `has_many` and `belongs_to`.  
`count_loader` is an ActiveRecord's association, which is preloadable by `preload` or `includes`.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'activerecord-precount'
```

## Usage

### Enable count\_loader option
First, enable your has\_many association's count\_loader option.

```diff
 class Tweet
-  has_many :favorites
+  has_many :favorites, count_loader: true
 end
```

The option defines an additional association whose name is `favorites_count`.  
Its association type is not an ordinary one (i.e. `has_many`, `belongs_to`) but `count_loader`.

### Preload the association
This association works well by default.

```rb
@tweets = Tweet.all
@tweets.each do |tweet|
  p tweets.favorites_count # same as tweets.favorites.count
end
```

You can eagerly load `count_loader` association by `includes` or `preload`.

```rb
@tweets = Tweet.all.preload(:favorites_count)
@tweets.each do |tweet|
  p tweets.favorites_count # this line doesn't execute an additional query
end
```

Since it is association, you can preload nested `count_loader` association.

```rb
@users = User.all.preload(tweets: :favorites_count)
@users.each do |user|
  user.tweets.each do |tweet|
    p tweet.favorites_count # this line doesn't execute an additional query
  end
end
```

## Supported Versions

- Ruby
  - 2.0, 2.1, 2.2
- Rails
  - 4.1, 4.2

### Databases

- sqlite
- mysql
- postgresql

## Testing

```bash
$ bundle exec rake
```

## Contributing

1. Fork it ( https://github.com/k0kubun/activerecord-precount/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
