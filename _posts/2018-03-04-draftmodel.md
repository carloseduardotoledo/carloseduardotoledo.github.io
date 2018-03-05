---
layout: post
title: A Draft Model
---

“Dad, you are such a nerd!” That's what my 12yo daughter called the other night when I told that her math combinations and permutations homework was fun.  I'm a professional computer geek, but I don't see myself as nerd. But I’ll not waste the reader’s precious time discussing the differences between geeks and nerds. We rather discuss the differences between a Graph Databases and Graph Compute Engines, although not right now.
So what is this post about? You might be wondering. This post is about having fun programming in Python, in reality it what I was meant at first, but no having fun programming Python for now. As much as I want to jump to programming my Geo to Graph interface I'll have to give some thoughts to the Risk Assessment Graph Model before, the model gives me context and help clarify the whole picture. This post is about the graph model.

And fere is a first draft:

![_config.yml]({{ site.baseurl }}/images/Pipeline_GraphModel_Draft.png)

For the ones not initiated in the matter, Risk Assessment can be defined as the product of Probability of Failure (PoF) times the Consequence of Failure (CoF), 

```
Risk = PoF x CoF.
```

Probability of Failure (PoF) can be describe as a function of: failure mechanisms that exposes the pipeline, mitigation measures to reduce the threat level, and the intrinsic resistance of the pipe segment when a threat mechanism gets to it.

```
PoF = f(exposition, mitigation, resistance)
```

Consequence of Failure (CoF) can be described as a function of: the receptors (people, environment and property), the product is being transported by the pipeline, the area affected.  
```
CoF = f(receptors, product, area)
```

Reading the Graph model is quite straightforward, but I’ve created a little story to illustrate the model anyway.

Farmer Joe never wanted that damn pipeline to cross his lands, but it did anyway.  Now he have to worry of hitting the pipe when tilling the soil with a disc harrow, that he is still paying for, attached to his old tractor.  Farm activity like landslides or corrosion processes are seen as a Failure Mechanism threatening the pipeline. To keep this incident from happening the pipeline operator maintains an open channel with Joe, so he can ask the company what activities are safe inside the "right of way" (right to make a way over a piece of land, usually to and from another piece of land).  Communication, signalization and the depth of cover on a buried pipeline acts as mitigations to reduce the failure potential of a failure mechanism, it will reduce the chances of Joe hitting the pipe.  

Occasionally and unfortunately, failure mechanisms overpass mitigation measures and do strikes the pipeline.  When it happens, the amount of intrinsic resistance of the pipeline segment to resists the theat mechanism will be the only thing keeping the pipe to leak or explode, causing impact to receptors: people, environment and property. In our little model Mary’s house and the local schools are receptors.  

This story is built on relations as the Risk Assessment. 

```
Mitigation - Mitigate -> Failure mechanism

Failure mechanism - Threats -> Pipe Segment

Pipe Segment - Can harm -> Receptor

Pipe Segment - Connects to -> Pipe Segment

Pipe Segment - Connects to -> Valve
```

So, I think it makes senses that a Risk Assessment algorithm uses a Graph Database. The following text was extracted from Graph Databases by Ian Robinson, Jim Webber, and Emil Eifrem (O’Reilly):

>The previous examples (Relational and NOSQL Databases) have dealt with implicitly connected data. As users we infer semantic dependencies between entities, but the data models—and the databases themselves—are blind to these connections. To compensate, our applications must
create a network out of the flat, disconnected data at hand, and then deal with any slow queries and latent writes across denormalized stores that arise.
>
>What we really want is a cohesive picture of the whole, including the connections between elements. In contrast to the stores we’ve just looked at, in the graph world, connected data is stored as connected data. Where there are connections in the domain, there are connections in the data.

See you on our next post.  Probably discussing some Python coding, but I may bring some thoughts on the differences between Graph Databases and Graph Compute Engines, and why I’ve chosen the former, and why I’m not excluding the latter.

Best regards,

**Pipeline accidents are seriouly business, to the reader not use to this industry I suggest the reading of this list of accidents:  [List of Pipeline Accidents](https://en.wikipedia.org/wiki/List_of_pipeline_accidents)**

References:

* Graph Databases by Ian Robinson, Jim Webber, and Emil Eifrem (O’Reilly). Copyright 2015 Neo Technology, Inc., 978-1-491-93089-2.  You can freely download a copy of the book, compliments of Neo4j, at [here](https://neo4j.com/graph-databases-book/)

* Enhanced Pipeline Risk Assessment, Part 1 — Probability of Failure Assessments Revision 2.1 W. Kent Muhlbauer, PE [here](http://pipelinerisk.net/)

* Enhanced Pipeline Risk Assessment, Part 2 — Assessments of Pipeline Failure Consequences Rev 3 W. Kent Muhlbauer, PE [here](http://pipelinerisk.net/)

* Diagrams built using Draw.io [here](https://www.draw.io/)
