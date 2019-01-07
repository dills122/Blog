---
layout: post
title: TPL Dataflow Library Introduction
categories: [general, release]
tags: [release, dills122]
description: Introduction to Microsoft TPL Dataflow Library
---

## Introduction

Processing data concurrently is hard especially when data integrity is key and lets be honest when isn't that a requirement in the enterprise world. Good news for all .NET fan boys out there, the .NET ecosystem has a library called TPL Dataflow for just this type of workflow. TPL gains its robustness from its design in the form of "pipelines" by connecting different types of processing or transporting block in a flow. This design allows the underlying library to handle the overhead of managing threads and processing order. Throughout this introduction we will discuss the fundamentals of TPL and gives some example use cases.

## Contents

- [Introduction](#introduction)
- [Contents](#contents)
- [How it Works](#how-it-works)
- [Block Types](#block-types)
  - [Processing Blocks](#processing-blocks)
    - [Action Block](#action-block)
    - [Transform Block](#transform-block)
    - [Transform Many Block](#transform-many-block)
  - [Transporting Blocks](#transporting-blocks)
    - [Buffer Block](#buffer-block)
    - [Broadcast Block](#broadcast-block)
- [Connecting Blocks](#connecting-blocks)

## How it Works

TPL functions in a producer and consumer model where most blocks function as both (except for Action Blocks) since they are functioning as a pass through along with their other specific tasks.

> TPL functions in the producer consumer model

Think of TPL as an assembly line with many different stations for processing, the product is constantly moving and only is interrupted for processing when a work must interact with it, same with TPL except the product is data and the worker is a block.

Another good analogy for TPL is building with Legos, each TPL block is like a Lego block and all you need to do is connect them together to accomplish your task.

**Well how does the data flow between blocks?**

When data is passed to the head block, processing is block until the block is marked as complete, which kicks off the processing, this same flow continues throughout each block until all data has been processed. In TPL this is called `CompletionPropagation` and this option signals that each block must ensure the previous block has been marked as complete before sending the data to the next block. However, there are a few exceptions to this rule when working with certain block types, we'll go more in depth later in this tutorial series.

For a more detailed explanation [here](https://channel9.msdn.com/Shows/Going+Deep/Stephen-Toub-Inside-TPL-Dataflow) is a video where one of the major architects of TPL goes over the inner workings of the library.

## Block Types

TPL has a variety of different block types, but we'll only go over the most commonly used blocks and for a full list and description checkout the documentation [here](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/dataflow-task-parallel-library#predefined-dataflow-block-types). Blocks can be split up into two different catagories, processing and transporting blocks with one exception the Action Block, which is more of a dead-end block since it only accepts input.

> Block I/O accepts Tuples

### Processing Blocks

Processing blocks are used for the processing and/or transformations of data. 

#### Action Block

An Action block is used as a fire forget processor block, since it only takes input. Because of this Action blocks are used often as the end of the pipeline.

#### Transform Block

{% highlight csharp %}

var block = new TransformBlock<int,int>();

{% endhighlight %}

#### Transform Many Block

The Transform many block is similar to the Transform block, the only exception being the many block will always return an IEnumerable of the defined return type.

{% highlight csharp %}

var block = new TransformManyBlock<int, int>();

{% endhighlight %}

### Transporting Blocks

Transportation blocks are used to help move and arrange data to the correct block.

#### Buffer Block

The Buffer block is just as it sounds a place to buffer data until it is ready to be processed.

{% highlight csharp %}

var block = new BufferBlock<int>();

{% endhighlight %}

#### Broadcast Block

The Broadcast block is used for passing the same message to up to three different blocks.

{% highlight csharp %}

var block = new BroadcastBlock<int>();

{% endhighlight %}


## Building a Pipeline

Now that we have discussed the fundamental parts of TPL now lets put it to use and build a pipeline. Building a pipeline is as simple as connecting the existing blocks together in a flow and ensuring a start and end point.

> Note if an Action Block is not at the end, the flow will NOT complete!


This is an example of linking the blocks together into a complete flow.

{% highlight csharp %}

var buffer = new BufferBlock<int>();

var multiplyTransform = new TransformBlock<int, int>();

var printBlock = new ActionBlock<int>();

var option = new DataflowLinkOptions() { PropagateCompletion = true }

buffer.LinkTo(multiplyTransform, option);
multiplyTransform.LinkTo(printBlock, option);

{% endhighlight %}

So as you can see from the example above to build a pipeline you simply just need to link each block together into the desired flow order and direction.

