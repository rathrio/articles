title: Modules don't make your code modular
author: radi
date: 2013-09-14
tags: ruby, rails, oop

<!--
Ruby's modules allow fast and easy code sharing in form of mixins. This article
exposes a major trade-off in [cohesion][0] that comes with mixins by presenting
a common (anti)pattern in which modules are used to share code. It will then be
explained in which ways that method impairs code cohesion and how this can be
prevented with alternative sharing techniques.


It is not obvious that inheritance increases coupling in a code base, but
subclasses are highly dependant on their superclasses. A change to the
superclass might not only compromise code that uses the superclass, but also
code that relies on one of the subclasses. In ruby, including a module to a
class creates an *include class*, a class that wraps the included module, and
adds it to the class' ancestor chain:

```ruby
module Receipt
  def line_items
    # ...
  end
end

class Order
  include Receipt
end

Order.is_a? Receipt
  # => false

Order.ancestors
  # => [Order, Receipt, Object, Kernel, BasicObject]
```

Even though `Receipt` is not a superclass of `Order`, it can be found in
`Order`'s ancestor chain.
-->

A common pattern most notably found in Rails code is to excessively use mixins
to share behaviour across `ActiveRecord`s. The default way of representing a
database table is to inherit from `ActiveRecord::Base` or
[another `ActiveRecord`][1], limiting the use of inheritance as a code sharing
mechanism. As a consequence, many developers create modules with common
behaviour and use them as mixins to reuse the code:

```ruby
# Order and Invoice both have line items with prices that must be converted
# sometimes.

class Order < ActiveRecord::Base
  has_many :line_items, class_name: OrderItem

  def convert_prices(new_currency, conversion_rate)
    line_items.each do |item|
      item.price_currency = new_currency
      item.price_value   *= conversion_rate
    end
  end
end

class Invoice < ActiveRecord::Base
  has_many :line_items, class_name: InvoiceItem

  def convert_prices(new_currency, conversion_rate)
    line_items.each do |item|
      item.price_currency = new_currency
      item.price_value   *= conversion_rate
    end
  end
end

# The easiest way to eliminate the duplication is to extract 
```

The shared behaviour does usually not belong to any of the `ActiveRecord`s'
responsibilities or it in fact does, but multiple `ActiveRecord`s would
implement the exact same code. 

*Example 2* demonstrates a case where the use of mixins does make sense.

In the latter case, if the behaviour truly
concerns the `ActiveRecord`'s data, the common behaviour could often be
described as a *role* the `ActiveRecord`s play and can be extracted to a module:





<!--
Classes represent database tables and
are expected to inherit from `ActiveRecord::Base` or [another `ActiveRecord`][1].
Developers that are new to Rails tend to add all their business logic to their
`ActiveRecord` classes, which is not inherently a bad thing. It might get bad as
soon as one needs to share code between two ore more `ActiveRecord`s.
-->






[0]: http://en.wikipedia.org/wiki/Cohesion_(computer_science)
[1]: http://www.martinfowler.com/eaaCatalog/singleTableInheritance.html

<!--

###OUTLINE###

1. Section I
*Umbrella:*

-->
