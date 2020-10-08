
<style>body {max-width: 1100px; margin: auto; padding: 1em; line-height: 20px ;}</style>

<!-- README.md is generated from README.Rmd. Please edit that file -->

<!-- -->

<!-- FILL OUT OPTIONS !!! -->

<!-- -->

<!-- -->

<!-- -->

# Kafka R Bindings via ‘rJava’

**Status**

<a href="https://travis-ci.org/petermeissner/kafkaesque"><img src="https://api.travis-ci.org/petermeissner/kafkaesque.svg?branch=master"><a/>
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/petermeissner/kafkaesque?branch=master&svg=true)](https://ci.appveyor.com/project/petermeissner/kafkaesque)
<a href="https://codecov.io/gh/petermeissner/kafkaesque"><img src="https://codecov.io/gh/petermeissner/kafkaesque/branch/master/graph/badge.svg" alt="Codecov" /></a>
<a href="https://cran.r-project.org/package=kafkaesque">
<img src="http://www.r-pkg.org/badges/version/kafkaesque"> </a>
<img src="http://cranlogs.r-pkg.org/badges/grand-total/kafkaesque">
<img src="http://cranlogs.r-pkg.org/badges/kafkaesque">

*lines of R code:* 370, *lines of test code:* 46

**Version**

0.1.0 ( 2020-10-08 19:39:20 )

**Description**

Provides R bindings via ‘rJava’ for Kafka. Kafka is a event log data
base to which messages can be published or read from topics subscribed
to. Kafka is distributed, highly scalable, provides persistent event
storage and is designed for high throughput.

**License**

GPL-3 <br>Peter Meissner \[aut, cre\], Marius Pirv \[aut\], virtual7
\[cph\]

**Citation**

``` r
citation("kafkaesque")
```

``` r
Meissner P, Pirv M (2020). kafkaesque: Kafka R Bindings via 'rJava'. R package version 0.1.0.
```

**BibTex for citing**

``` r
toBibtex(citation("kafkaesque"))
```

    @Manual{,
      title = {kafkaesque: Kafka R Bindings via 'rJava'},
      author = {Peter Meissner and Marius Pirv},
      year = {2020},
      note = {R package version 0.1.0},
    }

**Installation**

Stable version from CRAN:

``` r
install.packages("kafkaesque")
```

Latest development version from Github:

``` r
devtools::install_github("petermeissner/kafkaesque")
```

# ToDos

``` r
kafkaesque::todo
```

    ## $consumer
    ## $consumer[[1]]
    ## [1] "change offset: i"
    ## 
    ## $consumer[[2]]
    ## [1] "offsets reported after seeking are not reported accurate"
    ## 
    ## 
    ## $general
    ## list()

# Content

``` r
library(kafkaesque)
```

    ## Loading required package: rJava

    ## Loading required package: kafkaesquejars

``` r
ls("package:kafkaesque")
```

    ## [1] "%>%"                  "java_class"           "java_methods"         "kafka_consumer"       "kafka_consumer_class"
    ## [6] "kafka_producer"       "kafka_set_log_level"  "todo"

# Usage

## Start Consumer (… Stop, Status)

``` r
library("kafkaesque")

# new consumer
consumer <- kafka_consumer()

# starting/connecting - + status
consumer$start()

consumer$running()
```

    ## [1] TRUE

``` r
consumer$end()$running()
```

    ## [1] FALSE

``` r
consumer$start()$running()
```

    ## [1] TRUE

## Properties aka Config

See here for list of consumer properties:
<https://kafka.apache.org/documentation/#consumerconfigs>.

``` r
consumer$props()
```

    ## $key.deserializer
    ## [1] "org.apache.kafka.common.serialization.StringDeserializer"
    ## 
    ## $value.deserializer
    ## [1] "org.apache.kafka.common.serialization.StringDeserializer"
    ## 
    ## $group.id
    ## [1] "4f89890f-4d0a-415a-ad1b-855197166be2"
    ## 
    ## $bootstrap.servers
    ## [1] "localhost:9092"
    ## 
    ## $auto.offset.reset
    ## [1] "earliest"
    ## 
    ## $client.id
    ## [1] "kafkaesque_consumer"

``` r
consumer$props(max.poll.records = 200)
```

    ## $key.deserializer
    ## [1] "org.apache.kafka.common.serialization.StringDeserializer"
    ## 
    ## $value.deserializer
    ## [1] "org.apache.kafka.common.serialization.StringDeserializer"
    ## 
    ## $max.poll.records
    ## [1] "200"
    ## 
    ## $group.id
    ## [1] "4f89890f-4d0a-415a-ad1b-855197166be2"
    ## 
    ## $bootstrap.servers
    ## [1] "localhost:9092"
    ## 
    ## $auto.offset.reset
    ## [1] "earliest"
    ## 
    ## $client.id
    ## [1] "kafkaesque_consumer"

## Topics and Subscriptions

``` r
# list topics available to consumer
consumer$topics_list()
```

    ## [1] "test500000" "test2"      "test3"      "test"

``` r
# subscribe to topic
consumer$topics_subscribe("test500000")
consumer$topics_subscription()
```

    ## [1] "test500000"

## Retrieving a Message

``` r
# retrieve next message
consumer$consume_next()
```

    ##         topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ## 1: test500000 <NA>         0      0 1602183154259   1\r    CreateTime           0                -1                   2

## Looping over Messages and Executing Code

``` r
# loop over messages and execute code
res <- 
  consumer$consume_loop(
    expr  = expression(print(msgs)),
    check = expression(counter < 4)
  )
```

    ##         topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ## 1: test500000 <NA>         0      1 1602183154259   2\r    CreateTime           0                -1                   2
    ##         topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ## 1: test500000 <NA>         0      2 1602183154259   3\r    CreateTime           0                -1                   2
    ##         topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ## 1: test500000 <NA>         0      3 1602183154259   4\r    CreateTime           0                -1                   2
    ##         topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ## 1: test500000 <NA>         0      4 1602183154259   5\r    CreateTime           0                -1                   2

``` r
# having a look at the statistics
res
```

    ## $start_time
    ## [1] "2020-10-08 21:48:43 CEST"
    ## 
    ## $end_time
    ## [1] "2020-10-08 21:48:43 CEST"
    ## 
    ## $n
    ## [1] 4

## Looping over Batches of Messages and Executing Code

``` r
# loop over batches of messages and execute code
res <- 
  consumer$consume_loop(
    expr  = 
      expression({
        print(msgs)
        cat("\n")
      }),
    check = expression(counter < 1000),
    batch = TRUE
  )
```

    ##           topic  key partition offset     timestamp value timestampType leaderEpoch serializedKeySize serializedValueSize
    ##   1: test500000 <NA>         0      5 1602183154259   6\r    CreateTime           0                -1                   2
    ##   2: test500000 <NA>         0      6 1602183154259   7\r    CreateTime           0                -1                   2
    ##   3: test500000 <NA>         0      7 1602183154259   8\r    CreateTime           0                -1                   2
    ##   4: test500000 <NA>         0      8 1602183154259   9\r    CreateTime           0                -1                   2
    ##   5: test500000 <NA>         0      9 1602183154259  10\r    CreateTime           0                -1                   3
    ##  ---                                                                                                                     
    ## 491: test500000 <NA>         0    495 1602183154260 496\r    CreateTime           0                -1                   4
    ## 492: test500000 <NA>         0    496 1602183154260 497\r    CreateTime           0                -1                   4
    ## 493: test500000 <NA>         0    497 1602183154260 498\r    CreateTime           0                -1                   4
    ## 494: test500000 <NA>         0    498 1602183154260 499\r    CreateTime           0                -1                   4
    ## 495: test500000 <NA>         0    499 1602183154260 500\r    CreateTime           0                -1                   4
    ## 
    ##           topic  key partition offset     timestamp  value timestampType leaderEpoch serializedKeySize serializedValueSize
    ##   1: test500000 <NA>         0    500 1602183154260  501\r    CreateTime           0                -1                   4
    ##   2: test500000 <NA>         0    501 1602183154260  502\r    CreateTime           0                -1                   4
    ##   3: test500000 <NA>         0    502 1602183154260  503\r    CreateTime           0                -1                   4
    ##   4: test500000 <NA>         0    503 1602183154260  504\r    CreateTime           0                -1                   4
    ##   5: test500000 <NA>         0    504 1602183154260  505\r    CreateTime           0                -1                   4
    ##  ---                                                                                                                      
    ## 496: test500000 <NA>         0    995 1602183154260  996\r    CreateTime           0                -1                   4
    ## 497: test500000 <NA>         0    996 1602183154260  997\r    CreateTime           0                -1                   4
    ## 498: test500000 <NA>         0    997 1602183154260  998\r    CreateTime           0                -1                   4
    ## 499: test500000 <NA>         0    998 1602183154260  999\r    CreateTime           0                -1                   4
    ## 500: test500000 <NA>         0    999 1602183154260 1000\r    CreateTime           0                -1                   5
    ## 
    ##           topic  key partition offset     timestamp  value timestampType leaderEpoch serializedKeySize serializedValueSize
    ##   1: test500000 <NA>         0   1000 1602183154260 1001\r    CreateTime           0                -1                   5
    ##   2: test500000 <NA>         0   1001 1602183154260 1002\r    CreateTime           0                -1                   5
    ##   3: test500000 <NA>         0   1002 1602183154260 1003\r    CreateTime           0                -1                   5
    ##   4: test500000 <NA>         0   1003 1602183154260 1004\r    CreateTime           0                -1                   5
    ##   5: test500000 <NA>         0   1004 1602183154260 1005\r    CreateTime           0                -1                   5
    ##  ---                                                                                                                      
    ## 496: test500000 <NA>         0   1495 1602183154263 1496\r    CreateTime           0                -1                   5
    ## 497: test500000 <NA>         0   1496 1602183154263 1497\r    CreateTime           0                -1                   5
    ## 498: test500000 <NA>         0   1497 1602183154263 1498\r    CreateTime           0                -1                   5
    ## 499: test500000 <NA>         0   1498 1602183154263 1499\r    CreateTime           0                -1                   5
    ## 500: test500000 <NA>         0   1499 1602183154263 1500\r    CreateTime           0                -1                   5

``` r
res
```

    ## $start_time
    ## [1] "2020-10-08 21:48:43 CEST"
    ## 
    ## $end_time
    ## [1] "2020-10-08 21:48:43 CEST"
    ## 
    ## $n
    ## [1] 1495

## Offsets and Seeking

``` r
# get current offsets from Kafka
consumer$topics_offsets()
```

    ##         topic partition offset
    ## 1: test500000         0   1500

``` r
# seek to end of topics 
consumer$topics_seek_to_end()
consumer$topics_offsets()
```

    ##         topic partition offset
    ## 1: test500000         0 500000

``` r
# seek to beginning of topics
consumer$topics_seek_to_beginning()
consumer$topics_offsets()
```

    ##         topic partition offset
    ## 1: test500000         0      0

# Developement Notes

Java sources are in `./java/kafkaesque/` folder. Building Java sources
can be done via Maven: `mvn install` will compile everything and copy
the `kafkaesque.jar` into the package’s `./inst/java/` folder while all
dependencies are copied into the `./inst/java/` folder of the
{kafkaesquejars} package. Both packages should be placed within the same
folder to make this work properly.

After Java compilation, both R packages have to be build and installed -
first {kafkaesquejars} than {kafkaesque}.

If developing Java in VScode - as I did here - pressing Ctrl-Shift-B
should allow to select the two most important tasks: resolving
dependencies (these go into {kafkaesqujars}) and compiling the Java code
and distributing it to the right places as described above.

# Vocabulary and Concepts

## Is it a Queue? Is it BigData? Is it Distributed Log? Yes and No: It is Kafka.

TBD

### Features and Promises

TBD

## Queues and Topics

TBD

## Conumers and Producers

TBD
