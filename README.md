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

Lets say you have a controller ProductsController.cs

    [RoutePrefix("api/products")]
    public class ProductsController : ApiController
    {
        private readonly IMyDbContext _context;
        
        public ProductsController(IMyDbContext dbContext)
        {
            _context = dbContext;
        }
        
        [HttpPost]
        [Route("create")]
        public async Task<IHttpActionResult> Create(ProductModel model)
        {
            _context.Products.Add(new Product{
            //code 
            //code
            });
        }
    }

so to test the controller in your test file, you would simply:
    
    var dbContext = _container.Resolve<IStrategyDbContext>();
    var proudctContoroller = new ProductsController(dbContext);
    await productController.Create(new ProductModel());
    
    Assert.That(context.Products.Count(), Is.EqualTo(1));
    Assert.That(context.Products.First().Name, Is.EqualTo("Name"));

