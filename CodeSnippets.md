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

```c#
namespace BlowOut.Models
{
    [Table("Client")]
    public class Client
    {
        [Key]
        [Display(Name = "ID Number")]
        public int clientID { get; set; }

        [Required(ErrorMessage = "A First Name is required")]
        [Display(Name ="First Name")]
        public string firstName { get; set; }

        [Required(ErrorMessage = "A Last Name is required")]
        [Display(Name = "Last Name")]
        public string lastName { get; set; }

        [Required(ErrorMessage = "An Address is required")]
        [Display(Name = "Address")]
        public string clientAddress { get; set; }

        [Required(ErrorMessage = "A City is required")]
        [Display(Name = "City")]
        public string clientCity { get; set; }

        [Required(ErrorMessage = "A State is required")]
        [Display(Name = "State")]
        public string clientState { get; set; }

        [Required(ErrorMessage = "A Zip Code is required")]
        [Display(Name = "Zip Code")]
        [RegularExpression(@"^[0-9]{0,15}$", ErrorMessage = "Zip Code should contain only numbers")]
        [StringLength(255, MinimumLength = 5, ErrorMessage = "Invalid Zip. Must be 5 numerical digits")]
        public string clientZip { get; set; }

        [Required(ErrorMessage = "An Email is required")]
        [Display(Name = "Email")]
        [RegularExpression(@"[\w-]+@([\w -]+\.)+[\w-]+", ErrorMessage = "Email should follow the format of: test@test.com")]
        public string clientEmail { get; set; }

        [RegularExpression(@"^(\([0-9]{3}\) |[0-9]{3}-)[0-9]{3}-[0-9]{4}$", ErrorMessage = "Phone Numbers should follow the format of: (123) 456-7890")]
        [Display(Name = "Phone Number")]
        [Required(ErrorMessage = "An Phone Number is required")]
        public string clientPhone { get; set; }
    }
}
```




  

 
  
