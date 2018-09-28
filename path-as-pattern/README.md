# Intake: Parsing data from filenames and paths
## Introduction

Intake supports inspecting and loading multiple files with one command. This
significantly decreases the amount of code dedicated to concatenating data
across files. However in many circumstances data is encapsulated in the file
name or directory structure that causes concatenated data to lose some
important context. We have developed a method of specifying the patterns with
which to parse particular pieces of paths as new fields on the concatenated
data.


## How to use it
When loading multiple files, the locations of the files can often be provided
as a list or as a glob (glob notation is only supported in cases where there
is an unambiguous directory structure). For example to load precipitation data
for a number of emissions scenarios and models, a CSV data source may specify
that all files matching a particular glob be gathered:

```
urlpath: 'data/SRLCC_*_Precip_*.csv'
```

To setup a pattern for parsing information from filenames, catalog authors can
replace the `*` in glob notation with the desired field name. So in the case
above, the catalog entry becomes:

```
urlpath: 'data/SRLCC_{emissions}_Precip_{model}.csv'
```

When the data source is opened, the values for each declared field are
populated from the path or filename and returned on the data. In the intake
context, this string containing declared fields is referred to as a *pattern*
and the concept of populating data fields from the path using this pattern
is *path_as_pattern*.

When passing an explicit list of paths, the argument *path_as_pattern* can
be used to pass the *pattern* string:

```
urlpath:
  - 'data/SRLCC_a1b_Precip_ECHAM5-MPI.csv'
  - 'data/SRLCC_b1_Precip_PCM-NCAR.csv'
path_as_pattern: 'SRLCC_{emissions}_Precip_{model}.csv'
```

In this case the *pattern* is used to populate new columns ('emissions'
and 'model') on the data with the values for each set of data being populated
from the paths. Note that the *pattern* can just be a piece of the path as
long as it is unambiguous where the piece starts and stops (
`{emissions}_Precip_{model}` for instance would not yield the intended
outcome).

## How it works
The formatting of the *pattern* is python [format
string syntax]
(https://docs.python.org/3.7/library/string.html#format-string-syntax)
, but what is happening is more like the reverse of string formatting. You
can think of the relationship of the *pattern* to the format string like the
relationship between logs and exponents. The formulation ends up being:
Given ``format_string``(pattern) and ``resolved_string``(path), find
``arguments``({field: value...}) that would make true:
``format_string.format(**arguments) == resolved_string``.

Under the hood this method is implemented entirely independently of the path
and pattern context by setting up a helper function called `reverse_format`.
We'll call this function directly to demonstrate that any (reasonable) format
string syntax is supported and the parsed values will match the implied type
of the format string:

```python
>>> reverse_format('data_{year}_{month}_{day}.csv', 'data_2014_01_03.csv')
{'year': '2014', 'month': '01', 'day': '03'}
>>> reverse_format('data_{year:d}_{month:d}_{day:d}.csv', 'data_2014_01_03.csv')
{'year': 2014, 'month': 1, 'day': 3}
>>> reverse_format('data_{date:%Y_%m_%d}.csv', 'data_2016_10_01.csv')
{'date': datetime.datetime(2016, 10, 1, 0, 0)}
>>> reverse_format('{state:2}{zip:5}', 'PA19104')
{'state': 'PA', 'zip': '19104'}
```

## What's next
 - So far only the CSV plugin and intake-xarray plugin with `rasterio` driver
   support this behavior. More plugins can be made to respect path_as_pattern
   notation, but if the glob can be fed directly into an external library -
   as is often the case - then changes may be needed on the external library
   to keep track of the path associated with each file.
 - There are likely existing issues with the reverse formatting to get from
   path and pattern to value.
 - This feature seems most helpful in the context of multiple file loading,
   but it could certainly be extended to parse single files to save users
   time spent on string stripping and splitting.