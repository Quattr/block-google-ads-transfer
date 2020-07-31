# Google Ads Block
### What does this Block do for me?

**(1) Combine Multiple Data Sources** - Combine your Google Ads data with data from GA, DoubleClick, Facebook Ads, Salesforce, and more to get a holistic view of all your customer data in one central view (see bottom of this page for instructions).

**(2) Work with Complex Account Hierarchies** - Provides possibilities for custom permissions structures and singular views when you have MCC codes with thousands of accounts or perhaps multiple Data Transfer Service accounts going into multiple BigQuery projects.

**(3) Unbounded Custom Metrics** - With Looker's modeling language (LookML) there are no limitations to the kinds of complex custom metrics that can be created including filtered measures, measures that require advanced mathematical functions, custom groupings. Anything is possible.

**(4) Take Advantage of Blocks Ecosystem** - Want to add powerful machine learning with BQML? Add the [BQML block](https://looker.com/platform/blocks/source/bigquery-machine-learning-by-google), there are 100s of blocks in the [blocks directory](https://looker.com/platform/directory/home).

**(5) Enterprise Data Platform** - Take advantage of Looker's data platform functionality, including [data actions](https://looker.com/platform/actions), scheduling, permissions, alerting, parameterization (each user can only see their own data), and more. Get immediate alerts when budgets are exceeded, ads are underperforming, or any other business criteria.

**(6) Usable / Shareable Dashboards** - create centralized dashboards for the entire team, and departmental or individual dashboards for each user, and rest easy knowing everyone is looking at the same information at all times. Then schedule the dashboard for emails or alerts, period-end reporting, anomaly detection, or whatever else serves your use-case.

### Block Info and Requirements

This Block is modeled on the schema brought in by Google [BigQuery Transfer Service for Google Ads](https://cloud.google.com/bigquery-transfer/docs/adwords-transfer). This block does not cover every table in the Google Ads schema, it’s designed to help you begin modeling the complex structure of the BigQuery data transfer for Google Ads.



The schema documentation for Google Ads can be found in [Google's docs](https://developers.google.com/ads/api/docs/appendix/reports/). This block was developed with [Google Ads API v201809](https://developers.google.com/adwords/api/docs/appendix/reports/all-reports)

### Google Ads Raw Data Structure

* **Entity Tables and Stats Tables** - There are several primary entities included in the Google Ads data set, such as ad, ad group, campaign, customer, keyword, etc.. Each of these tables has a corresponding _Stats_ table, which includes all the various metrics for that entity. For example, the _campaign_ entity table contains attributes for each campaign, such as the campaign name and campaign status. The corresponding stats table - _Campaign Basic Stats_ contains metrics such as impressions, clicks, and conversions. This [blog post](https://www.bounteous.com/insights/2019/08/21/navigating-google-ad-data-transfer-bigquery/) does a good job of explaining some of the main concepts. 

* **Snapshots** - Google Ads tables keep records over time by snapshotting all data at the end of each day. The following day, a new snapshot is taken, and appended to the table. There are two columns on each table: '_DATA_DATE' and '_LATEST_DATE'. '_DATA_DATE' tells you the day the data was recorded, while '_LATEST_DATE' is a **mutable** field that tells you the most recent date a snapshot was taken. Querying the table using '_DATA_DATE' = '_LATEST_DATE' in the 'WHERE' clause would give you only the data for the latest day.

* **About Partitioning** Currently the block uses Views (i.e. AdBasicStats) not tables (i.e. p_AdBasicStats) - if you are using partitioning then consider changing to use the tables and add [partitioning logic](https://discourse.looker.com/t/analytic-block-partitioned-date-filters-in-bigquery/4380).


### Block Structure

* **upstream_views** - these are views coming directly from BigQuery (with minor modifications). Generate more files as needed and store them in this folder.
* **shared_views** - These are common throughout the model
  * **date_base & period_base** - These are used to provide period over period comparisons
  * **ads_common** - This file contains common metrics in Ads fact tables

* **Entity Base** - This file contains all the common entity tables found across all Google Ads deployments. If you have additional entities you'd like to include, simply bring them into the Looker and model them the same way. Full documentation on each entity table and each metric can be found in [Google's documentation](https://developers.google.com/adwords/api/docs/appendix/reports).


### Implementation Instructions / Required Customizations

* **Manifest file** - The manifest file contains [constants](https://docs.looker.com/reference/manifest-params/constant) that should be adapted to your BigQuery project and account numbers.

* **Multiple Accounts** - Union fact and dimension tables if you have multiple accounts. Use a persisted derived table to store the result of the union.

* **Add Stats and Entity tables** - Only _ad_basic_stats_ is added as an example, other views should be imported and joined as needed.

### What if I find an error? Suggestions for improvements?

Great! Blocks were designed for continuous improvement through the help of the entire Looker community, and we'd love your input. To log an error or improvement recommendation, simply create a "New Issue" in the corresponding [Github repo for this Block](https://github.com/looker/block-google-ads-transfer-v2/issues). Please be as detailed as possible in your explanation, and we'll address it as quick as we can.
