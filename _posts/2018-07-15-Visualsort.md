---
title: "Visualsort"
date: 2018-07-15
excerpt: >-
  A visualizer of in-memory sorting algorithms.
comments: true
share: true
show_date: true
related: true
toc: true
toc_sticky: true
categories:
  - Projects
  - Programming
tags:
  - Go
  - Algorithms
  - Sorting
---

Visualsort borns as a program to exploit the capabilities of [Pixel](https://github.com/faiface/pixel) gaming library (and having fun with [Go](https://go.dev/)).

It's aim is to provide a visual representation of the most popular in-memory [sorting algorithms (Wikipedia)](https://en.wikipedia.org/wiki/Sorting_algorithm).

[<i class="fab fa-github" aria-hidden="true"></i>&nbsp;&nbsp;&nbsp;Source Code](https://github.com/MrBogomips/visualsort){: .btn .btn--primary .btn--small}

## Demo 

<div>
<iframe width="560" height="315" src="https://www.youtube.com/embed/VOQb-9w5WS8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

Demo video on [Youtube](https://youtu.be/VOQb-9w5WS8).

## Algorightms

- [x] **Quick Sort**: I effectively use the Go Lib implementation that should be a randomized version
- [x] **Bubble Sort**
- [x] **Bubble Sort 2**: fast end
- [x] **Insertion Sort**
- [x] **Selection Sort**
- [x] **Shell Sort**
- [ ] **Cocktail Sort**
- [ ] **Merge Sort**

## Usage

Visualsort is a command line utility.

Just run `visualsort` to get a visual representation of quicksort (one of the best algorithm (not the "best" in the sense it's not an "optimal" solution for the problem of "in-memory sorting") implemented in many system libraries.

You can customize speed, delay, algorithm, data set and many more options for the sake of your experiments.

### Command Options

For a complete list of options issue the interactive help: `visualsort -h`.

Some of them are treated here:

- _asc_: will use a data set ordered in ascending
- _desc_: will use a data set ordered in descending
- _delay_: will retard the the start of the game by an amount of seconds. Useful for video recording
- _seed_: set a random seed. Useful to compare algorithms with similar data sets
- _size_: the size of the data set
- _speed_: the speed of the animation

## Installation

It would suffice

```
 go get github.com/MrBogomips/visualsort
```

## Contribution

This project started for fun to explore Go and Pixel gaming library.
The code should deserve better organization and many more algos (notably **Mergesort**).

Neverthless… if you found another sorting algorithm to share…