# Core.Async Storage [![Build Status](https://travis-ci.org/glittershark/core-async-storage.svg?branch=master)](https://travis-ci.org/glittershark/core-async-storage)

A Clojurescript wrapper around react-native's [AsyncStorage][] using
[core.async][]

[AsyncStorage]: https://facebook.github.io/react-native/docs/asyncstorage.html#content
[core.async]: https://github.com/clojure/core.async

## Installation

[![Clojars Project](https://img.shields.io/clojars/v/core-async-storage.svg)](https://clojars.org/core-async-storage)

## Usage

In general, all exposed functions are `kebab-case` versions of AsyncStorage's
`camelCase` functions, that return a `core.async` channel instead of taking a
callback as their last argument, and also convert all input values to EDN and
read all return values as Clojure data structures

```clojure
(ns my-ns.core
 (:require [glittershark.core-async-storage :refer [get-item set-item]]
           [cljs.core.async :refer [<!]])
 (:require-macros [cljs.core.async.macros :refer [go]]))

(go
  (<! (set-item :foo {:bar "baz"})) ;; => [nil], or [error]
  (println (<! (get-item :foo))))   ;; => [nil {:bar "baz"}], or [error nil]

(let [a-map {:a "foo" :b "bar"}
      a-vec [[:a "foo"][:b "bar"]]]
  (go
    ;; multi-set can accept a map or a collection of vectors
    (println (<! (multi-set a-map)))
    (println (<! (multi-set a-vec))))
```

Result channels use core.async's [promise-chan][], so you can read from them as
often as you like and they'll always yield the same value

```clojure
(go
  (let [result (get-item :foo)]
    (println (<! result))   ;; => [nil {:bar "baz"}]
    (println (<! result)))) ;; => [nil {:bar "baz"}]
```

[promise-chan]: https://clojure.github.io/core.async/#clojure.core.async/promise-chan

## TODO

- [x] AsyncStorage.getItem
- [x] AsyncStorage.setItem
- [x] AsyncStorage.removeItem
- [ ] AsyncStorage.mergeItem
- [x] AsyncStorage.clear
- [ ] AsyncStorage.getAllKeys
- [ ] AsyncStorage.flushGetRequests
- [x] AsyncStorage.multiGet
- [x] AsyncStorage.multiSet
- [ ] AsyncStorage.multiRemove
- [ ] AsyncStorage.multiMerge

## License

Copyright © 2017 Griffin Smith

Distributed under the MIT License.
