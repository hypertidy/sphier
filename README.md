Hierarchical Spatial Data
=========================

Non-hierarchical spatial data are points or vertices in one dimension and edges or paths in two dimensions. They are the only spatial entities which may be considered atomic; that is, not composed of anything else. Two vertices may form an path, or they may simply be two unrelated vertices (spatial autocorrelation notwithstanding). Whether they form a path or not will often depend on the intention of the person considering those vertices Thus we quickly arrive at the conclusion that spatial hierarchies are in the eye of the beholder. If the object or objective of an analysis concerns relationships between vertices, then the path connecting two vertices is likely to be important. Storing information on the system comprised of those two vertices then requires storing information on the vertices themselves, as well as on the way they are connected or related; that is, on the path

The `sphier` project aims to provide a common form for representing hierarchical data of any form, but is particularly oriented toward spatial data. The primary `sphier` entities are `vertex`, `path`, `object`, and `container`. The first three are equivalent to `silicate` `vertex`, `path`, and `object` entities, while the latter is analogous to, yet somewhat different from, a `silicate::path_link_vertex` table. Note that there are no `sphier` `coordinate` objects, as coordinates are contained within the `vertex` objects, along with a primary index enumerating the vertices. Silicate references vertices by position only, yet the necessity of referencing by index in `sphier` will be demonstrated below. The following concrete example illustrates this `sphier` structure:

``` r
p1 <- c (0, 01) # x, y coordinates
p2 <- c (1, 1)
s <- sphier (rbind (p1, p2), type = "a kind of line")
s
```

    ## $vertex
    ## # A tibble: 2 x 3
    ##   index     x     y
    ##   <dbl> <dbl> <dbl>
    ## 1     1     0     0
    ## 2     2     1     1
    ## 
    ## $path
    ## # A tibble: 2 x 2
    ##    path vertex
    ##   <int>  <int>
    ## 1     1      1
    ## 2     1      2
    ## 
    ## $container
    ## # A tibble: 1 x 3
    ##   object  type contains
    ##    <int> <chr>    <lgl>
    ## 1      1  path       NA
    ## 
    ## $object
    ## # A tibble: 1 x 3
    ##   object           type    value
    ##    <int>          <chr>    <dbl>
    ## 1      1 a kind of line 1.414214

The first column of the `container` object enumerates the number of the container, and is directly used to construct higher-order hierarchical objects as illustrated below. The first column of `object` indexes directly into the equivalent `object` column of `container`. In this case, there is only one single container, numbered 1, and that object describing that container holds the distance between the two vertices. Note that the `type` of object in `container` is `path`, not `a kind of line`. Containers use the two keywords `path` and `vertex` to refer to objects of those respective types, with these types - and only these types - being empty containers.

An Hierarchical Example
=======================

The "a kind of line" object in the preceding example was not hierarchical and therefore did not "contain" anything. Consider the addition of another (non-hierarchical) object giving the following `sphier` object:

``` r
p1 <- c (2, 3)
p2 <- c (2, 3)
s <- c (s, sphier (rbind (p1, p2), type = "a kind of line"))
s
```

    ## $vertex
    ## # A tibble: 4 x 3
    ##    indx     x     y
    ##   <int> <int> <int>
    ## 1     1     0     0
    ## 2     2     1     1
    ## 3     3     2     2
    ## 4     4     3     3
    ## 
    ## $path
    ## # A tibble: 4 x 2
    ##    path vertex
    ##   <int>  <int>
    ## 1     1      1
    ## 2     1      2
    ## 3     2      3
    ## 4     2      4
    ## 
    ## $container
    ## # A tibble: 2 x 3
    ##   object  type contains
    ##    <int> <chr>    <lgl>
    ## 1      1  path       NA
    ## 2      2  path       NA
    ## 
    ## $object
    ## # A tibble: 2 x 4
    ##   object           type    value   `2`
    ##    <int>          <chr>    <dbl> <dbl>
    ## 1      1 a kind of line 1.414214     2
    ## 2      2 a kind of line 1.414214     2

