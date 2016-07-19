# EntityFrameworkTestingFramework
TestWithEF 1.0.0

Testing with Entity Framework:

Assume you have a "Products" entity with "MyDBContext" in your project to test

In your test project create a new model - TestProducts
TestProducts.cs:

    public class TestProducts :TestDbSet<Database.Models.Products>
    {
    }

Create a new TestMyDbContext:
TestDbContext.cs

    public class TestMyDbContext : IMyDBContext
    {
        public DbSet<Products> Products { get; set; }
        
        public TestMyDbContext()
        {
            Products = new TestProducts();
        }
        public int SaveChanges()
        {
            return 0;
        }

        public Task<int> SaveChangesAsync()
        {
            return Task.FromResult(0);
        }

        public void Dispose()
        {
            
        }
    }
    
Now in your tests you can mock the DBContext.  I am using castle windsor 
  
    {
      var dbContent = _container.Resolve<IStrategyDbContext>();
    }

and inject where ever you want to.
