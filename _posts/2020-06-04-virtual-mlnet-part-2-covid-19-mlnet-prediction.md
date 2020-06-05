---
layout: post
comments: true
title: "Virtual ML.Net: Part-2 COVID-19 Time Series Analysis and Prediction using ML.Net framework"
category: data science
---



# Part - 2: COVID-19 Time Series Analysis and Prediction using ML.Net framework

## COVID-19
- As per [Wiki](https://en.wikipedia.org/wiki/Coronavirus_disease_2019) **Coronavirus disease 2019** (**COVID-19**) is an infectious disease caused by severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2). The disease was first identified in 2019 in Wuhan, the capital of China's Hubei province, and has since spread globally, resulting in the ongoing 2019–20 coronavirus pandemic.
- The virus had caused a pandemic across the globe and spreading/affecting most of the nations. 
- The purpose of notebook is to visualize the number of confirmed cases over time and predicting it for next 7 days using time series in ML.Net

### Acknowledgement
- [Johns Hopkins CSSE](https://github.com/CSSEGISandData/COVID-19/raw/master/csse_covid_19_data) for dataset
- [COVID-19 data visualization](https://www.kaggle.com/akshaysb/covid-19-data-visualization) by Akshay Sb

### Dataset

- [2019 Novel Coronavirus COVID-19 (2019-nCoV) Data Repository by Johns Hopkins CSSE - Time Series](https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data/csse_covid_19_time_series).

### Introduction 

This is **Part-2** of our analysis on the COVID-19 dataset provided by Johns Hopkins CSSE. In [**Part-1**]({% link _posts/2020-06-04-virtual-mlnet-part-1-covid-19-mlnet-analysis.md %}), I did data analysis on the dataset and created some tables and plots for getting insights from it. In Part-2, I'll focus on applying machine learning for making a prediction using time-series API's provided by ML.Net framework. I'll be building a model from scratch on the number of confirmed cases and predicting for the next 7 days. Later on, I'll plot these numbers for better visualization.

[**ML.Net**](https://dotnet.microsoft.com/apps/machinelearning-ai/ml-dotnet) is a cross-platform framework from Microsoft for developing Machine learning models in the .Net ecosystem. It allows .Net developers to solve business problems using machine learning algorithms leveraging their preferred language such as C#/F#. It's highly scalable and used within Microsoft in many of its products such as Bing, PowerPoint, etc.

**Disclaimer**: This is an exercise to explore different features present in ML.Net. The actual and predicted numbers might vary due to several factors such as size and features in a dataset.

### Summary

Below is the summary of steps we'll be performing

1. Define application level items
    - Nuget packages
    - Namespaces
    - Constants
    
2. Utility Functions
    - Formatters    

3. Dataset and Transformations
    - Actual from [Johns Hopkins CSSE](https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/csse_covid_19_time_series)
    - Transformed [time_series_covid19_confirmed_global_transposed.csv](time_series_covid19_confirmed_global_transposed.csv)
    
4. Data Classes
    - ConfirmedData : Provides a map between columns in a dataset
    - ConfirmedForecast : Holds predicted values

5. Data Analysis
    - Visualize Data using DataFrame API
    - Display Top 10 Rows - dataframe.Head(10)
    - Display Last 10 Rows - dataframe.Tail(10)
    - Display Dataset Statistics - dataframe.Description()
    - Plot of TotalConfimed cases vs Date

6. Load Data - MLContext
7. ML Pipeline
8. Train Model
9. Prediction/Forecasting
10. Prediction Visualization
11. Prediction Analysis
12. Conclusion

**Note** : Graphs/Plots may not render in GitHub due to security reasons, however if you run this notebook locally/binder they will render.


```C#
#!about
```

<table><tbody><tr><td><img src="data:image/png;base64, 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" width="125em"></img></td><td style="line-height:.8em"><p style="font-size:1.5em"><b>.NET Interactive</b></p><p>&#169; 2020 Microsoft Corporation</p><p><b>Version: </b>1.0.130302+84eb63d57e6b46e2e6be496a3555923d6f459802</p><p><b>Build date: </b>2020-06-03T19:01:42.3234746Z</p><p><a href="https://github.com/dotnet/interactive">https://github.com/dotnet/interactive</a></p></td></tr></tbody></table>


### 1. Define Application wide Items

#### Nuget Packages



```C#
// ML.NET Nuget packages installation
#r "nuget:Microsoft.ML"
#r "nuget:Microsoft.ML.TimeSeries"
#r "nuget:Microsoft.Data.Analysis"

// Install XPlot package
#r "nuget:XPlot.Plotly"
```


    Installed package Microsoft.ML version 1.5.0
    Installed package Microsoft.Data.Analysis version 0.4.0
    Installed package Microsoft.ML.TimeSeries version 1.5.0
    Installed package XPlot.Plotly version 3.0.1


#### Namespaces


```C#
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.ML;
using Microsoft.ML.Data;
using Microsoft.Data.Analysis;
using Microsoft.ML.Transforms.TimeSeries;
using Microsoft.AspNetCore.Html;
using XPlot.Plotly;
```

#### Constants


```C#
const string CONFIRMED_DATASET_FILE = "time_series_covid19_confirmed_global_transposed.csv";

// Forecast API
const int WINDOW_SIZE = 5;
const int SERIES_LENGTH = 10;
const int TRAIN_SIZE = 100;
const int HORIZON = 7;

// Dataset
const int DEFAULT_ROW_COUNT = 10;
const string TOTAL_CONFIRMED_COLUMN = "TotalConfirmed";
const string DATE_COLUMN = "Date";
```

### 2. Utility Functions

#### Formatters

By default the output of DataFrame is not proper and in order to display it as a table, we need to have a custom formatter implemented as shown in next cell. 


```C#
Formatter<DataFrame>.Register((df, writer) =>
{
    var headers = new List<IHtmlContent>();
    headers.Add(th(i("index")));
    headers.AddRange(df.Columns.Select(c => (IHtmlContent) th(c.Name)));
    var rows = new List<List<IHtmlContent>>();
    var take = DEFAULT_ROW_COUNT;
    for (var i = 0; i < Math.Min(take, df.Rows.Count); i++)
    {
        var cells = new List<IHtmlContent>();
        cells.Add(td(i));
        foreach (var obj in df.Rows[i])
        {
            cells.Add(td(obj));
        }
        rows.Add(cells);
    }

    var t = table(
        thead(
            headers),
        tbody(
            rows.Select(
                r => tr(r))));

    writer.Write(t);
}, "text/html");
```

### 3. Dataset and Transformations

#### Download Dataset
- Actual Dataset: [Johns Hopkins CSSE](https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/csse_covid_19_time_series)
- Transformed Dataset: [time_series_covid19_confirmed_global_transposed.csv](time_series_covid19_confirmed_global_transposed.csv)


I'll be using COVID-19 time series dataset from [Johns Hopkins CSSE](https://github.com/CSSEGISandData/COVID-19/blob/master/csse_covid_19_data/csse_covid_19_time_series) and will be performing predictions using **time_series_covid19_confirmed_global.csv** file.

The data present in these files have name of the countries as Rows and dates as columns which makes it difficult to map to our classes while loading data from csv. Also, it contains data per country wise. In order to keep things simple I'll work with global count of COVID-19 cases and not specific country.

I have done few transformations to the dataset as below and created transformed csv's
- Sum cases from all the countries for a specific date
- Just have two rows with Date and Total 
- Applied transformation to the csv for converting Rows into Columns and vice-versa. [Refer](https://support.office.com/en-us/article/transpose-rotate-data-from-rows-to-columns-or-vice-versa-3419f2e3-beab-4318-aae5-d0f862209744) for transformation.
- Below transposed files have been saved in the current GitHub directory. There is no change in dataset. The files have data till 05-27-2020
    - [time_series_covid19_confirmed_global_transposed.csv](time_series_covid19_confirmed_global_transposed.csv) : Columns - **Date, TotalConfirmed**

##### Before transformation

<img src="/images/virtualmlnet-prediction/time-series-before-transformation.png" alt="Time Series data before transofmation" style="zoom: 80%;" />

#### After transformation

<img src="/images/virtualmlnet-prediction/time-series-after-transformation.png" alt="Time Series data after transofmation" style="zoom: 80%;" />

### 4. Data Classes

Now, we need to create few data structures to map to columns within our dataset.

#### Confirmed cases


```C#
/// <summary>
/// Represent data for confirmed cases with a mapping to columns in a dataset
/// </summary>
public class ConfirmedData
{
    /// <summary>
    /// Date of confirmed case
    /// </summary>
    [LoadColumn(0)]
    public DateTime Date;

    /// <summary>
    /// Total no of confirmed cases on a particular date
    /// </summary>
    [LoadColumn(1)]
    public float TotalConfirmed;
}
```


```C#
/// <summary>
/// Prediction/Forecast for Confirmed cases
/// </summary>
internal class ConfirmedForecast
{
    /// <summary>
    /// No of predicted confirmed cases for multiple days
    /// </summary>
    public float[] Forecast { get; set; }
}
```

### 5. Data Analysis

For loading data from csv, first we need to create MLContext that acts as a starting point for creating a machine learning model in ML.Net. Few things to note
- Set hasHeader as true as our dataset has header
- Add separatorChar to ',' as its a csv

#### Visualize Data - DataFrame


```C#
var predictedDf = DataFrame.LoadCsv(CONFIRMED_DATASET_FILE);
```


```C#
predictedDf.Head(DEFAULT_ROW_COUNT)
```




<table><thead><th><i>index</i></th><th>Date</th><th>TotalConfirmed</th></thead><tbody><tr><td>0</td><td>1/22/2020</td><td>555</td></tr><tr><td>1</td><td>1/23/2020</td><td>654</td></tr><tr><td>2</td><td>1/24/2020</td><td>941</td></tr><tr><td>3</td><td>1/25/2020</td><td>1434</td></tr><tr><td>4</td><td>1/26/2020</td><td>2118</td></tr><tr><td>5</td><td>1/27/2020</td><td>2927</td></tr><tr><td>6</td><td>1/28/2020</td><td>5578</td></tr><tr><td>7</td><td>1/29/2020</td><td>6166</td></tr><tr><td>8</td><td>1/30/2020</td><td>8234</td></tr><tr><td>9</td><td>1/31/2020</td><td>9927</td></tr></tbody></table>




```C#
predictedDf.Tail(DEFAULT_ROW_COUNT)
```




<table><thead><th><i>index</i></th><th>Date</th><th>TotalConfirmed</th></thead><tbody><tr><td>0</td><td>4/28/2020</td><td>3097229</td></tr><tr><td>1</td><td>4/29/2020</td><td>3172287</td></tr><tr><td>2</td><td>4/30/2020</td><td>3256910</td></tr><tr><td>3</td><td>5/1/2020</td><td>3343777</td></tr><tr><td>4</td><td>5/2/2020</td><td>3427584</td></tr><tr><td>5</td><td>5/3/2020</td><td>3506729</td></tr><tr><td>6</td><td>5/4/2020</td><td>3583055</td></tr><tr><td>7</td><td>5/5/2020</td><td>3662691</td></tr><tr><td>8</td><td>5/6/2020</td><td>3755341</td></tr><tr><td>9</td><td>5/7/2020</td><td>3845718</td></tr></tbody></table>




```C#
predictedDf.Description()
```




<table><thead><th><i>index</i></th><th>Description</th><th>TotalConfirmed</th></thead><tbody><tr><td>0</td><td>Length (excluding null values)</td><td>107</td></tr><tr><td>1</td><td>Max</td><td>3845718</td></tr><tr><td>2</td><td>Min</td><td>555</td></tr><tr><td>3</td><td>Mean</td><td>923109.56</td></tr></tbody></table>



##### Number of Confirmed cases over Time


```C#
// Number of confirmed cases over time
var totalConfirmedDateColumn = predictedDf.Columns[DATE_COLUMN];
var totalConfirmedColumn = predictedDf.Columns[TOTAL_CONFIRMED_COLUMN];

var dates = new List<string>();
var totalConfirmedCases = new List<string>();
for (int index = 0; index < totalConfirmedDateColumn.Length; index++)
{
    dates.Add(totalConfirmedDateColumn[index].ToString());
    totalConfirmedCases.Add(totalConfirmedColumn[index].ToString());
}
```


```C#
var title = "Number of Confirmed Cases over Time";
var confirmedTimeGraph = new Graph.Scattergl()
    {
        x = dates.ToArray(),
        y = totalConfirmedCases.ToArray(),
        mode = "lines+markers"
    };
    


var chart = Chart.Plot(confirmedTimeGraph);
chart.WithTitle(title);
display(chart);
```

<img src="/images/virtualmlnet-prediction/confirmed-time-before.png" alt="Confirmed cases over time" style="zoom:80%;" />

**Analysis**

- Duration: 1/22/2020 through 5/27/2020
- Total records: 127
- Case on first day: 555
- Case on last day: 5691790
- No of confirmed cases was low in the beginning, there was first jump around 2/12/2020 and an exponential jump around 3/22/2020.
- Cases have been increasing at an alarming rate in the past two months.

### 6. Load Data - MLContext


```C#
var context = new MLContext();
```


```C#
var data = context.Data.LoadFromTextFile<ConfirmedData>(CONFIRMED_DATASET_FILE, hasHeader: true, separatorChar: ',');
```

### 7. ML Pipeline

For creating ML Pipeline for a time-series analysis, we'll use [Single Spectrum Analysis](https://en.wikipedia.org/wiki/Singular_spectrum_analysis). ML.Net provides built in API for same, more details could be found at [TimeSeriesCatalog.ForecastBySsa](https://docs.microsoft.com/en-us/dotnet/api/microsoft.ml.timeseriescatalog.forecastbyssa?view=ml-dotnet) 


```C#
var pipeline = context.Forecasting.ForecastBySsa(
                nameof(ConfirmedForecast.Forecast),
                nameof(ConfirmedData.TotalConfirmed),
                WINDOW_SIZE, 
                SERIES_LENGTH,
                TRAIN_SIZE,
                HORIZON);
```

### 8. Train Model

We are ready with our pipeline and ready to train the model


```C#
var model = pipeline.Fit(data);
```

### 9. Prediction/Forecasting - 7 days

Our model is trained and we need to do prediction for next 7(Horizon) days.
Time-series provides its own engine for making prediction which is similar to PredictionEngine present in ML.Net. Predicted values show an increasing trend which is in alignment with recent past values.


```C#
var forecastingEngine = model.CreateTimeSeriesEngine<ConfirmedData, ConfirmedForecast>(context);
var forecasts = forecastingEngine.Predict();
display(forecasts.Forecast.Select(x => (int) x))
```


<table><thead><tr><th><i>index</i></th><th>value</th></tr></thead><tbody><tr><td>0</td><td>3348756</td></tr><tr><td>1</td><td>3450496</td></tr><tr><td>2</td><td>3563966</td></tr><tr><td>3</td><td>3690067</td></tr><tr><td>4</td><td>3830294</td></tr><tr><td>5</td><td>3985414</td></tr><tr><td>6</td><td>4156340</td></tr></tbody></table>


### 10. Prediction Visualization


```C#
var lastDate = DateTime.Parse(dates.LastOrDefault());
var predictionStartDate = lastDate.AddDays(1);

for (int index = 0; index < HORIZON; index++)
{
    dates.Add(lastDate.AddDays(index + 1).ToShortDateString());
    totalConfirmedCases.Add(forecasts.Forecast[index].ToString());
}
```


```C#
var title = "Number of Confirmed Cases over Time";
var layout = new Layout.Layout();
layout.shapes = new List<Graph.Shape>
{
    new Graph.Shape
    {
        x0 = predictionStartDate.ToShortDateString(),
        x1 = predictionStartDate.ToShortDateString(),
        y0 = "0",
        y1 = "1",
        xref = 'x',
        yref = "paper",
        line = new Graph.Line() {color = "red", width = 2}
    }
};

var chart1 = Chart.Plot(
new [] 
    {
        new Graph.Scattergl()
        {
            x = dates.ToArray(),
            y = totalConfirmedCases.ToArray(),
            mode = "lines+markers"
        }
    },
    layout
);

chart1.WithTitle(title);
display(chart1);
```

<img src="/images/virtualmlnet-prediction/confirmed-time-after.png" alt="Confirmed cases after prediction" style="zoom:80%;" />




### 11. Analysis

Comparing the plots before and after prediction, it seems our ML model has performed reasonably well. The red line represents the data on future date(5/8/2020). Beyond this, we predicted for 7 days. Looking at the plot, there is a sudden drop on 5/8/2020 which could be accounted due to insufficient data as we have only 127 records. However we see an increasing trend for next 7 days in alignment with previous confirmed cases. We can extend this model for predicting confirmed cases for any number of days by changing HORIZON constant value. This plot is helpful in analyzing the increased number of cases and allow authorities to take precautionary measures to keep the numbers low.

## Conclusion

I hope you have enjoyed reading the notebook, and might have got some idea on the powerful framework ML.Net. ML.Net is a very fast emerging framework for .Net developers which abstracts lot of complexity present in the field of Data science and Machine Learning. The focus of Part-2 notebook is leverage ML.Net for making predictions using time-series API. The model generated can be saved as a zip file and used in different applications.

**Source Code:**  https://github.com/praveenraghuvanshi1512/covid-19


If you liked it, please like/comment at [Comments](https://github.com/praveenraghuvanshi1512/TechnicalSessions/issues/1). It'll encourage me to write more. 

**Contact**

**LinkedIn :** https://in.linkedin.com/in/praveenraghuvanshi  
**Github   :** https://github.com/praveenraghuvanshi1512  
**Twitter  :** @praveenraghuvan



## References
- [Tutorial: Forecast bike rental service demand with time series analysis and ML.NET](https://docs.microsoft.com/en-us/dotnet/machine-learning/tutorials/time-series-demand-forecasting#evaluate-the-model)
- [Time Series Forecasting in ML.NET and Azure ML notebooks](https://github.com/gvashishtha/time-series-mlnet/blob/master/time-series-forecast.ipynb) by Gopal Vashishtha

#  ******************** Be Safe **********************
