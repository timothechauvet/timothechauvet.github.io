---
title: "The Modern Stack of Facebook.com 📚"
date: 2024-02-04
hero: facebook.webp
description: Facebook refuses to use traditional software because of its massive user base. Here's why they decided to build their own stack.
theme: Toha
tags: ["Translated", "Stack", "Facebook"]
---

[*Lire cet article en français*](https://timothechau.vet/posts/technique/facebook-stack)

I loved Facebook during my childhood. Today, people from my generation agree to call it the *worst social network*, still it remains the most popular social network in the world. With **2 billion active users daily**, Facebook is "not dead, nor dying" according to its director (March 2023).

Launched on February 4, 2004, by Mark Zuckerberg, or "the Zucc" as we love to call him, Facebook started as a PHP monolith coded outside his creator's class hours. As they couldn't anticipate the success of the social network, its code was readjusted, and the infrastructure modernized.

Today, Facebook.com celebrates its 20th anniversary, and I wanted to pay tribute to this great site of my era that I still decided to leave in 2022 at the end of my studies. Here's a summary of its technical stack, which Facebook discusses in its blog *[Engineering at Meta](https://engineering.fb.com/)*, and that I decided to condense for you in this small article that I really loved making.

</br>
</br>

# Homebrewed Frontend

If **ReactJS** is used by so many, it's because everyone wants to become Facebook. Facebook's teams launched in 2013 the *trendy* JS framework [despite its decline](https://insights.stackoverflow.com/trends?tags=reactjs%2Cvue.js%2Cangular%2Csvelte%2Cangularjs%2Cvuejs3). It's now the framework used by [the new Facebook website and apps](https://engineering.fb.com/2023/02/06/ios/facebook-ios-app-architecture/).

**GraphQL** is what they use for their API requests. Like React, GraphQL was developed in 2015 by Facebook and is now open-source. The problem with traditional APIs is that they return too much data compared to what is requested, a problem that GraphQL tackles.

Finally, [**Relay**, less popular](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/), manages data dynamically in React. The idea behind this framework is that data needs to be hierarchical and React components need specific data. Relay then queries GraphQL only for what's interesting, while also prioritizing content.


</br>
</br>

# Facebook.com's backend : from PHP to PHP (but better)

I remember a time when Facebook was regularly unavailable, and I couldn't post my mediocre jokes to my internet friends. I believe this problem is definitively solved today, and I understand why.

Despite the popularity of Kubernetes, Facebook chose to use [an in-house orchestrator, **Twine**](https://engineering.fb.com/2019/06/06/data-center-engineering/twine/), about which not much is known as it is closed source. It's the new name for Tupperware, who is managing Facebook's workloads and containers [since the past decade](https://engineering.fb.com/2020/11/11/data-center-engineering/twine-2/).

In terms of programming language, *Zucc* started with PHP. Then, in 2014, Facebook developed the open-sourced [**Hack language**](https://engineering.fb.com/2014/03/20/core/hack-a-new-programming-language-for-hhvm/), an object-oriented programming language that is based on and compiles PHP. Their servers run **HHVM** (HipHop Virtual Machine). Hack supports the entirety of Facebook's PHP codebase and resolves many security issues.



[I've heard](https://engineering.fb.com/2019/03/14/data-center-engineering/f16-minipack/) that **CentOS** was used for servers despite the distro being dead. They developed in parallel [**FBOSS**](https://engineering.fb.com/2015/03/10/data-center-engineering/facebook-open-switching-system-fboss-and-wedge-in-the-open/), an OS for their network switches.

</br>
</br>

# Homebrewed Hardware, too

I don't understand much about hardware, so I won't go into details, but Facebook has been developing [its own hardware for a long time](https://engineering.fb.com/category/data-center-engineering/). I remember in 2013 when they also tried to compete with phone brands with the HTC First, but [it was a partnership with HTC](https://www.businessinsider.com/htc-first-and-facebook-home-review-2013-4?r=US&IR=T).

</br>
</br>

# Sticking to Classic Databases

As expected, most large-scale databases use  **MySQL**. They recently [migrated to the 8.0 release](https://engineering.fb.com/2021/07/22/data-infrastructure/mysql/), which wasn't easy to do as it took multiple years.

Facebook.com also developed **Cassandra** in 2008 for their [Messenger product](https://engineering.fb.com/2010/11/15/core-infra/the-underlying-technology-of-messages/). They rebuilt it as [**Rocksandra**](https://instagram-engineering.com/open-sourcing-a-10x-reduction-in-apache-cassandra-tail-latency-d64f86b43589) 10 years later, and promises a 10x decrease in latency.

</br>
</br>

# A Massive Infrastructure for Big Data

Facebook rewrote a part of MySQL's code [to build the **MyRocks DB server**](https://engineering.fb.com/2016/08/31/core-infra/myrocks-a-space-and-write-optimized-mysql-database/), which optimizes storage space and writing times. [**RocksDB**](https://engineering.fb.com/2013/11/21/core-infra/under-the-hood-building-and-open-sourcing-rocksdb/), written in C++ and based on Google's LevelDB, serves as the database.

They are very proud of their [**Scribe** engine](https://engineering.fb.com/2019/10/07/data-infrastructure/scribe/), which is an in-house message queue manager.

**Apache Spark** is used by the Machine Learning teams [for training models](https://engineering.fb.com/2017/02/07/core-infra/using-apache-spark-for-large-scale-language-model-training/). They moved from Hive which was [apparently too slow](https://engineering.fb.com/2016/08/31/core-infra/apache-spark-scale-a-60-tb-production-use-case/) (I don't know enough to have an opinion on that).

Finally, **Presto**, [launched in 2012](https://engineering.fb.com/2013/11/06/core-infra/presto-interacting-with-petabytes-of-data-at-facebook/), aims to optimize SQL queries for large volumes of data (Hive, HBase, Scribe...).

</br>
</br>

# Unfamiliar Technologies for Automation and DevOps

If some of the previously mentioned services are unfamiliar to you, those that follow are even more obscure. At Facebook, we use the in-house [**Sapling** for Git](https://engineering.fb.com/2022/11/15/open-source/sapling-source-control-scalable/), which is a scalable Git server for large codebases. It took Facebook about a decade to develop the `sl` tool internally, and they open-sourced it in 2022.

I don't know what Facebook uses for its CI/CD integration and deployment pipelines, but [they developed **Buck2**](https://engineering.fb.com/2023/04/06/open-source/buck2-open-source-large-scale-build-system/), a system for building their massive codebase coded in Rust. 

For testing purposes, in-house [**Infer**](https://fbinfer.com/) is being used for mobile testing (C, C++, Objective-C, Java) and [**Sapienz**](https://engineering.fb.com/2018/05/02/developer-tools/sapienz-intelligent-automated-software-testing-at-scale/) for testing user stories.

**SLICK** enables [SLI monitoring](https://engineering.fb.com/2021/12/13/production-engineering/slick/) (SLI-CK, get it ?). It's useful for reaching 100% uptime. Unfortunately, not much information is available about this system, which remains closed source.

Finally, **Docusaurus** is their documentation CMS, featuring a rather adorable crocodile mascot. The result is very pretty, and [they open-sourced the project](https://docusaurus.io/).

# Conclusion and Surprises

I was surprised to see that the Facebook.com teams developed so many in-house technologies, and that most are open source. It's a good thing for the dev & ops communities and shows that the "least liked social network" still has a long way to go.

In 20 years, Facebook has made scalability a core concern and has successfully transitioned from its PHP monolith to a modern stack. They've been satisfying the needs of 2 billion daily users from scratch, all to their credit. What will be the next technical challenges for the social network is more exciting than ever.

---

"Facebook" banner generated by [DALL•E](https://labs.openai.com)