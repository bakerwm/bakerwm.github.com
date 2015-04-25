---
layout: post
title: "using cufflinks"
description: ""
category: 
tags: []
---
{% include JB/setup %}


painful with Cufflinks installization

OS: linux x86_64 [CentOS 6.5]

Record installization process:


1 . Install Boost 1.57.0

Install `Boost 1.57.0` according to instructions [here](http://www.boost.org/doc/libs/1_57_0/more/getting_started/unix-variants.html)

  ```
  # download boost_1_57_0.tar.bz2 at: http://www.boost.org/users/history/version_1_57_0.html
  tar --bzip2 -xf boost_1_57_0.tar.bz2
  cd path/to/boost_1_57_0
  ./bootstrap.sh --prefix=mylocaldir
  ./b2 install
  ```

2. Install SAM tools







