# Fast Excel

#### Build / Release
[![Build status](https://ci.appveyor.com/api/projects/status/tcabp7tnn8dq5l05/branch/master?svg=true)](https://ci.appveyor.com/project/mrjono1/fastexcel-43v9o/branch/master)
[![License](http://img.shields.io/:license-MIT-blue.svg)](https://raw.githubusercontent.com/mrjono1/FastExcel/master/LICENSE)
[![NuGet Badge](https://buildstats.info/nuget/FastExcel)](https://www.nuget.org/packages/FastExcel/)

#### About
- Provides a fast way of reading and writing to *.xlsx Excel files.
- Small memory footprint while running
- Does not use the Open XML SDK to interact with the data but going directly and editing the underlying xml files.
- This project is not intended to be a replacement for full featured Excel packages with things like formatting, just light weight fast way of interacting with data in Excel.

#### Version 2
- Contribute using Visual Studio 2017
- Built using [.NetStandard](https://docs.microsoft.com/en-us/dotnet/standard/library) 1.3 so it should run on
	- .Net Core 1.0
	- .Net Framework 4.6
	- Mono
	- Xamarin.iOS
	- Xamarin.Androud
	- Universal Windows Platform

#### Version 1 (old)
[![Build status](https://ci.appveyor.com/api/projects/status/5cwbg9ffxqsdeguf/branch/master?svg=true)](https://ci.appveyor.com/project/mrjono1/fastexcel/branch/master)
- Built using .Net Framework 4.5
- Contribute using Visual Studio 2015
- This version may not be up to date

#### Installation
```
PM> Install-Package FastExcel
```

## Write Demo 1
This demo uses Generic objects, ie any object you wish with public properties
```C#
using (FastExcel.FastExcel fastExcel = new FastExcel.FastExcel(outputFile))
{
    List<MyObject> objectList = new List<MyObject>();

    for (int rowNumber = 1; rowNumber < NumberOfRecords; rowNumber++)
    {
        MyObject genericObject = new MyObject();
        genericObject.StringColumn1 = "A string " + rowNumber.ToString();
        genericObject.IntegerColumn2 = 45678854;
        genericObject.DoubleColumn3 = 87.01d;
        genericObject.ObjectColumn4 = DateTime.Now.ToLongTimeString();

        objectList.Add(genericObject);
    }
    fastExcel.Write(objectList, "sheet3", true);
}
```

## Write Demo 2
This demo lets you specify exactly which cell you are writing to

```C#
// Get your template and output file paths
FileInfo templateFile = new FileInfo("Template.xlsx");
FileInfo outputFile = new FileInfo("C:\\Temp\\output.xlsx");

//Create a worksheet with some rows
Worksheet worksheet = new Worksheet();
List<Row> rows = new List<Row>();
for (int rowNumber = 1; rowNumber < 100000; rowNumber++)
{
    List<Cell> cells = new List<Cell>();
    for (int columnNumber = 1; columnNumber < 13; columnNumber++)
    {
        cells.Add(new Cell(columnNumber, columnNumber * DateTime.Now.Millisecond));
    }
    cells.Add(new Cell(13, "Hello" + rowNumber));
    cells.Add(new Cell(14, "Some Text"));
 
    rows.Add(new Row(rowNumber, cells));
}
worksheet.Rows = rows;


// Create an instance of FastExcel
using (FastExcel.FastExcel fastExcel = new FastExcel.FastExcel(templateFile, outputFile))
{
    // Write the data
    fastExcel.Write(worksheet, "sheet1");
}
```

## Read Demo 1 Get Worksheet

```C#
// Get the input file paths
FileInfo inputFile = new FileInfo("C:\\Temp\\input.xlsx");

//Create a worksheet
DataSet worksheet = null;

// Create an instance of Fast Excel
using (FastExcel.FastExcel fastExcel = new FastExcel.FastExcel(inputFile, true))
{
    // Read the rows using worksheet name
    worksheet = fastExcel.Read("sheet1");

    // Read the rows using the worksheet index
    // Worksheet indexes are start at 1 not 0
    // This method is slightly faster to find the underlying file (so slight you probably wouldn't notice)
    worksheet = fastExcel.Read(1);
}
```

## Read Demo 2 Get All Worksheets

```C#
// Get the input file paths
FileInfo inputFile = new FileInfo("C:\\Temp\\fileToRead.xlsx");

// Create an instance of Fast Excel
using (FastExcel.FastExcel fastExcel = new FastExcel.FastExcel(inputFile, true))
{
    foreach (var worksheet in fastExcel.Worksheets)
    {
        Console.WriteLine(string.Format("Worksheet Name:{0}, Index:{1}", worksheet.Name, worksheet.Index));
        
        //To read the rows call read
        worksheet.Read();
        var rows = worksheet.Rows().ToArray();
        //Do something with rows
        Console.WriteLine(string.Format("Worksheet Rows:{0}", rows.Count()));
    }
}
```

## Update Demo

```C#
// Get the input file paths
FileInfo inputFile = new FileInfo("C:\\Temp\\input.xlsx");

//Create a some rows in a worksheet
Worksheet worksheet = new Worksheet();
List<Row> rows = new List<Row>();
                
for (int rowNumber = 1; rowNumber < NumberOfRecords; rowNumber+= 50)
{
    List<Cell> cells = new List<Cell>();
    for (int columnNumber = 1; columnNumber < 13; columnNumber+= 2)
    {
        cells.Add(new Cell(columnNumber, rowNumber));
    }
    cells.Add(new Cell(13, "Updated Row"));

    rows.Add(new Row(rowNumber, cells));
}
worksheet.Rows = rows;

// Create an instance of Fast Excel
using (FastExcel.FastExcel fastExcel = new FastExcel.FastExcel(inputFile))
{
    // Read the data
    fastExcel.Update(worksheet, "sheet1");
}
```

### Thanks to
- Sibz
- paritoshmmmec

- Insperation for this project came from [SejExcelExport](https://github.com/jsegarra1971/SejExcelExport) by jsegarra1971 who did a great job. I wanted to have my own crack at this problem.
