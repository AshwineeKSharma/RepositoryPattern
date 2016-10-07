# RepositoryPattern
Repository Pattern Code and Reference 


1. Code First Entity Framework
    <p>Application.Data (Class Library)<br>
        Create Model/s)<br>
        Install Entity Framework Nuget Package<br>
        Create Context/s<br>
        Add Connection Strings<br>
        Enable-Migrations<br>
        Add-Migration MigrationName<br>
        Update-Database -Verbose</p>
      
2.Add class Library Packages 
     <p>Repository<br>
      Service</p>
      
3.Add UI Project(Application.Client)
     <p>Add References<br>
          Application.Data<br>
          Application.Repository<br>
          Application.Service</p>
          
4.Use Required Folder Structure<br>
5. Work For Repository<br>
      <p>Create New Interface<br>
        <u><b>InterFace Code Goes Here</b></u>
      </p>
<p>
using System;<br>
using System.Collections.Generic;<br>
using System.Linq;<br>
using System.Text;<br>
using System.Threading.Tasks;<br>
using WebApplication.Data.Models;<br>
<br>
namespace WebApplication.Repository.Interfaces<br>
{
<br>
   public interface ICustomerRepository<br>
    {<br>
        List<Customer> GetCustomers();<br>
       Customer GetCustomer(int id);<br>
       void InsertCustomer(Customer cust);<br>
       void UpdateCustomer(Customer cust);<br>
       void DeleteCustomer(int id);<br>
       void SaveCustomer();<br>
<br>
    }<br>
}<br>
</p>
        
        
        <p>Implement Interface</p>
        <p>
        <u><b>Implementation Code Goes Here</b></u>
      </p>
<p>
using System;<br>
using System.Collections.Generic;<br>
using System.Linq;<br>
using System.Text;<br>
using System.Threading.Tasks;<br>
using WebApplication.Data.Contexts;<br>
using WebApplication.Data.Models;<br>
using WebApplication.Repository.Interfaces;<br>
using System.Data.Entity;<br>
namespace WebApplication.Repository.Implementation<br>
{<br>
  public class CustomerRepository:ICustomerRepository<br>
    {<br>
      private CustomerDBContext db=new CustomerDBContext();<br>
   <br>
        public List<Customer> GetCustomers()<br>
        {<br>
            return db.Customers.ToList();<br>
        }<br>
<br>
        public Customer GetCustomer(int id)<br>
        {<br>
            return db.Customers.Find(id);<br>
            <br>
        }<br>

        public void InsertCustomer(Customer cust)<br>
        {<br>
            db.Customers.Add(cust);<br>
        }<br>
<br>
        public void UpdateCustomer(Customer cust)<br>
        {<br>
            db.Entry(cust).State = EntityState.Modified;<br>
        }<br>

        public void DeleteCustomer(int id)<br>
        {
            Customer existing = db.Customers.Find(id);<br>
            db.Customers.Remove(existing);<br>
        }<br>

        public void SaveCustomer()<br>
        {<br>
            db.SaveChanges();<br>
        }<br>
    }<br>
}<br>
        
</p>
        
6. Work For Service(Class Library)
     <p> Add Reference<br>
      Create Interface <br>
        <u><b>Interface Code Goes Here</b></u></p>
            
<p>using System;<br>
using System.Collections.Generic;<br>
using System.Linq;<br>
using System.Text;<br>
using System.Threading.Tasks;<br>
using WebApplication.Data.Models;<br>
using WebApplication.Repository.Implementation;<br>
using WebApplication.Repository.Interfaces;<br>

namespace WebApplication.Service.Interfaces<br>
{<br>
     
    public interface ICustomerService<br>
    {<br>
        
        List<Customer> GetCustomers();<br>
        Customer GetCustomer(int id);<br>
        void InsertCustomer(Customer cust);<br>
        void UpdateCustomer(Customer cust);<br>
        void DeleteCustomer(int id);<br>
        void SaveCustomer();<br>
    }
    <br>
    }
    </p>
            
     <p> Implement Interface<br>
     <u><b>Interface Code Goes Here</b></u></p>
       
<p>             
using System;<br>
using System.Collections.Generic;<br>
using System.Linq;<br>
using System.Text;<br>
using System.Threading.Tasks;<br>
using WebApplication.Data.Models;<br>
using WebApplication.Repository.Implementation;<br>
using WebApplication.Repository.Interfaces;<br>
using WebApplication.Service.Interfaces;<br>

