![Loom logo](https://raw.github.com/aysylu/loom/master/doc/loom_logo.png "Loom")

**Caveat coder**: this lib is alpha-stage. The API may change in future versions.

This library is based off of Justin Kramer's Loom library.

Contact: email [robertlachlan@gmail.com](mailto:robertlachlan@gmail.com), [aysylu7 [at] gmail [dot] com](mailto:aysylu7@gmail.com), twitter: [aysylu22](http://twitter.com/aysylu22)

## Usage

### Leiningen/Clojars [group-id/name version]

    [haeffalump/loom "0.3.0-SNAPSHOT"]

### Namespaces

    loom.graph - records & constructors
    loom.alg   - algorithms (see also loom.alg-generic)
    loom.gen   - graph generators
    loom.attr  - graph attributes
    loom.label - graph labels
    loom.io    - read, write, and view graphs in external formats

### Basics

Create a graph:

    ;; Initialize with any of: edges, adacency lists, nodes, other graphs
    (def g (graph [1 2] [2 3] {3 [4] 5 [6 7]} 7 8 9))
    (def dg (digraph g))
    (def wg (weighted-graph {:a {:b 10 :c 20} :c {:d 30} :e {:b 5 :d 5}}))
    (def wdg (weighted-digraph [:a :b 10] [:a :c 20] [:c :d 30] [:d :b 10]))
    (def rwg (gen-rand (weighted-graph) 10 20 :max-weight 100))
    (def fg (fly-graph :neighbors range :weight (constantly 77)))

If you have [GraphViz](http://www.graphviz.org) installed, and its binaries are in the path, you can view graphs with <code>loom.io/view</code>:

    (view wdg) ;opens image in default image viewer
    
Inspect:

    (nodes g)
    => #{1 2 3 4 5 6 7 8 9}
    
    (edges wdg)
    => ([:a :c] [:a :b] [:c :d] [:d :b])
    
    (neighbors g 3)
    => #{2 4}
    
    (incoming wdg :b)
    => #{:a :d}
    
    (degree g 3)
    => 2
    
    (in-degree wdg :b)
    => 2
    
    (weight wg :a :c)
    => 20
    
    (map (juxt graph? directed? weighted?) [g wdg])
    => ([true false false] [true true true])
    
Add/remove items (graphs are immutable, of course, so these return new graphs):

    (add-nodes g "foobar" {:name "baz"} [1 2 3])
    
    (add-edges g [10 11] ["foobar" {:name "baz"}])
    
    (add-edges wg [:e :f 40] [:f :g 50]) ;weighted edges
    
    (remove-nodes g 1 2 3)

    (remove-edges g [1 2] [2 3])
    
    (subgraph g [5 6 7])

Traverse a graph:

    (bf-traverse g) ;lazy
    => (9 8 5 6 7 1 2 3 4)
    
    (bf-traverse g 1)
    => (1 2 3 4)
    
    (pre-traverse wdg) ;lazy
    => (:a :b :c :d)
    
    (post-traverse wdg) ;not lazy
    => (:b :d :c :a)
    
    (topsort wdg)
    => (:a :c :d :b)

Pathfinding:

    (bf-path g 1 4)
    => (1 2 3 4)
    
    (bf-path-bi g 1 4) ;bidirectional, parallel
    => (1 2 3 4)
    
    (dijkstra-path wg :a :d)
    => (:a :b :e :d)
    
    (dijkstra-path-dist wg :a :d)
    => [(:a :b :e :d) 20]

Other stuff:

    (connected-components g)
    => [[1 2 3 4] [5 6 7] [8] [9]]

    (bf-span wg :a)
    => {:c [:d], :b [:e], :a [:b :c]}

    (pre-span wg :a)
    => {:a [:b], :b [:e], :e [:d], :d [:c]}
    
    (dijkstra-span wg :a)
    => {:a {:b 10, :c 20}, :b {:e 15}, :e {:d 20}}

TODO: link to autodocs

## Dependencies

Nothing but Clojure. There is optional support for visualization via [GrapViz](http://graphviz.org).

## TODO

* Use deftype instead of defrecord
* Do more functional graph research
* Solidify basic API, guarantees
* Implement more algorithms
* Test & profile more with big, varied graphs
* Multigraphs, hypergraphs, adjacency matrix-based graphs?

## License

Copyright (C) 2010 Justin Kramer jkkramer@gmail.com

Distributed under the [Eclipse Public License](http://opensource.org/licenses/eclipse-1.0.php), the same as Clojure.
