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
          
4.Use Required Folder Structure
5. Work For Repository
      <p>Create New Interface<br>
        InterFace Code Goes Here</p>
   <!--
        using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using WebApplication.Data.Models;

namespace WebApplication.Repository.Interfaces
{
   public interface ICustomerRepository
    {

        List<Customer> GetCustomers();
       Customer GetCustomer(int id);
       void InsertCustomer(Customer cust);
       void UpdateCustomer(Customer cust);
       void DeleteCustomer(int id);
       void SaveCustomer();

    }
}
        -->
        
        
        <p>Implement Interface
        Implementation Code Goes Here</p>
        <!--
        using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using WebApplication.Data.Contexts;
using WebApplication.Data.Models;
using WebApplication.Repository.Interfaces;
using System.Data.Entity;
namespace WebApplication.Repository.Implementation
{
  public class CustomerRepository:ICustomerRepository
    {
      private CustomerDBContext db=new CustomerDBContext();
   
        public List<Customer> GetCustomers()
        {
            return db.Customers.ToList();
        }

        public Customer GetCustomer(int id)
        {
            return db.Customers.Find(id);
            
        }

        public void InsertCustomer(Customer cust)
        {
            db.Customers.Add(cust);
        }

        public void UpdateCustomer(Customer cust)
        {
            db.Entry(cust).State = EntityState.Modified;
        }

        public void DeleteCustomer(int id)
        {
            Customer existing = db.Customers.Find(id);
            db.Customers.Remove(existing);
        }

        public void SaveCustomer()
        {
            db.SaveChanges();
        }
    }
}
        
        -->
        
6. Work For Service(Class Library)
     <p> Add Reference<br>
      Create Interface <br>
        <u><b>Interface Code Goes Here</b></u><p>
            <!--
                using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using WebApplication.Data.Models;
using WebApplication.Repository.Implementation;
using WebApplication.Repository.Interfaces;

namespace WebApplication.Service.Interfaces
{
     
    public interface ICustomerService
    {
        
        List<Customer> GetCustomers();
        Customer GetCustomer(int id);
        void InsertCustomer(Customer cust);
        void UpdateCustomer(Customer cust);
        void DeleteCustomer(int id);
        void SaveCustomer();
    }
}
            
            -->
            
     <p> Implement Interface<br>
     <u><b>Interface Code Goes Here</b></u><p>
                <!--
                
                  using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using WebApplication.Data.Models;
using WebApplication.Repository.Implementation;
using WebApplication.Repository.Interfaces;
using WebApplication.Service.Interfaces;

namespace WebApplication.Service.Implementation
{
    public class CustomerService:ICustomerService
    {
        private ICustomerRepository db = new CustomerRepository();
        public List<Customer> GetCustomers()
        {
            return db.GetCustomers();
        }

        public Customer GetCustomer(int id)
        {
            return db.GetCustomer(id);
        }

        public void InsertCustomer(Customer cust)
        {
            db.InsertCustomer(cust);
        }

        public void UpdateCustomer(Customer cust)
        {
            db.UpdateCustomer(cust);
        }

        public void DeleteCustomer(int id)
        {
            db.DeleteCustomer(id);
        }

        public void SaveCustomer()
        {
            db.SaveCustomer();
        }
    }
}
                
                -->
                
                
7. Working With UI Controller(Application.Client)

<p>Controller Code Goes Here</p>
      
          <!--
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

          
          -->
        
Check View and Evaluate!!!!
        
        
      
