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

- [description: Introduction to Microsoft TPL Dataflow Library](#description-introduction-to-microsoft-tpl-dataflow-library)
- [Introduction](#introduction)
- [Contents](#contents)
- [How it Works](#how-it-works)
- [Block Types](#block-types)
  - [Processing Blocks](#processing-blocks)
    - [Transform Block](#transform-block)
  - [Transporting Blocks](#transporting-blocks)

## How it Works

TPL functions in a producer and consumer model where most blocks function as both (except for Action Blocks) since they are functioning as a pass through along with their other specific tasks.

> TPL functions in the  producer consumer model

Think of TPL as an assembly line with many different stations for processing, the product is constantly moving and only is interrupted for processing when a work must interact with it, same with TPL except the product is data and the worker is a block.

Another good analogy for TPL is building with Legos, each TPL block is like a Lego block and all you need to do is connect them together to accomplish your task.

**Well how does the data flow between blocks?**

When data is passed to the head block, processing is block until the block is marked as complete, which kicks off the processing, this same flow continues throughout each block until all data has been processed. In TPL this is called `CompletionPropagation` and this option signals that each block must ensure the previous block has been marked as complete before sending the data to the next block. However, there are a few exceptions to this rule when working with certain block types, we'll go more in depth later in this tutorial series.

For a more detailed explanation [here](https://channel9.msdn.com/Shows/Going+Deep/Stephen-Toub-Inside-TPL-Dataflow) is a video where one of the major architects of TPL goes over the inner workings of the library.

## Block Types

TPL has a variety of different block types, but we'll only go over the most commonly used blocks and for a full list and description checkout the documentation [here](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/dataflow-task-parallel-library#predefined-dataflow-block-types). Blocks can be split up into two different catagories, processing and transporting blocks with one exception the Action Block, which is more of a dead-end block since it only accepts input.

> Block I/O accepts Tuples

### Processing Blocks

#### Transform Block

{% highlight csharp %}

var block = new TransformBlock<int,int>();

{% endhighlight %}

### Transporting Blocks