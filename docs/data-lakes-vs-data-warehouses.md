# Data Lakes vs Data Warehouses

Yesterday I read through an interesting discussion on the A16Z blog, "[The Great Data Debate](https://a16z.com/2020/11/12/a16z-podcast-the-great-data-debate/)," about whether data lakes would consume data warehouses or vice versa.

I don't really have a horse in this race, but it's clear some companies and investors are making large bets. Snowflake, which produces one of the "Big Three" data warehouses, recently went public in the largest SaaS IPO in history. The company's former CEO [Bog Muglia](https://twitter.com/Bob_Muglia) (who's rather outspoken) was at the discussion and said "five years from now data is going to sit behind a SQL prompt, and SQL data warehouses will replace data lakes."

But Andreesen Horowitz's GP [Martin Casado](https://twitter.com/martin_casado) saw it differently: "I actually think over time you can argue that it’s the data lake that ends up consuming everything, not the data warehouse."

So I wanted to take a step back and try to understand the major differences between a data lake and a data warehouse. What are the trade-offs between the two types of technology, especially for a practitioner at a company that's not "mega scale" like Facebook?

### What's the difference between a lake and a warehouse?

I'll take the definition of a Data Lake that's used in the A16Z article:

> Data lakes is a blurry term used by different people to mean different things, but for the purposes of this discussion, let’s define data lakes as tabular data – so tables, rows and columns – stored in an open source file format, like Parquet or ORC, in a public cloud object storage, like S3 or Google Cloud storage.

The key part there is **public cloud object storage**. If you've worked with data that lives in S3 before, you'll appreciate that S3 offers no guarantees about the data itself: it might have null values that you don't want, and there's no notion of relationships between tables defined by constructs like foreign keys. It's like storing CSVs that are parsed whenever you run a query.

I'll define data warehouses a bit more directly:

>  Data warehouses are any columnar store database - including Snowflake, Redshift, Bigquery, and even Postgres with the [`cstore_fdw`](https://www.citusdata.com/blog/2014/04/03/columnar-store-for-analytics/) extension. They use a relational model and can provide guarantees about the state of the data that's been inserted.

So data lakes lose some of the data integrity benefits of a data warehouse, but by using a more generalized object storage, they enable running more general computations than what the data warehouse gives you, and they can also process data types that aren't supported by data warehouses, such as photos, audio, and videos.

It's also worth highlighting that the definitions I've used are different than what I see used more commonly. Martin Fowler, for example, emphasizes that the data in a data lake is "raw" while in a data warehouse that data has been transformed into a "single schema" (and presumably into a format that's helpful to end-users).

>  The data lake stores raw data, in whatever form the data source provides. There is no assumptions about the schema of the data, each data source can use whatever schema it likes... Data warehouses tend to go with the notion of a single schema for all analytics needs.
- Martin Fowler, ["DataLake (2015)"](https://martinfowler.com/bliki/DataLake.html)

I've emphasized a different distinction between warehouses and lakes because the definition that Martin uses is a bit out of date since he wrote it back in 2015. Today, almost everyone inserts "raw" data into their data warehouses, rather than modifying it in any way before inserting it. That was made possible because the newer warehouses like BigQuery and Snowflake separate storage costs from compute costs, and it's no longer cost-prohibitive to store all an organization's data in the warehouse directly.

So if you accept the definitions I've chosen, that data warehouses offer more guarantees about the state of the data, but less generalized computation, especially on complex data types like photos, audio, and videos, then it's clear that data lakes are only more useful than data warehouses when you have a need for those generalized computations. In other words, when you need machine learning. 

### Will Warehouses do Machine Learning?

Since the primary use case for data lakes is really just machine learning, especially on complex data types, it's worth considering whether warehouses will enable that kind of modelling in the future, and how long away that future is.  

Bob Muglia estimated it would be around 2025:

> The question is: what are the operations that actually need to be performed against data that sits in a data lake? Today anything associated with complex data, the data warehouse can’t help you, and so **there’s a huge reason to have a data lake today. In 2025, I don’t think so.**

Personally, I'd expect that all the warehouses offer at least some support for building models on complex data types before 2025, as BigQuery already offers [BigQuery ML](https://cloud.google.com/bigquery-ml/docs/introduction) for building regressions, clustering, and even neural nets. You can already run code today that looks like this:   

```sql
create model "product_recommender" (
    MODEL_TYPE='DNN_CLASSIFIER',
    ...
)
```

So I think it's fair to say that some machine learning and modelling capabilities are coming to warehouses, and personally I'm excited for those new features. 

### If warehouses offer machine learning, who needs a data lake?

Although warehouses will support some machine learning, I'm skeptical they'll ever be able to support all the modelling requirements of every organization, especially a large enterprise where machine learning is mission critical.

For example, Facebook analyzes every image that gets uploaded for content that violates its community guidelines, while YouTube does the same for every video. Companies like Rev have also built their entire product around transcribing audio to text, and likely do some of that automatically through models. 

Those models will always need to be built on a custom "in-house platform," because the companies require unique customizations over their models, and it's not cost effective for vendors to support those unique customizations when they're only relevant for a single customer.

But in my view, the need to run such sophisticated machine learning is comparatively niche. Most companies simply have no user-generated content they could even use, so building machine learning on complex data types like photos, audio, or video isn't relevant for them.

In the small number of cases where companies do have a genuine need for machine learning, I expect that the machine learning supported by the data warehouses will often be sufficient.

In the even smaller number of cases where companies need specialized machine learning that isn't supported by the warehouse, companies will usually be satisfied by loading just the data they need into an "ML environment," such as a simple Python Notebook, and building the models there. While that would mean some data duplication between environments, in my view it's a better alternative than supporting an entire data lake.

So I expect most organizations will adopt warehouses over lakes, while only the huge enterprises with mission-critical models will find it worth the cost to maintain their own data lakes and associated machine learning infrastructure.
