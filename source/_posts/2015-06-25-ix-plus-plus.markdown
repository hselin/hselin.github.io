---
layout: post
title: "IX++"
date: 2015-06-25 16:21:16 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <a href="http://cs344g.stanford.edu">CS344G: (Your) Great Ideas for Networked Applications</a> class project. 
  <br />
  <br />
  <b>Introduction</b>
  <br />
  IX++ is an extension of the IX Dataplane Operating System that uses PCI Single Root I/O Virtualization (SR-IOV) to run multiple networked applications concurrently with minimal interference. The original IX ran alongside Linux and required a dedicated physical network interface card (NIC) for each multi-threaded IX application, as well as one for Linux. In IX++, each multi-threaded application is instead given its own virtual NIC through SR-IOV, and Linux can run concurrently on the physical NIC. In brief, the original IX required a dedicated NIC for Linux and one NIC per IX application while IX++ uses NIC virtualization to allow Linux and multiple IX applications to multiplex a single physical NIC. In this paper we describe the implementation of IX++ and demonstrate its significant performance advantages over Linux when running concurrent networked applications. We also show that using different Linux Traffic Control Queuing Disciplines can help, but not close, this performance advantage.
  <br />
  <br />
  <a href="/resources/ix++_final_presentation.pdf">Project presentation</a>
  <br />
  <a href="/resources/CS344g_ixplusplus_final paper.pdf">Read project report here</a>
  <br />
  <br />
  <b>Latency vs Throughput</b>
  <img src="/images/posts/ix++_latency_chart.png" style="width: 100%; height: auto;">
</div>