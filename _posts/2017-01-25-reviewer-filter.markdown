---
layout: post
title:  "GitHubのreviewerをfilterする"
date:   2017-01-25 19:00:00 +0900
categories: GitHub
---
最近追加されたGitHubのReviewer機能。

いままではラベルを作ったり、タイトルに`[WIP]`をつけたりして運用でカバーと言う感じだったけど、こっちの運用に寄せていっているんじゃないかと思う。

先日、GitHubが[reviwerのフィルタリング機能](https://github.com/blog/2306-filter-pull-request-reviews-and-review-requests)をリリースした。

`is:pr is:open review:required review-requested:motokiee`でフィルタすれば、自分がreviewerに割り当てられているPRがすぐに分かる。

便利。