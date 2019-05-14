SQL Statement for BigQuery
--------------------------

* Data source: [Google Patents Public Datasets on BigQuery](https://console.cloud.google.com/marketplace/details/google_patents_public_datasets/google-patents-public-data?filter=solution-type:dataset&q=google%20patents%20public%20datasets&id=2877ec09-debc-41bd-a2d7-df1fd089e4d0)

* The following SQL selects the first claims of all US utility patents in 2015 and aggregate the CPC codes at subclass level for each patent in the first column of query result. (a caveat: the "CPC subclass" is called "group_id" in the "cpc_current" table, for unknown reason)

``` python
# python code 
query = '''
  SELECT STRING_AGG(distinct t2.%s order by t2.%s) AS cpc_ids, 
  t1.id, t1.date, text
  FROM `patents-public-data.patentsview.patent` t1, 
  `patents-public-data.patentsview.cpc_current` t2,
  `patents-public-data.patentsview.claim` t3
  where t1.id = t2.patent_id 
  and t1.id = t3.patent_id
  and timestamp(t1.date) >= timestamp('%s-01-01') 
  and timestamp(t1.date) <= timestamp('%s-12-31') 
  and t3.sequence='1'
  and t1.type='utility'
  group by t1.id, t1.date, t3.text  
''' % ('group_id', 'group_id', '2015', '2015')
```

* The following SQL selects all labels at the CPC subclass level in 2015.
``` python
# python code 
query = '''
  SELECT t1.id, t1.title
  FROM `patents-public-data.patentsview.%s` t1
  order by t1.id
''' % 'group_id'
```
