title: IRB for the lazy
author: radi
date: 2013-09-16
tags: ruby, rails

When experimenting in an `irb` or `rails` console, it can become quite tedious
to type out class names with namespaces if you are only concerned with
constants from one specific namespace:

```ruby
module Production
  class Order < ActiveRecord::Base
    # ...
  end

  class OrderItem < ActiveRecord::Base
    # ...
  end
end

1.9.3-p327 :001 > Production::OrderItem.where order_id: 62,
1.9.3-p327 :002 >   order_type: Production::Order.to_s
```
One possible solution is to manually assign the classes your experimenting with
to a shorter constant:

```ruby
1.9.3-p327 :001 > O = Production::Order
1.9.3-p327 :002 > I = Production::OrderItem
1.9.3-p327 :003 > I.where order_id: 62, order_type: O.to_s
```

Another way is to simply enter a nested `irb` session with the module that
represents the namespace you no longer want to type out:

```ruby
1.9.3-p327 :001 > irb Production
1.9.3-p327 :002 > self
 => Production
1.9.3-p327 :003 > Order
 => Production::Order(...)
1.9.3-p327 :004 > OrderItem.where order_id: 62, order_type: Order.to_s
```

Note that the constant without the namespace is merely a reference to the
original, so methods like `.to_s` in the previous example will behave as if they
were called on the original constant:

```ruby
1.9.3-p327 :005 > Order.to_s
1.9.3-p327 :006 > "Production::Order"
```

This can not only be done with namespaces, but with any ruby object. The current
scope, `self`, will then be set to the given object:

```ruby
1.9.3-p327 :001 > irb Production::Order.new
1.9.3-p327 :002 > self
 => #<Production::Order:0x007f8bd1105b30>
1.9.3-p327 :003 > to_s
 => "#<Production::Order:0x007f8bd1105b30>"
```

With this knowledge, you might save many, many keystrokes.
