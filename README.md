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
        <u><b>InterFace Code Goes Here</b></u></p>
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
        
        
        <p>Implement Interface
        <u><b>Implementation Code Goes Here</b><u></p>
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
          using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Entity;
using System.Linq;
using System.Net;
using System.Web;
using System.Web.Mvc;
using WebApplication.Data.Contexts;
using WebApplication.Data.Models;
using WebApplication.Repository.Implementation;
using WebApplication.Repository.Interfaces;

namespace WebApplication.Client.Controllers
{
    public class CustomersController : Controller
    {
        private ICustomerRepository repo = new CustomerRepository();

        // GET: Customers
        public ActionResult Index()
        {
            return View(repo.GetCustomers());
        }

        

        // GET: Customers/Create
        public ActionResult Create()
        {
            return View();
        }

        // POST: Customers/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "Id,CustomerName,Address,EmailAddress,ContactNo")] Customer customer)
        {
            if (ModelState.IsValid)
            {
                repo.InsertCustomer(customer);
                repo.SaveCustomer();
              
                return RedirectToAction("Index");
            }

            return View(customer);
        }

        // GET: Customers/Edit/5
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Customer customer = repo.GetCustomer(Convert.ToInt32(id));
            if (customer == null)
            {
                return HttpNotFound();
            }
            return View(customer);
        }

        // POST: Customers/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "Id,CustomerName,Address,EmailAddress,ContactNo")] Customer customer)
        {
            if (ModelState.IsValid)
            {
                repo.UpdateCustomer(customer);
                repo.SaveCustomer();
                return RedirectToAction("Index");
            }
            return View(customer);
        }

        // GET: Customers/Delete/5
        public ActionResult Delete(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Customer customer = repo.GetCustomer(Convert.ToInt32(id));
            if (customer == null)
            {
                return HttpNotFound();
            }
            return View(customer);
        }

        // POST: Customers/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            repo.DeleteCustomer(id);
            repo.SaveCustomer();
            return RedirectToAction("Index");
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                //repo.Dispose();
            }
            base.Dispose(disposing);
        }
    }
}

          
          </p>
        
Check View and Evaluate!!!!
        
        
      