Those two separate lines may be considered to form part of some higher-level "metaline" object, represented as:

``` r
s <- c (s, sphier (object = c (1, 2), type = "metaline",
                   relationship = "thick"))
```

    ## $vertex
    ## # A tibble: 4 x 3
    ##    indx     x     y
    ##   <int> <int> <int>
    ## 1     1     0     0
    ## 2     2     1     1
    ## 3     3     2     2
    ## 4     4     3     3
    ## 
    ## $path
    ## # A tibble: 4 x 2
    ##    path vertex
    ##   <int>  <int>
    ## 1     1      1
    ## 2     1      2
    ## 3     2      3
    ## 4     2      4
    ## 
    ## $container
    ## # A tibble: 4 x 3
    ##   object     type contains
    ##    <dbl>    <chr>    <dbl>
    ## 1      1     path       NA
    ## 2      2     path       NA
    ## 3      3 metaline        1
    ## 4      3 metaline        2
    ## 
    ## $object
    ## # A tibble: 3 x 4
    ##   number           type    value relationships
    ##    <int>          <chr>    <dbl>         <chr>
    ## 1      1 a kind of line 1.414214          <NA>
    ## 2      2 a kind of line 1.414214          <NA>
    ## 3      3       metaline       NA         thick

In this case, the "metaline" object contains the two basic paths of two vertices. each, and declares that they share a "thick" relationship. The `vertex` object is the only aspect that geometrically anchors the entire data structure. The `container` object maps inter-relationships between all objects (both `path` and higher-level objects), and `object` includes the properties whether quantitative or qualitative of all higher-level objects.

Although `vertex` entities are considered inherently primitive objects that do not possess attributes, attributes of vertices may be readily stored by creating associated objects, as in the following example.

``` r
s <- c (s, sphier (vertex = 1, type = "vertex", colour = "blue"))
```

    ## $vertex
    ## # A tibble: 4 x 3
    ##    indx     x     y
    ##   <int> <int> <int>
    ## 1     1     0     0
    ## 2     2     1     1
    ## 3     3     2     2
    ## 4     4     3     3
    ## 
    ## $path
    ## # A tibble: 4 x 2
    ##    path vertex
    ##   <int>  <int>
    ## 1     1      1
    ## 2     1      2
    ## 3     2      3
    ## 4     2      4
    ## 
    ## $container
    ## # A tibble: 5 x 3
    ##   object     type contains
    ##    <int>    <chr>    <dbl>
    ## 1      1     path       NA
    ## 2      2     path       NA
    ## 3      3 metaline        1
    ## 4      3 metaline        2
    ## 5      4    point       NA
    ## 
    ## $object
    ## # A tibble: 4 x 5
    ##   number           type    value relationships colour
    ##    <int>          <chr>    <dbl>         <chr>  <chr>
    ## 1      1 a kind of line 1.414214          <NA>   <NA>
    ## 2      2 a kind of line 1.414214          <NA>   <NA>
    ## 3      3       metaline       NA         thick   <NA>
    ## 4      4          point       NA          <NA>   blue

This example also illustrates that the more information that is stored regarding the various `object` entities in a given spatial representation, the less efficient the object storage may ultimately become. Nevertheless, the storage of all data in this scheme of four flat tables is extremely advantageous for executing geometric operations on objects, paths, or vertices, because these operations can be directly translated into filtering and joining operations applied directly to these tables.

Time; trajectories; spatial dynamics
====================================

The `sphier` system explicitly accommodates representations of temporal dynamics. As the simplest level, a vector trajectory can be represented through extension of the `vertex` component to include a `t` column. The maximum that the `sphier` `vertex` table can contain is the four columns of (`x`, `y`, `z`, `t`).
