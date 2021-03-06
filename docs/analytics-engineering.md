# What is analytics engineering?

In one sentence, I think analytics engineering is:

Helping organizations extract value from their data, through collecting, organizing, and interpreting.

An analogy is to be the manager of a communal workshop: create a destination where people can be a productive as possible, have the tools to do their best work, and contribute back to the workshop themselves.

From one of the founders on stackoverflow: https://news.ycombinator.com/item?id=24656588 "Our product, dbt (https://www.getdbt.com/), has come to be synonymous with the practice of analytics engineering, defining an entire industry."

## Interpreting

Interpreting requires more domain knowledge than anything else. 

The analytics engineer might know that there was actually an automated QA process that was creating new records in the database, which were essentially bots. Those bots shouldn't be counted in the user growth. They're not just monitoring the data itself, but they're close to the teams working on those projects, so they have the contextual awareness to help with that interpretation.

Other types of interpreting include not just "local knowledge". Example from FB: tracking sentiment of people that were exposed to FB "brand campaigns." But that's not _unique_ to an analytics engineer.

So if interpreting is one of the skills associated with analytics engineering, why is it called engineering? Engineering implies some level of building things - what are they building?

## Organizing

They primary way to ensure people avoid the issue with the data mentioned is to have it organized in a way that doesn't require constantly remembering and add custom resolutions to the data. Imagine if everyone at the organization had to implement their own solution to account for those automated users. It's inevitable someone would forget, or doing it in a way that was subtly wrong, or just make a typo.

Most organizations today organize their data in a system they call a data warehouse.

## Collecting

But there's now another problem - what if there's no reliable way to differentiate those bot users? 

The simplest solution might be to ask the engineers that implemented the automated QA to 

## What analytics engineering is not

Building data pipelines
Pulling data
Predictive modelling