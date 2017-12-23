# Design document

This program will implement the t-digest algorithm for fast and distributed
estimation of quantiles and other distribution parameters from sampled real 
valued (floating-point) signals.

## Problem statement

Characterising the distribution of data is one of the primary tasks of data
analysis. For normally distributed data this is easy, because the distribution
can be summarized using only the mean and variance, the calculation of which is
efficient and can be parallelized. However, many real world quantities are not
normally distributed; sometimes the real underlying distribution is not even
known, so the empirical distribution is used instead.

The problem with using the empirical distribution function is that it requires
storing essentially the entire sample or signal for calculating values of 
interest (e.g. the median or some other quantiles). As datasets grow, storing
them entirely in memory becomes impossible. Moreover, the naive computation 
cannot be paralellised, so larger datasets can only be dealt with using more
powerful machines instead of farming out the work to a cluster of cheaper
less powerful nodes.

The two variants of the t-digest algorithm (Dunning & Ertl) provide a solution
to this problem. The algorithms provide a fast, streaming, and parallelisable
estimate of the empirical distribution function. The t-digest provides a
compromise between space requirements and quantile accuracy, that can be tuned
by the user. Thus, when absolute accuracy is not needed, the t-digest can be
used to provide high accuracy quantiles with low memory requirements.

## Algorithms and data structures

Generating the t-digest requires a sorting algorithm as well as an 
implementation of k-means clustering. However, a more efficient approach, which
will also be adopted here is to have a balanced binary search tree.

Performance comparisons will also be carried out against the naive sorting 
implementation as well as possibly some other approximation method. 

## Inputs

Data in CSV format. Also library calls can be made by supplying a raw value
array.

## Output

t-digest serialization. Query functionality will also be provided.

## Space and time complexity

The space and time complexity of the t-digest algorithm are dependent on the
space-accuracy tradeoff chosen. This can be operationalised using a compression
factor delta, which describes the amount of memory required by a t-digest (i.e.
the space requirement is O(delta)). 

Generating the t-digest requires sorting the data as well as merging onto the
t-digest data structure. Jugding from the pesudocode given by Dunning and Ertl,
the overall time complexity should still be O(n*log(n)) for a constant value of 
delta. 

Querying a quantile is O(delta), which for a constant delta is of course O(1).
Likewise calculting trimmed means is O(delta)=O(1). 

## Bibliography

[1] Dunning, T., Ertl, O. (online). Computing extremely accurate quantiles using t-digests.
Available at https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf
