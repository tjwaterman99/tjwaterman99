---
date: 2021-04-08
---

# Schema on read for analytics, please

__*Just load your JSON and stop rejecting "schema" issues*__

Defining a schema "on read" gets a bad rep because of applications that use "schemaless" databases like MongoDB. Invariably  those schemaless designs come back to bite you: dirty data gets in the database, and now your users have bugs that are difficult to find and are hard to fix.

For most types of applications, validating data before it enters the database is critical, and a schema enforced by the database itself is the method that has worked since the 1970s. I think you'd be crazy to not enforce a schema for your web app's database.

But in analytics, data is different. The data that your logging systems send is only ever sent once and it should never be mutated: a user signs up and you log a signup event, or a user views a page and you log a pageview event. Should you reject that data if they have an encoding error? Of course not, throwing away those events completely invalidates your data.

But that data still _feels_ wrong, so databases try to enforce a schema and "quarantine" data that doesn't match what they expect. Maybe that quarantine is just an s3 bucket, and the designers of that schema feel happy they've created such a "safe" system with only "clean" data. 

But would it really be so bad to expose consumers to that data, and ask them to clean it themselves? Does that data really _need_ to be cleaned before it enters the analytics database? Can it really _only_ be cleaned by data engineers?

Consider what happens when the data gets quarantined instead of loading it. A data engineer now needs to review the rejected data each day. They manually apply some transformations (hopefully not to the raw data itself, because that's causing data loss), and eventually they create a version that's fixed up and "clean." They then manually load it into the analytics database once they're done, and all their effort feels very productive: no one was exposed to that terrible, dirty data! 

In reality, all they're doing is spending huge amounts of time managing a duplicate system from the analytics database, completing work that is far more effectively completed by the downstream consumers themselves, and delaying those consumers from their projects. Of course, it helps that they've created a system that's "necessary" and requires their personal management each day. 

 meanwhile, take the plunge and allow a schemaless ingestion, just taking the dirty data as is, which is almost always just a simple JSON object. 

Anyone using that data has to do the work that only they can do: validate it's clean for their purposes. With a data modelling framework like dbt, it's also easy for them to share that cleaning back to the analytics warehouse so other users can use it as well. They don't need to ask a data engineer where their data went, and how long until it's available.

Of course, schemaless loading doesn't mean the data needs to stays schemaless. Once the data has been loaded, any issues with the schema can be handled by the analysts consuming it. 

I promise you, it's going to be okay. Load your JSON. Let your analysts play with it. If you really can't trust the analysts to handle the difference between `Account_id` and `account_id`, trying to fix that problem by automating your schema validation isn't going to work.