namespace WebApplication.Service.Implementation<br>
{<br>
    public class CustomerService:ICustomerService<br>
    {<br>
        private ICustomerRepository db = new CustomerRepository();<br>
        public List<Customer> GetCustomers()<br>
        {<br>
            return db.GetCustomers();<br>
        }<br>

        public Customer GetCustomer(int id)<br>
        {<br>
            return db.GetCustomer(id);<br>
        }<br>
<br>
        public void InsertCustomer(Customer cust)<br>
        {<br>
            db.InsertCustomer(cust);<br>
        }<br>

        public void UpdateCustomer(Customer cust)<br>
        {<br>
            db.UpdateCustomer(cust);<br>
        }<br>
<br>
        public void DeleteCustomer(int id)<br>
        {<br>
            db.DeleteCustomer(id);<br>
        }<br>
<br>
        public void SaveCustomer()<br>
        {<br>
            db.SaveCustomer();<br>
        }<br>
    }<br>
}<br>
                <br>
                </p>
                
                
7. Working With UI Controller(Application.Client)

<p><u><b>Controller Code Goes Here</b></u></p>
      
<p>
using System;<br>
using System.Collections.Generic;<br>
using System.Data;<br>
using System.Data.Entity;<br>
using System.Linq;<br>
using System.Net;<br>
using System.Web;<br>
using System.Web.Mvc;<br>
using WebApplication.Data.Contexts;<br>
using WebApplication.Data.Models;<br>
using WebApplication.Repository.Implementation;<br>
using WebApplication.Repository.Interfaces;<br>

namespace WebApplication.Client.Controllers<br>
{<br>
    public class CustomersController : Controller<br>
    {<br>
        private ICustomerRepository repo = new CustomerRepository();<br>
<br>
        // GET: Customers<br>
        public ActionResult Index()<br>
        {<br>
            return View(repo.GetCustomers());<br>
        }<br>
<br>
        

        // GET: Customers/Create<br>
        public ActionResult Create()<br>
        {<br>
            return View();<br>
        }
<br>
       <p> // POST: Customers/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.</p><br>
        [HttpPost]<br>
        [ValidateAntiForgeryToken]<br>
        public ActionResult Create([Bind(Include = "Id,CustomerName,Address,EmailAddress,ContactNo")] Customer customer)<br>
        {<br>
            if (ModelState.IsValid)<br>
            {<br>
                repo.InsertCustomer(customer);<br>
                repo.SaveCustomer();<br>
              
                return RedirectToAction("Index");<br>
            }<br>

            return View(customer);<br>
        }<br>

        // GET: Customers/Edit/5
        public ActionResult Edit(int? id)<br>
        {<br>
            if (id == null)<br>
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);<br>
            }
            Customer customer = repo.GetCustomer(Convert.ToInt32(id));<br>
            if (customer == null)<br>
            {<br>
                return HttpNotFound();<br>
            }<br>
            return View(customer);<br>
        }<br>

        <p>// POST: Customers/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.</p>
        [HttpPost]<br>
        [ValidateAntiForgeryToken]<br>
        public ActionResult Edit([Bind(Include = "Id,CustomerName,Address,EmailAddress,ContactNo")] Customer customer)<br>
        {<br>
            if (ModelState.IsValid)<br>
            {<br>
                repo.UpdateCustomer(customer);<br>
                repo.SaveCustomer();<br>
                return RedirectToAction("Index");<br>
            }<br>
            return View(customer);<br>
        }<br>

        // GET: Customers/Delete/5
        public ActionResult Delete(int? id)<br>
        {<br>
            if (id == null)<br>
            {<br>
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);<br>
            }<br>
            Customer customer = repo.GetCustomer(Convert.ToInt32(id));<br>
            if (customer == null)<br>
            {<br>
                return HttpNotFound();<br>
            }<br>
            return View(customer);<br>
        }

        // POST: Customers/Delete/5
        [HttpPost, ActionName("Delete")]<br>
        [ValidateAntiForgeryToken]<br>
        public ActionResult DeleteConfirmed(i<br>nt id)<br>
        {
            repo.DeleteCustomer(id);<br>
            repo.SaveCustomer();<br>
            return RedirectToAction("Index");<br>
        }<br>

        protected override void Dispose(bool disposing)<br>
        {<br>
            if (disposing)<br>
            {<br>
                //repo.Dispose();<br>
            }<br>
            base.Dispose(disposing);<br>
        }<br>
    }<br>
}
<br>
</p>
        
Check View and Evaluate!!!!
        
        
      
