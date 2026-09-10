(ns yui.coscientist-test
  "Charter-gate and tournament tests for the yui co-scientist. Deterministic.
   Run: nbb --classpath src:test test/yui/coscientist_test.cljs
   (nbb has no --platform flag; passing one makes it try to open it as a file.)"
  (:require [clojure.test :refer [deftest is run-tests]]
            [yui.coscientist :as cs]))

(deftest generate-one-per-catalog
  (is (= (count cs/catalog) (count (cs/generate cs/catalog)))))

(deftest forbidden-mechanism-is-vetoed
  (let [bad {:id :bad :mechanism "engagement-maximizing"
             :param :conv-visit-signup :datum "d" :prediction "p"}]
    (is (false? (:ok (cs/review bad))))
    (is (some #(= :G-mechanism (:gate %)) (:problems (cs/review bad))))))

(deftest parameterless-non-measurement-candidate-is-vetoed
  (let [bad {:id :bad2 :mechanism "retention" :param nil
             :datum "d" :prediction "p"}]
    (is (false? (:ok (cs/review bad))))
    (is (some #(= :G-measured (:gate %)) (:problems (cs/review bad))))))

(deftest predictionless-candidate-is-vetoed
  (let [bad {:id :bad3 :mechanism "onboarding-fix" :param :x
             :datum "d" :prediction nil}]
    (is (false? (:ok (cs/review bad))))
    (is (some #(= :G-falsifiable (:gate %)) (:problems (cs/review bad))))))

(deftest aligned-catalog-survives
  (is (every? :ok (map cs/review (cs/generate cs/catalog)))))

(deftest rank-is-deterministic-and-gain-ordered
  (let [hyps (cs/generate cs/catalog)
        cands (cs/surviving hyps)
        gains {"yui-h1-self-serve-onboarding-per-door" 1169.6
               "yui-h2-contributor-starter-kits" 254.2
               "yui-h3-active-retention-windows" 230.4
               "yui-h5-reputation-public-ledger" 74.3}
        r1 (cs/rank cands gains)
        r2 (cs/rank cands gains)]
    (is (= r1 r2))
    ;; the biggest measured sim gain (onboarding fix) must rank first
    (is (= "yui-h1-self-serve-onboarding-per-door" (first r1)))))

(deftest evolve-combines-top2
  (let [hyps (cs/generate cs/catalog)
        cands (cs/surviving hyps)
        ranked (cs/rank cands {"yui-h1-self-serve-onboarding-per-door" 1169.6})
        e (cs/evolve ranked cands)]
    (is (some? e))
    (is (= 2 (count (:mechanisms e))))))

(defn -main []
  (let [{:keys [fail error]} (run-tests)]
    (when (or (pos? fail) (pos? error))
      (.exit js/process 1))))

(-main)
