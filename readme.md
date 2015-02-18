# Active Record Associations

### What are assocations?

- Active Record associations can be used to describe one-to-one, one-to-many and many-to-many relationships between models. 
- Each model uses an association to describe its role in the relation. 
- The belongs_to association is always used in the model that has the foreign key.

### Why associations?
- Think about the code you'd need to write every time you made an app that talks to a DB. 
- There's a lot of repetition there. Not dry.
- If you'd done a lot of projects, you'd probably have your own code that you shared across projects.
- As a programmer you should always be on the look out for optimizing workflows.

### belongs_to
- The belongs_to association creates a one-to-one match with another model.

```ruby
class Order < ActiveRecord::Base
  belongs_to :customer
end
```

- Each instance of the Order class will have the following methods

```ruby
# returns the associated object, if any. If no associated object is found, it returns nil.
@customer = @order.customer

# assigns an associated object to this object. 
# Behind the scenes, this means extracting the primary key from the associate object and setting this object's foreign key to the same value.
@order.customer = @customer

# The build_association method returns a new object of the associated type.
# The link through this object's foreign key will be set, but the associated object will not yet be saved.
@customer = @order.build_customer(customer_number: 123,
                                  customer_name: "JohnJoe Jones")

# The create_association method returns a new object of the associated type. 
# The link through this object's foreign key will be set
# Once it passes all of the validations specified on the associated model, the associated object will be saved.
@customer = @order.create_customer(customer_number: 123,
                                   customer_name: "JohnJoe Jones")
                                   
# Same as create_association above, but Raises ActiveRecord::RecordInvalid if the record is invalid.
create_customer!
```

### has_one

- The has_one association creates a one-to-one match with another model.
- This association says that the other class contains the foreign key.
- These are the methods that ActiveRecord adds to your class with a has_one association.
```ruby
association(force_reload = false)
association=(associate)
build_association(attributes = {})
create_association(attributes = {})
create_association!(attributes = {})
```
- In all of these methods, association is replaced with the symbol passed as the first argument to has_one.

```ruby
class Supplier < ActiveRecord::Base
  has_one :account
end
```
- What would be the methods that Active Record adds for you when you declare the above association?
- You can check if an associated object exists with `nil?`

```ruby
if @supplier.account.nil?
  @msg = "No account found for this supplier"
end
```

### has_many
- One-to-many relationship
- The other class will have a foreign key that refers to instances of this class

```ruby
class Customer < ActiveRecord::Base
  has_many :orders
end
```

- Here are the methods added to the model when you declare a has_many association
```ruby
# orders(force_reload = false)
@orders = @customer.orders

# orders<<(object, ...)
@customer.orders << @order1

# orders.delete(object, ...)
# Removes one or more objects from the collection by setting their foreign keys to NULL.
@customer.orders.delete(@order1)

# orders.destroy(object, ...)
# Removes one or more objects from the collection by running destroy on each object.
@customer.orders.destroy(@order1)

# Makes the collection contain only the supplied objects, by adding and deleting as appropriate.
orders=(objects)
@customer.orders = [@order1, @order2]

# order_ids
@order_ids = @customer.order_ids

order_ids=(ids)
orders.clear
orders.empty?
orders.size
orders.find(...)

# orders.where(...)
# Lazy loading 
@open_orders = @customer.orders.where(open: true) # No query yet
@open_order = @open_orders.first # Now the database will be queried

orders.exists?(...)
orders.build(attributes = {}, ...)
orders.create(attributes = {})
orders.create!(attributes = {})
```

### has_many :through

### has_and_belongs_to_many

- The `has_and_belongs_to_many` association creates a many-to-many relationship with another model. 
- This associates two classes via an intermediate join table that includes foreign keys referring to each of the classes.


### Choosing between many-to-many relationships (habtm vs. has_may :through)
- The rule of thumb is that if you need the join table to be an independent entity, use `has_many :through`
- This affords you validations, callbacks, and attributes on the join model
- It also affords you some good, thoughtful naming on the join table
- You will almost always end up needing a first-order model, so we recommend going with `has_many :through` unless you have a reason not to

### Resources

- [Rails Guide to Association Basics](Rails Guide to AR associations http://guides.rubyonrails.org/association_basics.html)
- [Active Record API](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html)
