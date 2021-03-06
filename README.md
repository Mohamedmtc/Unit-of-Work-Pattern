# Unit-of-Work-Pattern
Data Access Patterns: Unit of Work Pattern

The unit of work pattern is a great addition to our repository pattern. What it allows us to do is to reference multiple repositories inside our unit of work class. And when we want to commit data to our database, for instance, imagine that we have two repositories order Repository and Product Repository, both the order repository as well as our product repository want to create data at the same time. Instead of doing that in two transactions. We can make sure that we save the data in one transaction to the database. So our unit of work class will make sure that we reduce the communication with our database. So, in essence, applying the unit of work pattern means that we just introduce a class that holds a reference to our different repositories.

applying the unit of work pattern means that we just introduce a class that holds a reference to our different repositories to make this work and be able to commit all the work in the same transaction to the same data context. Each of the repositories inside our unit of work needs to share the same data context.

Unit of Work Pattern Characteristics

    1- References Multiple Repositories
    2- Reduce the communication with the database


# Use Case for Unit of work

let us assume in a single controller we used two repositories one for order and one for a customer, using the customer repository we created a customer with customer id autogenerated then we used this customer to create an order for him using the order repository, there will be an exception where the two repositories using different database context object 

so if we used the Unit of Work pattern where all the repositories use the same database context object the above exception will not occur 

```CSharp
public interface IUnitOfWork
    {
        IRepository<Customer> CustomerRepository { get; }
        IRepository<Order> OrderRepository { get; }
        IRepository<Product> ProductRepository { get; }

        void SaveChanges();
    }

public class UnitOfWork : IUnitOfWork
    {
        private ShoppingContext context;

        public UnitOfWork(ShoppingContext context)
        {
            this.context = context;
        }

        private IRepository<Customer> customerRepository;
        public IRepository<Customer> CustomerRepository
        {
            get
            {
                if (customerRepository == null)
                {
                    customerRepository = new CustomerRepository(context);
                }

                return customerRepository;
            }
        }

        private IRepository<Order> orderRepository;
        public IRepository<Order> OrderRepository
        {
            get
            {
                if(orderRepository == null)
                {
                    orderRepository = new OrderRepository(context);
                }

                return orderRepository;
            }
        }

        private IRepository<Product> productRepository;
        public IRepository<Product> ProductRepository
        {
            get
            {
                if (productRepository == null)
                {
                    productRepository = new ProductRepository(context);
                }

                return productRepository;
            }
        }

        public void SaveChanges()
        {
            context.SaveChanges();
        }
    }
```
# Unit of Work Critical Problem

one of the problems with the unit of work is that from an outside perspective, we might not know which of the repositories in our unit of work that our controller or action or method that we're calling is leveraging in a really large solution. You might break it up and have multiple, different units of works with only the repositories that make sense to be changed together because if you only have one large unit of work and you're passing that into a controller, you really have no clue what that particular controller or action or method is updating. So we saw here that the unit of work pattern is really powerful. It allows us to commit everything in one transaction
