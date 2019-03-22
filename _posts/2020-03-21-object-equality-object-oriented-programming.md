---
layout: post
title: "Object Comparison"
description: "Object Comparison"
tags: [OOP]
comments: true
share: true
cover_image: '/content/images/2019/03/gabriel-gurrola-609666-unsplash.jpg'
---

When do you say two objects are equal?

Taking example of the below two and having ruby as the language,

### Comparing primitive objects

```ruby
irb(main):001:0> 1 == 1
=> true
irb(main):002:0> 'tasdik' == 'tasdik'
=> true
irb(main):003:0>
```

### Comparing custom objects

But what if you are having a custom class defined which has attributes for itself, you can’t really compare them using
`==` default.

For example.

```ruby
irb(main):001:0> module Money
irb(main):002:1>   class Wallet
irb(main):003:2>     attr_accessor :rupee, :paise
irb(main):004:2>
irb(main):005:2>     def initialize(rupee: 0, paise: 0)
irb(main):006:3>       @rupee = rupee
irb(main):007:3>       @paise = paise
irb(main):008:3>     end
irb(main):009:2>   end
irb(main):010:1> end
=> :initialize
irb(main):011:0>
irb(main):012:0> office_wallet = Money::Wallet.new
=> #<Money::Wallet:0x00007fb4328889c8 @rupee=0, @paise=0>
irb(main):013:0> personal_wallet = Money::Wallet.new
=> #<Money::Wallet:0x00007fb430991290 @rupee=0, @paise=0>
irb(main):014:0>
irb(main):015:0> office_wallet == personal_wallet
=> false
irb(main):016:0>
```

But as we can see that the two objects have the same attributes, which would be rupee and paise having the same values.
They should, logically be the same thing and they should have returned `true` as a result of the `==` comparison

`==` is a general comparison operator in ruby

At the Object level, `==` returns `true` only if obj and other are the same object. Typically, this method is overridden
in descendant classes to provide class-specific meaning.

This is the most common comparison, and thus the most fundamental place where you (as the author of a class) get to 
decide if two objects are “equal” or not.

The double equals method should implement the general identity algorithm to an object, which usually means that you 
should compare the object attributes and not if they are the same object in memory.
