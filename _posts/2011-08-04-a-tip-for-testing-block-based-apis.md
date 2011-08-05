---
kind: article
layout: post
title: "A Tip for Testing Block-based APIs"
comments: true
---

Testing asynchronous, block-based APIs can be hard. You can put the usual
assertions in the completion block, but there is no guarantee that they will
execute before the main process ends.

To deal with this, I came up with this little macro that runs the current run
loop until some condition is true.

<script src="https://gist.github.com/1124312.js"> </script>

To wait for a block to execute, you can just add a simple boolean flag:

<script src="https://gist.github.com/1126975.js"> </script>

Or you can the macro to test an existing condition:

<script src="https://gist.github.com/1126958.js"> </script>

I've used this little macro in a few projects successfully, so I thought I'd
pass it along.
