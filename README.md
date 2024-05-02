# Inhalt
- [Search](#search)
- [Edit](#edit)
- [Delete](#delete)
- [OwnerUser](#owneruser)
- [Validation](#validation)
- [DTOs](#dtos)
- [ViewData](#viewdata)
- [SQLiteDB](#sqlitedb)
- [E-Mail](#email)
- [Formstyling](#formstyling)
- [Logincheck](#logincheck)
- [Navbarlink](#Navbarlink)
- [Seitentitel](#seitentitel)
- [Partial](#partial)
# Search
## onChange
Mit JavaScript
```js
$(document).ready(function () {
    $('#jobPostingTable').DataTable();            
});
```
## onSubmit
[Partial](#partial)
# Edit
```c#
<a asp-route-id="Id" asp-controller="Controller" asp-action="Funktion" class="btn btn-warning">Editieren</a>
```
# Delete

Button in der Seite
```c#
<button type="button" class="btn btn-danger" onclick="onDeleteClicked(Id)">Delete</button>
```

Modal
```c#
<!-- Modal -->
<div class="modal fade" id="confirmDelete" >
    <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Löschen bestätigen</h5>
                <button type="button" class="btn btn-close close"></button>
            </div>
            <div class="modal-body">
                Wollen Sie wirklich löschen?
            </div>
            <div class="modal-footer">                
                <button type="button" class="btn btn-success ok">Ja</button>
                <button type="button" class="btn btn-default cancel">Nein</button>
            </div>
        </div>
    </div>
</div>
```

JS-Code
```js
function onDeleteClicked(id) {
    //Frage den Benutzer...            
	$('#confirmDelete').modal('show');
	
    $('#confirmDelete .ok').on('click', function () {
        //Backend Call
        $.ajax({
            type: "POST",
            url: "@Url.Action("DeleteJobPosting")",
            data: { id: id },
            success: function (msg) {
                //alert("Ok, wurde gelöscht");
            location.reload();
            },
            error: function (msg) {
	            alert("Fehler beim Löschen");
            }
        });
        $('#confirmDelete').modal('hide');
    });
    $('#confirmDelete .cancel').on('click', function () {
        $('#confirmDelete').modal('hide'); 
    });
    $('#confirmDelete .close').on('click', function () {
        $('#confirmDelete').modal('hide');
    });
}
```
# OwnerUser
mit OwnerUser lässt sich ein Objekt dem User, der es erstellt hat zuweisen.
# Validation

In der Klasse der eingegebenen Daten mit Attribute Bedingungen festlegen:

```c#
[Required, MinLength(2, ErrorMessage = "Fehler!"), MaxLength(5)]
public string testString { get; set; } = string.Empty;

[Required, RegularExpression("\\w+[@]\\w+[.]\\w+", ErrorMessage = "Fehler!")]
public string Email { get; set; }
```

In dem View ganz unten:

```cs
@section Scripts {
    @{
        await Html.RenderPartialAsync("_ValidationScriptsPartial");
    }
}
```

Im View beim jeweiligen Feld:

```cs
<input class="form-control" asp-for="teststring" type="text" placeholder="test"/>
<span asp-validation-for="teststring" class="text-danger"></span>

<input class="form-control" asp-for="Email" type="text" placeholder="Mustermann" />
<span asp-validation-for="Email" class="text-danger"></span>
```
# DTOs
Wird verwendet wenn mehr als das "normale" Model übergeben werden soll. In diesem Fall erstellt man eine Klasse in welcher alle benötigten Daten gespeichert werden.

Beispiel:
```c#
public class DepartmentDTO
{
    public Department department {get; set;}
    public string requestedDepartment { get; set; }
}
```
# ViewData
Im Controller
```c#
public IActionResult CreateSpeiseForm()
{
    //// Informationen für eine Drop-Down-Liste bereitstellen
    List<SelectListItem> selectListItems = new List<SelectListItem>();
    SelectListItem eintrag1 = new SelectListItem("Yummy Good", "5");
    selectListItems.Add(eintrag1);
    SelectListItem eintrag2 = new SelectListItem("Good", "4");
    selectListItems.Add(eintrag2);
    SelectListItem eintrag3 = new SelectListItem("Nice", "3");
    selectListItems.Add(eintrag3);
    SelectListItem eintrag4 = new SelectListItem("Ok", "2");
    selectListItems.Add(eintrag4);
    SelectListItem eintrag5 = new SelectListItem("Awful", "1");
    selectListItems.Add(eintrag5);
    
    // Die Übergabe erfolgt über das ViewData-Dictionary
    ViewData["Kategorien"] = selectListItems;

    return View();
}
```

Im View
```c#
<select asp-for="Sterne" form-control" asp-items="@((IEnumerable<SelectListItem>)ViewData["Kategorien"])"></select>
```
# SQLiteDB
1. Nugets installieren
	- Core.Design
	- Core.Sqlite
	- Core.Tools


2. Application DB Context
	Hier werden die Models Speise und Zutat erstellt
```c#
public class ApplicationDbContext : DbContext
{
	// DBSet
	public DbSet<Speise> Speisen { get; set; }
	public DbSet<Zutat> Zutaten { get; set; }
	
	public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
	: base(options) {}
}
```
  
3. Program.cs
	In der Program.cs den ConnectionString festlegen
```c#
var connectionString = builder.Configuration.GetConnectionString("SpeisenDb");

builder.Services.AddDbContext<ApplicationDbContext>(options => options.UseSqlite(connectionString));

```

4. Connected Services
   Neuen Service SQLite-Datenbank
   
5. Datenbank aufsetzen
   Wie immer add-migration und update-database
# EMail
Beim Input einer Email als type "email" verwenden. Dieser übernimmt automatisch die Validierung! 
# Formstyling

```html
<form asp-controller="controller" asp-action="function" enctype="multipart/form-data">
    <div class="card">
        <div class="card-header">
            <h1>Header</h1>
        </div>
        <div class="card-body">
	        
	        ...
	        
            <div class="row mb-3">
                <div class="col">
                    <label>variable</label>
                    <input class="form-control" asp-for="variable"/>
                    <span asp-validation-for="variable" class="text-danger"></span>
                </div>
            </div>
            
            ...
            
        </div>
        <div class="card-footer">
            <button class="btn btn-primary" type="submit">Abschicken</button>
        </div>
    </div>
</form>

```
# Logincheck
```c#
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager

@if (SignInManager.IsSignedIn(User))
{
	//Code when signed in
}
else {
	//Code when not signed in
}
```
# Navbarlink
Bei Unterschied zwischen eingelogt oder nicht:
in LoginPartial.cshtml Element hinzufügen

Wenn er immer angezeigt werden soll:
Layout.cshtml abändern

# Seitentitel
```c#
@{
    ViewData["Title"] = "Hauptseite";
}
```

# Partial
\_...Partial.cshtml -> normale .cshtml-File mit dem ausgelagertem Teil

Erstellung der Partial-File im Controller:
```c#
public IActionResult GetJobPostingsPartial()
{
    return PartialView("_JobPostingListPartial", jobs);
}
```

Erstellung mit Querystring:
```c#
public IActionResult GetJobPostingsPartial(string query)
{
    if(string.IsNullOrEmpty(query))
    {
		//Ohne Query
    }
    else
    {
        //Query
    }
    return PartialView("_JobPostingListPartial", jobs);
}
```

Laden im View:
```js
$('#partialList').load('/Home/GetJobPostingsPartial')
```

Laden mit Querystring:
```js
function onClickSearch() {
    let query = $("#inputQuery").val();
    $('#partialList').load('/Home/GetJobPostingsPartial?query=' + query)
}
```
