---
layout: post
title: Leap birthdays
description: |
  A puzzle for BBC radio 4’s Today programme, broadcast February 28th 2019

categories:
- puzzles
- poetry
...

My father, my daughter and I all share a birthday — February 29th. I’m always _exactly_ half way between their ages. As of tomorrow, we'll all be a prime number of years old, and have each had a prime number of true birthdays. What year was I born?

<!--more-->

This was broadcast on February 28th 2019.
That is important for the solution.

Incidentally, my favourite February 29th event is [rare disease day](//www.rarediseaseday.org/article/what-is-rare-disease-day), as it suggests somebody has a sense of humour.

[_Puzzle #426 at the Today programme website._](//www.bbc.co.uk/programmes/articles/5CmHdB1K5rNy2XGwzppfqDN/puzzle-for-today)

# Construction

February 29th is a fascinating date, which no causes all sorts of horrible legal and computational crises.
And so it is prime material for a puzzle.

In order to construct the puzzle, I wrote a short haskell script to perform some calculations involving prime numbers.

## Preamble

Due to a stack bug [#4595](//github.com/commercialhaskell/stack/issues/4595) this may not work as a script unless the dependencies are built first (`stack --resolver=… build rio primes`).

```haskell
#!/usr/bin/env stack
{- stack
  --ghc-build tinfo6-nopie
  --resolver https://gist.github.com/dbaynard/9693107a883a6282f52f22785d0072e2/raw/edd32d61464cf0e19236081e250aebbe8cea1112
  --package primes
  exec ghci
  -}
-- |
-- Module      : leap-year
-- Description : Leap year calculation
-- Copyright   : David Baynard 2019
-- 
-- License     : BSD-3-Clause OR Apache-2.0
-- Maintainer  : David Baynard <puzzles@baynard.me>
-- Stability   : experimental
-- Portability : unknown

-- {-# OPTIONS_GHC -Wall #-}
{-# LANGUAGE NoImplicitPrelude         #-}
{-# LANGUAGE NoMonomorphismRestriction #-}
{-# LANGUAGE OverloadedStrings         #-}
{-# LANGUAGE PackageImports            #-}

module Main where
```

### Imports

A prime number generator, some data structures, and some tools for printing the output.

```haskell
import           "primes" Data.Numbers.Primes
import           "containers" Data.Set        (Set, filter, fromDistinctAscList,
                                               intersection, member)
import qualified "text" Data.Text             as T
import qualified "text" Data.Text.IO          as T
import           "rio" RIO                    hiding (filter)
```

## The action

This gives a list of birth years for which a person’s age and number of true birthdays (by March 1st 2019) would both be prime numbers.

```haskell
main :: IO ()
main = (T.putStrLn . displayLeapYear) `traverse_` leapYears
```

```haskell
-- | This is hacky as it is only valid for input lists of elements that are
-- strictly increasing in size. OK for this.
viable :: Integral int => [int] -> Set int
viable = fromDistinctAscList . takeWhile (< 125)

-- | Not correct for 1900, 1800, 1700, 1500 etc.. Thankfully, the puzzle rules
-- out those dates
yearsSinceLeapYear :: [Int]
yearsSinceLeapYear = [3,7..]

primesSinceLeapYear :: Set Int
primesSinceLeapYear = viable yearsSinceLeapYear `intersection` viable primes

leapYears :: Set Int
leapYears = filter f $ viable primes
  where
    f x = (4*x + 3) `member` primesSinceLeapYear

displayLeapYear :: Int -> Text
displayLeapYear n = let age = (4*n + 3) in T.unlines
  [ "Leap years: " <> tshow n
  , "Years: " <> tshow age
  , "Born: " <> tshow (2019 - age)
  ]
```

## The form of the puzzle

The script revealed there were more dates than I had anticipated.
Thankfully, one triple featured two gaps of the same duration; a suitable duration for a parent-child relationship.

# Solution

February 29th only occurs during a leap year. A year divisible by 4 but not 100, or divisible by 400, is a leap year.
For example, 2017, 2018 and 2019 are not leap years (not divisible by 4 and therefore not divisible by 400).
Leap years include 2016 and 2020 (divisible by 4 but not 100) and 1600 and 2000 (divisible by 400).
The years 1900 and 2100 are not leap years (divisible by 4 and 100, and not 400).

The age of a person, alive today, born on February 29th in a leap year since 1904, will be 3 more than a multiple of 4, as the last leap year was 3 years ago and they occur every 4 years.
This could be written

```
age = 4 × leap years + 3
```

This corresponds to 3, 7, 11, …, 115, and covers the [oldest ages on record in the world](http://supercentenarian-research-foundation.org/TableE.aspx).

Prime numbers have exactly two factors: 1 and themselves. 2, 3, 5, ….

The age must be in both lists, and the number of leap years in the list of primes.

The numbers in both lists are the following:

[ 3 , 7 , **11** , 19 , **23** , **31** , 43 , **47** , 59 , 67 , **71** , **79** , 83 , 103 , 107 ]

However, only the subset of ages in bold correspond to prime numbers of leap years.

| Leap years |   Age | Born |
|-----------:|------:|-----:|
|          2 |    11 | 2008 |
|          5 |    23 | 1996 |
|          7 |    31 | 1988 |
|         11 |    47 | 1972 |
|         17 |    71 | 1948 |
|         19 |    79 | 1940 |

Finally, the differences between the neighbouring pairs of ages must be the same.
The gaps are 12 (3), 8 (2), 16 (4), 24 (6) and 8 (2) years (leap years).
It is possible to find differences of 24 years (6 leap years) between 1996 and 1972, and 1972 and 1948.

Therefore the character setting the puzzle was born in 1972.

---

Possible ages for people born on February 29th
: [ 3 , 7 , 11 , 15 , 19 , 23 , 27 , 31 , 35 , 39 , 43 , 47 , 51 , 55 , 59 , 63 , 67 , 71 , 75 , 79 , 83 , 87 , 91 , 95 , 99 , 103 , 107 , 111 , 115 ]

Prime numbers
: [ 2 , 3 , 5 , 7 , 11 , 13 , 17 , 19 , 23 , 29 , 31 , 37 , 41 , 43 , 47 , 53 , 59 , 61 , 67 , 71 , 73 , 79 , 83 , 89 , 97 , 101 , 103 , 107 , 109 , 113 ]

As is conventional, the day of birth itself does not count as a birthday (well, unless you count a zeroth birthday). This assumption doesn't affect the working, though, but the outcomes will be off by four years.
