# SQL Bulk Copy
* [SQL Bulk Insert](https://pritom.hashnode.dev/sql-bulk-insert-c-sharp)
* [SQL Bulk Update](https://pritom.hashnode.dev/sql-bulk-update-c-sharp)

# 1. SQL Bulk Update - C# (BulkCopy) Using C# (SQL BulkCopy)

https://pritom.hashnode.dev/sql-bulk-update-c-sharp

Steps: 

So there is no magic method to do a bulk update on the database using c#. But we can have some workaround on it. There are some steps to achieve that.

* Create a temp table.
* Insert all the data into the temp table.
* Update from that temp table to destination table.
* Drop temp table.

## Convert List to DataTable
```
private DataTable ToDataTable(List<T> items)
{
  DataTable dataTable = new DataTable(typeof(T).Name);
  PropertyInfo[] Props = typeof(T).GetProperties(BindingFlags.Public | BindingFlags.Instance);
  foreach (PropertyInfo prop in Props)
  {
    dataTable.Columns.Add(prop.Name);
  }
  foreach (T item in items)
  {
    var values = new object[Props.Length];
    for (int i = 0; i < Props.Length; i++)
    {
      values[i] = Props[i].GetValue(item, null);
    }
    dataTable.Rows.Add(values);
  }
  return dataTable;
}
```

## Convert List to DataTable
```
private DataTable ToDataTable(List<T> items)
{
  DataTable dataTable = new DataTable(typeof(T).Name);
  PropertyInfo[] Props = typeof(T).GetProperties(BindingFlags.Public | BindingFlags.Instance);
  foreach (PropertyInfo prop in Props)
  {
    dataTable.Columns.Add(prop.Name);
  }
  foreach (T item in items)
  {
    var values = new object[Props.Length];
    for (int i = 0; i < Props.Length; i++)
    {
      values[i] = Props[i].GetValue(item, null);
    }
    dataTable.Rows.Add(values);
  }
  return dataTable;
}
```


## Create Temp Table in SQL Server from DataTable
```
string GetTempTableCreateCmd(DataTable dataTable, string tempTable)
{
  StringBuilder columnTxt = new StringBuilder();
  columnTxt.Append($"CREATE TABLE {tempTable}(");
  int columnCount = dataTable.Columns.Count;
  for (int i = 0; i < columnCount; i++)
  {
    string dataType = dataTable.Columns[i].DataType == Type.GetType("System.String") ? "VARCHAR(100) " : dataTable.Columns[i].DataType.ToString();
    string colum = $"{dataTable.Columns[i]} {dataType}";
    columnTxt.Append($"{colum}");
    if (i != columnCount - 1)
      columnTxt.Append(", ");
  }
  columnTxt.Append(");");
  return columnTxt.ToString();
}
```

## ExecuteCmd
```
string tempTableTxtCmd = GetTempTableCreateCmd(datatable, tempTableName);
ExecuteCmd(tempTableTxtCmd, conn);

private void ExecuteCmd(string cmdTxt, SqlConnection connection)
{
  using (SqlCommand cmd = new SqlCommand(cmdTxt, connection))
  {
    cmd.ExecuteNonQuery();
  }
}
```

## Update Original from Temp Table
```
string GetOriginalTblToTempTableUpdateCmd(DataTable dataTable, string originalTable, string tempTable)
{
  StringBuilder updateTblCmd = new StringBuilder();
  updateTblCmd.Append("UPDATE ORGI SET ");

  for (int i = 1; i < dataTable.Columns.Count; i++)
  {
    updateTblCmd.Append($"ORGI.{dataTable.Columns[i]} = TEMP.{dataTable.Columns[i]}");

    if (i != dataTable.Columns.Count - 1)
      updateTblCmd.Append(", ");
  }

  updateTblCmd.Append($" FROM {tempTable} TEMP INNER JOIN {originalTable} ORGI ON ORGI.{dataTable.Columns[0]} = TEMP.{dataTable.Columns[0]}");

  return updateTblCmd.ToString();
}
```

## Drop Temp Table
```
var dropTempTableCmd = $"DROP TABLE {tempTableName}";
ExecuteCmd(dropTempTableCmd, conn);
```

# Developer Stories 🧛‍♂️🥂😎💥

Get Started with Any of the code base, follow this pattern:

`git clone --single-branch --branch <branch_name> https://github.com/purkayasta/Stories.git`

### DbSetMock in .NET (*Branch* => `dbset-mock`)
[DbContext, DbSet mocking - C# made easy](https://pritom.hashnode.dev/dbset-mocking-csharp)

### Exceptions in .NET 
1. [What, Why, How Exceptions in .NET](https://pritompurkayasta.me/exceptions-in-net) (*Branch* => `exceptions`)

### Multi Threading with C#
1. [Concurrency Vs Parallelism](https://pritompurkayasta.me/concurrency-vs-parallelism) (*Branch* => `paraconcurrent`)

### Asynchronous Programming With C#
1. [Task Based Programming](https://pritompurkayasta.me/task-based-programming-model)
2. [Thread Pool](https://pritompurkayasta.me/thread-pool)
3. [Asynchronous Programming](https://pritompurkayasta.me/asynchronous-programming-101) (*Branch* => `async_101`)

### Sql Bulk Insertion / Update (*Branch* => `bulkinsertupdate`)
1. [Bulk Insert](https://pritompurkayasta.me/sql-bulk-insert-c-sharp)
2. [Bulk Update](https://pritompurkayasta.me/sql-bulk-update-c-sharp)

### Intro To Dependency Injection:
> Types of DI:  (*Branch* => `DependencyInjection`)
> 1. [Constructor Injection](https://pritompurkayasta.medium.com/introduction-to-dependency-injection-part-1-217caafd15ad)
> 2. [Property Injection](https://pritompurkayasta.medium.com/introduction-to-dependency-injection-part-2-153bbcedaf52)
> 3. [Method Injection](https://pritompurkayasta.medium.com/introduction-to-dependency-injection-part-3-78889d3c8ed7)


>(*Branch* => `DependencyInjectionRealLife`)
>1. This is an [article on How to implement dependency injection
using unity in C#](https://pritompurkayasta.medium.com/dependecny-injection-in-real-life-c-project-using-unity-da1742f3bbab)

### Consume Third Party Api 101 🎈🎃🎨 (*Branch* => `ThirdPartyApi`)
How to Consume Any [Api Response in C#](https://pritompurkayasta.me/api-to-json-c-sharp)
