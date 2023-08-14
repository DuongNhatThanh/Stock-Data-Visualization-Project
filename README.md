# Stock-Data-Visualization-Project

---

This project focuses on extracting and analyzing stock data for Tesla (TSLA) and GameStop (GME) using various Python libraries and techniques.

## Table of Contents

- [Define a function to make graph](#define-a-function-to-make-graph)
- [Use yfinance to Extract Stock Data](#use-yfinance-to-extract-stock-data)
- [Use Webscraping to Extract Tesla Revenue Data](#use-webscraping-to-extract-tesla-revenue-data)
- [Use Webscraping to Extract GME Revenue Data](#use-webscraping-to-extract-gme-revenue-data)
- [Plot Tesla Stock Graph](#plot-tesla-stock-graph)
- [Plot GME Stock Graph](#plot-gme-stock-graph)

## Define a function to make graph

The `make_graph` function is responsible for creating a graph that displays the historical share price and revenue data for a given stock. It utilizes the `plotly` library to generate interactive plots.

```python
def make_graph(stock_data, revenue_data, stock):
    # This line initializes a figure object (fig) using the make_subplots function from the plotly.subplots module. It creates a subplot grid with 2     rows and 1 column, sharing the x-axis, and assigns titles to each subplot.
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price","Historical Revenue"))

    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    """
    These lines add two traces to the figure (fig). The first trace represents the share price data, and the second
    trace represents the revenue data. Each trace is created using the go.Scatter class from the plotly.graph_objects module.
    The x-values are converted to datetime format using pd.to_datetime, and the y-values are converted to float. The name
    parameter assigns a label to each trace. The traces are added to specific rows and columns in the subplot grid.
    """
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    # These lines update the x-axis and y-axis titles for each subplot.
    fig.update_xaxes(title_text='Date', row=1, col=1)
    fig.update_xaxes(title_text='Date', row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    """
    This line updates the layout of the figure. It hides the legend, sets the height of the figure to 900 pixels, assigns the stock parameter as the     title, and enables a range slider for the x-axis.
    """
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
```

## Use yfinance to Extract Stock Data

The `yfinance` library is used to extract historical stock data for Tesla (TSLA) and GameStop (GME). The retrieved data is stored in Pandas DataFrames.

Using the `tkinder` function enter the ticker symbol of stock we want to extract data on to create a ticker object
```python
Stock_data = yf.Ticker('TSLA')
```
Using the ticker object and the function history extract stock information and save it into a dataframe named tesla_data. Set the period parameter to max so we can get information for the maximum amount of time.
```python
tesla_data = pd.DataFrame(Stock_data.history(period='Max'))
tesla_data.reset_index(inplace=True)
```

## Use Webscraping to Extract Tesla Revenue Data

Web scraping techniques are employed to extract revenue data for Tesla from a specific URL. The `requests` and `BeautifulSoup` libraries are used to retrieve and parse the HTML data, respectively.
```python
url = 'https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue'
htmtl_data = requests.get(url).text
soup = BeautifulSoup(htmtl_data,'html.parser')
tesla_revenue = soup.find_all('table')[1]
tesla_revenue=pd.read_html(str(tesla_revenue), flavor='bs4')[0]
tesla_revenue.rename(columns={'Tesla Quarterly Revenue(Millions of US $)': 'Date','Tesla Quarterly Revenue(Millions of US $).1': 'Revenue'}, inplace=True)
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',|\$','')
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
```

## Use Webscraping to Extract GME Revenue Data

Similar to the previous step, web scraping is performed to extract revenue data for GameStop from a different URL.
```python
gme = yf.Ticker('GME')
gme_data=gme.history(period="max")
gme_data.reset_index(inplace=True)
url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html'
html_data=requests.get(url).text
soup=BeautifulSoup(html_data,"html.parser")
gme_revenue=soup.find_all("table")[1]
gme_revenue=pd.read_html(str(gme_revenue), flavor='bs4')[0]
gme_revenue.rename(columns={'GameStop Quarterly Revenue(Millions of US $)': 'Date','GameStop Quarterly Revenue(Millions of US $).1': 'Revenue'}, inplace=True)
gme_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(',|\$',"")
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
```
## Plot Tesla Stock Graph

This section utilizes the `make_graph` function to plot the historical share price and revenue data for Tesla. The stock data is filtered to include only a specific date range.

```python
make_graph(tesla_data, tesla_revenue, 'TSLA')
```
<img width="2000" alt="image" src="https://github.com/DDDDNNNNNThanh/Stock-Data-Visualization-Project/assets/110702728/e4738bd0-df46-45ca-9a5e-96a183769139">


## Plot GME Stock Graph

Similar to the previous section, this part uses the `make_graph` function to plot the historical share price and revenue data for GameStop. The stock data is also filtered to a specific date range.

```python
make_graph(gme_data, gme_revenue, 'GME')
```
<img width="2000" alt="image" src="https://github.com/DDDDNNNNNThanh/Stock-Data-Visualization-Project/assets/110702728/fd233eaf-ac0e-41d4-8495-6a08484ec56a">


