# Follow this for an A+

## Create the Project

- Create a New MVC (Choose MVC, not Empty) Project named `FinalExam`
- Add the Entity Framework package to the project thru Manage NuGet Packages

## Create the Database in SQL Server

- Copy the provided script in a new query and run
- Make sure the right ID's are assigned and edited
	- Click table and choose design
	- Double click on `Identity Specification` and choose seed/increment and make it a key if needs be
	- Check for any other database requirements that Anderson has stated in prompt
- Copy connection string from SQL Server Object explorer
- Add the connection string to the RIGHT Web.config file

```xml
<connectionStrings>
    <add name="DatabaseNameContext" connectionString="PASTE HERE"
      providerName="System.Data.SqlClient" />
  </connectionStrings>
```
- Now create the database model by right clicking the Models folder and choosing Add | Class
- Name it the table name
	- Remember that columns named ID or with ID at the end of the name will be primary keys
	- You can add the attribute `[DatabaseGenerated(DatabaseGeneratedOption.None)]` to turn off the system trying to maintain int primary keys
	- Add the [Table] and [Key] annotations (along with other annotations)


```c#
namespace BlowOut.Models
{
   [Table("Book")]
    public class Book
    {
        [Key]
        //[Display(Name = "ID Number")]
        public int BookID { get; set; }

        //[Required(ErrorMessage = "A Book Name is required")]
        //[Display(Name = "Book Name")]
        public string BookName { get; set; }

        //[Required(ErrorMessage = "A Book Author is required")]
        //[Display(Name = "Book Author")]
        public string BookAuthor { get; set; }

        //[Required(ErrorMessage = "A Book Name is required")]
        //[Display(Name = "Page Length")]
        public int PageLength { get; set; }

        //[Required(ErrorMessage = "A Book Name is required")]
        //[Display(Name = "Book Status")]
        public bool? Status { get; set; }

        

        public int? PersonID { get; set; }
        public virtual Person Person { get; set; }
    }
}
```
```c#
namespace BlowOut.Models
{
     [Table("Person")]
    public class Person
    {
        [Key]
        //[Display(Name = "ID Number")]
        public int PersonID { get; set; }

        //[Required(ErrorMessage = "A First Name is required")]
        //[Display(Name = "First Name")]
        public string FirstName { get; set; }

        //[Required(ErrorMessage = "A Last Name is required")]
        //[Display(Name = "Last Name")]
        public string LastName { get; set; }

        //[Required(ErrorMessage = "An Email is required")]
        //[Display(Name = "Email")]
        //[RegularExpression(@"[\w-]+@([\w -]+\.)+[\w-]+", ErrorMessage = "Email should follow the format of: test@test.com")]
        public string Email { get; set; }

        //[Required(ErrorMessage = "An Address is required")]
        //[Display(Name = "Address")]
        public string Address { get; set; }

        //[RegularExpression(@"^(\([0-9]{3}\) |[0-9]{3}-)[0-9]{3}-[0-9]{4}$", ErrorMessage = "Phone Numbers should follow the format 		of: (123) 456-7890")]
        //[Display(Name = "Phone Number")]
        //[Required(ErrorMessage = "An Phone Number is required")]
        public string Phone { get; set; }

        public int RoleID { get; set; }
        public virtual Role Role { get; set; }

        //[Required(ErrorMessage = "An username is required")]
        //[Display(Name = "Username")]
        public string Username { get; set; }

        //[Required(ErrorMessage = "A Password is required")]
        //[Display(Name = "Password")]
        public string Password { get; set; }

       
    }
}
```
```c#
[Table("Role")]
public class Role
{
[Key]
//[Display(Name = "ID Number")]
public int RoleID { get; set; }

//[Required(ErrorMessage = "A Role Description is required")]
//[Display(Name = "Role Description")]
public string RoleDescription { get; set; }

}
```
- Modify Global.asax file to include using for models and the folder that will contain the context class (It's the Database.Set)
```c#
namespace BlowOut
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            Database.SetInitializer<DALName>(null);

            AreaRegistration.RegisterAllAreas();
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
    }
}
```
- Add a new folder to the project called DAL
- Add a new class to this folder called NBAContext.cs
- NOTE: This is the name of your dbContext variable and string in the connection string (web.config)

```c#
namespace ProjectName.DAL
{
    public class DALName : DbContext
    {
        public DALName() : base("DALName")
        {

        }

        public DbSet<Team> Teams { get; set; }
        public DbSet<Player> Players { get; set; }
        public DbSet<Position> Positions { get; set; }
    }
}
```
- BUILD THE PROJECT
- Now go add scaffolded controllers making sure that generate views is selected and adding the model and the context to the scaffolding
	- You can do this by right mouse clicking on the controller folder and choosing New Scaffolded Item.
	- Choose MVC 5 Controller with views, using EntityFramework. Click Add
	- In the Model class click on the down arrow and choose Player. Click Add
- Save and build the project
- Run the project

## Helpful Code
### Add this to Home Controller for Static object and Database access
```c#
 public static LibraryCatalogContext db = new LibraryCatalogContext();
 public static Person oPerson = new Person();
```
### To create a login method
- Be weary of SQL Statement syntax!!!
```c#
[HttpGet]
[AllowAnonymous]
public ActionResult Login()
{
    return View();
}
[AllowAnonymous]
[HttpPost]
public ActionResult Login(FormCollection form, bool rememberMe = false)
{
    String username = form["Username"].ToString();
    String password = form["Password"].ToString();

    var Customer = db.Database.SqlQuery<Person>(
    "Select * " +
    "FROM Person " +
    "WHERE Username = '" + username + "' AND " +
    "RoleID = 1 AND " +
    "Password = '" + password + "'").ToList();

    var Admin = db.Database.SqlQuery<Person>(
    "Select * " +
    "FROM Person " +
    "WHERE Username = '" + username + "' AND " +
    "RoleID = 2 AND " +
    "Password = '" + password + "'").ToList();

    if (Customer.Count() > 0)
    {
	FormsAuthentication.SetAuthCookie(username, rememberMe);
	//string CustomerName = Customer[0].FirstName + " " + Customer[0].LastName;

	oPerson.PersonID = Customer[0].PersonID;
	oPerson.FirstName = Customer[0].FirstName;

	return RedirectToAction("Index", "Home", new { userlogin = username });
    }
    else if (Admin.Count() > 0)
    {
	FormsAuthentication.SetAuthCookie(username, rememberMe);

	oPerson.PersonID = Admin[0].PersonID;
	oPerson.FirstName = Admin[0].FirstName;

	//string AdminName = Admin[0].FirstName + " " + Admin[0].LastName;

	return RedirectToAction("Index", "Home", new { userlogin = username });
    }
    else
    {
	return View();
    }
}
```
- Or use this if you want to hardcode it
```c#
[HttpPost]
public ActionResult Login(FormCollection form, bool rememberMe = false)
{
String email = form["Email address"].ToString();
String password = form["Password"].ToString();

if (string.Equals(email, "greg@test.com") && (string.Equals(password, "greg")))
{
FormsAuthentication.SetAuthCookie(email, rememberMe);

return RedirectToAction("Index", "Home");

}
else
{
return View();
}
}
```
### The form for the Login view
```c#
@model BlowOut.Models.Instrument
@{
    ViewBag.Title = "Login";
}

<h2>User Login</h2>

<head>
    <meta name="viewport" content="width=device-width" />
    <title>RsvpForm</title>
    <link href="~/Content/bootstrap.min.css" rel="stylesheet" />
    <script src="~/Scripts/jquery-2.1.4.min.js"></script>
    <script src="~/Scripts/bootstrap.min.js"></script>
</head>
<body>
    <div class="jumbotron">
        <div class="container">
            @using (Html.BeginForm("Login", "Home", FormMethod.Post))
            {
                <label for="inputEmail">Email address</label>
                @Html.TextBox("Email address")
                <label for="inputPassword">Password</label>
                @Html.Password("Password")
                <button type="submit">Login</button>  
            }
        </div>
    </div>
</body>
```

### Part of the shared layout for navbar
```c#
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("BlowOut Instrument Rentals", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("Rentals", "Rental", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                    <li>@Html.ActionLink("Clients", "Index", "Clients")</li>
                    <li>@Html.ActionLink("Instruments", "Index", "Instruments")</li>
                    <li>@Html.ActionLink("Login", "Login", "Home")</li>
                </ul>
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody()
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>

    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
```
### Authentication to add
```c#
<system.web>
  <compilation debug="true" targetFramework="4.6.1" />
  <httpRuntime targetFramework="4.6.1" />
  <authentication mode="Forms">
    <forms loginUrl="~/Home/Login" timeout="2880" />
  </authentication>
</system.web>
```
 
 ### To make dropdown
 - In the Edit get controller method
 ```c#
  // GET: Books/Edit/5
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Book book = db.Books.Find(id);
            if (book == null)
            {
                return HttpNotFound();
            }
            ViewBag.PersonID = new SelectList(db.People, "PersonID", "FirstName", book.PersonID);
            #####ViewBag.ListBooks = db.Books.ToList();#####
            return View(book);
        }
 ```
 - And then add this in the Edit view
 ```c#
 <div class="form-group">
    @Html.LabelFor(model => model.BookName, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
	#####@Html.DropDownListFor(model => model.BookName, new SelectList(ViewBag.ListBooks, "BookID", "BookName"))#####
	@Html.ValidationMessageFor(model => model.BookName, "", new { @class = "text-danger" })
    </div>
  </div>
 ```
### Get and Post for confirmation and summary methods
```c#
//Get
public ActionResult Rent()
{
    return View();
}

[HttpPost]
public ActionResult Rent(Person person, int id, int id2)
{
    Book book = db.Books.Find(id);
    person = db.People.Find(id2);

    book.PersonID = person.PersonID;
    book.Status = false;
    db.SaveChanges();

    return RedirectToAction("Summary", new { book.BookName});
}

public ActionResult Summary(string BookName)
{
    ViewBag.BookName = BookName;
    return View();
}
```
### Rent confirmation view
```c#
@{
    ViewBag.Title = "Rent";
}
<link href="~/Content/myStyles.css" rel="stylesheet" />
@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()

    <div class="form-horizontal">
        <h4>Are you sure you want to rent this book?</h4>
        <br />
        <input type="submit" value="Rent" class="btn btn-default" />
        <br /><br /><br /><br />
    </div>
}
<div>
    @Html.ActionLink("Back to List", "Catalog")
</div>
```
### Rent summary view
```c#

@{
    ViewBag.Title = "Summary";
}

<h2>Summary</h2>

<h3>Thank you <strong>@FinalLibraryPractice.Controllers.HomeController.oPerson.FirstName</strong> for renting a @ViewBag.BookName</h3>
```
### Code for methods for a search
```c#
   public ActionResult Search()
        {
            return View();
        }

        [HttpPost]
        public ActionResult Search(string firstname, string lastname, string title)
        {
            var books = db.Books.Include(b => b.Author).Include(b => b.Genre).Where(a => a.Author.AuthorFirstName == firstname).Where(a => a.Author.AuthorLastName == lastname).Where(a => a.BookName == title);
            return View(books);
        }
	
	
	  public ActionResult Search()
        {
            return View();
        }

        [HttpPost]
        public ActionResult Search(string authorname)
        {
            var books = db.Books.Where(a => a.BookAuthor == authorname);
            return View("Results", books.ToList());
        }
```
- This is the serach form for the view
```c#
<form method="post">
    Author First Name:<br>
    <input type="text" name="firstname" placeholder="Steve">
    <br>
    Author Last name:<br>
    <input type="text" name="lastname" placeholder="Jobs">
    <br />
    Book Title:<br>
    <input type="text" name="title" placeholder="Jobs">
    <br><br>
    <input type="submit" value="Submit">
</form>
```
### For shwoing list of current user items
```c#
public ActionResult MyBooks()
{
    //var books = db.Books.Include(b => b.o);
    IEnumerable<Book> books = db.Database.SqlQuery<Book>(
  "SELECT * FROM Book, Person WHERE Book.PersonID = Person.PersonID AND Person.Username = '" + oPerson.Username + "'");

    return View(books.ToList());
}
```
- then just scaffold a view from the method using a list and the book model
