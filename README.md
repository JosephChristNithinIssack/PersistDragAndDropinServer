
# Persisting Drag and Drop in Server-Side using Syncfusion Grid in ASP.NET MVC

  

## Introduction:

  

In modern web applications, drag and drop functionality is a common requirement. In this tutorial, we will explore how to implement drag and drop functionality in **Syncfusion EJ2 Grid**, and persist the changes on the server-side using ASP.NET MVC. We will use the Syncfusion EJ2 Grid component and ASP.NET MVC framework to achieve this functionality.

  

## Setting up the Project

  

Start by creating a new ASP.NET MVC project in Visual Studio. Make sure to include the necessary dependencies for Syncfusion EJ2 Grid. We will be using the EJ2 Grid component in this tutorial. For more information on how to set up ASP.NET MVC project with EJ2 Grid, please refer the below documentation.

  

**https://ej2.syncfusion.com/aspnetmvc/documentation/grid/getting-started-mvc**

  

## Creating the View

Create a new view file (e.g., Index.cshtml) and add the following code:

```

<div>

<ejs-grid  id="Grid"  height="500"  allowExcelExport="true"  allowRowDragAndDrop="true"  rowDrop="rowDrop"  allowFiltering="true">

<e-data-manager  url="/Home/UrlDatasource"  adaptor="UrlAdaptor"></e-data-manager>

<e-grid-filterSettings  type="Excel"></e-grid-filterSettings>

<e-grid-columns>

<e-grid-column  field="OrderID"  headerText="Order ID"  isPrimaryKey="true"  textAlign="Right"  width="120"></e-grid-column>

<e-grid-column  field="CustomerID"  headerText="Customer Name"  width="150"></e-grid-column>

<e-grid-column  field="Freight"  headerText="Freight"  textAlign="Right"  format="C2"  width="120"></e-grid-column>

<e-grid-column  field="EmployeeID"  headerText="Employee ID"  width="170"></e-grid-column>

<e-grid-column  field="OrderDate"  headerText="Order Date"  width="150"></e-grid-column>

</e-grid-columns>

</ejs-grid>

</div>

<script>

function  rowDrop(args) {

var  grid = document.getElementById('Grid').ej2_instances[0];

args.cancel = true; // prevent the default action

let  ajax = new  ej.base.Ajax();

ajax.type = "POST"

ajax.url = "Home/Move"

let  moveposition = { oldIndex:  args.fromIndex, newIndex:  args.dropIndex };

ajax.data = JSON.stringify(moveposition);

ajax.send();

ajax.onSuccess = () => {

grid.refresh(); // refresh the Grid once the action is done

}

}

</script>
```
  
  

In this code, we have added the Syncfusion EJ2 Grid component with the necessary properties and events. The **rowDrop** event handler function is responsible for handling the drag and drop action. It sends an **AJAX request** to the server-side action method to persist the changes.

  

Step 3: Creating the Controller

In the HomeController.cs file, add the following code:

```

public IActionResult UrlDatasource([FromBody] DataManagerRequest dm)

{

IEnumerable DataSource = OrdersDetails.GetAllRecords();

DataOperations operation = new DataOperations();

if (dm.Search != null && dm.Search.Count > 0)

{

DataSource = operation.PerformSearching(DataSource, dm.Search); //Search

}

if (dm.Sorted != null && dm.Sorted.Count > 0) //Sorting

{

DataSource = operation.PerformSorting(DataSource, dm.Sorted);

}

if (dm.Where != null && dm.Where.Count > 0) //Filtering

{

DataSource = operation.PerformFiltering(DataSource, dm.Where, dm.Where[0].Operator);

}

int count = DataSource.Cast<object>().Count();

if (dm.Skip != 0)

{

DataSource = operation.PerformSkip(DataSource, dm.Skip); //Paging

}

if (dm.Take != 0)

{

DataSource = operation.PerformTake(DataSource, dm.Take);

}

return dm.RequiresCounts ? Json(new { result = DataSource, count = count }) : Json(DataSource);

}

public void Move([FromBody] RowIndex data) {

// you can also change the position of your data based on the from and drop index

if (data != null)

{

var tmp = OrdersDetails.GetAllRecords()[data.oldIndex];

if (data.oldIndex < data.newIndex)

{

for (int i = data.oldIndex; i < data.newIndex; i++)

{

OrdersDetails.GetAllRecords()[i] = OrdersDetails.GetAllRecords()[i + 1];

}

}

else if (data.oldIndex > data.newIndex)

{

for (int i = data.oldIndex; i > data.newIndex; i--)

{

OrdersDetails.GetAllRecords()[i] = OrdersDetails.GetAllRecords()[i - 1];

}

}

OrdersDetails.GetAllRecords()[data.newIndex] = tmp;

}

}
```
  

In the controller, we have defined two action methods: UrlDatasource and Move. The **UrlDatasource** method handles the AJAX request from the Grid to **fetch the data**. You can customize this method to retrieve data from your data source.

The **Move** method handles the **AJAX request** sent from the client-side when a drag and drop action occurs. Inside this method, you can update the position of your data based on the **oldIndex and newIndex** values. You can then persist the changes to your database or any other storage.

### API Links:

  * https://ej2.syncfusion.com/javascript/documentation/api/grid/#allowrowdraganddrop
  * https://ej2.syncfusion.com/javascript/documentation/api/grid/#rowdrop


## Conclusion:

  

In this tutorial, we learned how to implement drag and drop functionality in Syncfusion EJ2 Grid using ASP.NET MVC. We explored how to handle the drag and drop action and persist the changes on the server-side. By following these steps, you can enhance your web application by allowing users to interactively rearrange data in the Grid through drag and drop actions.

Remember to include the necessary dependencies and make any adjustments to the code based on your specific requirements. Happy coding!
