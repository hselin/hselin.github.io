---
layout: post
title: "Keep Calm and Break Rules"
date: 2015-10-06 23:24:12 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <b>Inefficient and incompatible rules</b>
  <br />
  <del>It is a truth universally acknowledged that</del> Some of the Host Managed SMR <a href="/blog/2015/09/29/keep-calm-and-follow-the-rules/">rules</a> are not exactly conducive to efficient I/O. Even worse, they are incompatible with current host side implementations. For example, a fresh drive with no written data will FAIL ALL read commands sent by the host as ZAC/ZBC does not allow host to read unwritten LBAs. However, this behavior impedes BIOS/OS' attempt to read partition table/disk signature during system initialization - resulting in either boot failure or long boot time (waiting for retries and timeouts).
  <br />
  <br />
  <b>Feedback and assumptions</b>
  <br />
  These situations occur when <del>storage vendors</del> we attempt to define protocols and heuristics without adequate and timely host side validation. By the time host side feedbacks are considered - firmware and hardware implementations have already ossified. Furthermore, sometimes storage standard authors <del>arrogantly</del> incorrectly assume that as long as a functionality is defined (in a standard), then operating systems should have already implemented support for it (e.g. ATA sense data reporting, SCT WRITE SAME). When reality hits assumption - we are left with inefficient and incompatible implementations.  
  <br />
  <br />
  <img class="" src="/images/posts/keep_calm_and_break_rules/keep_calm_and_break_rules.png" style="width: 30%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <b>Rules are meant to be broken</b>
  <br />
  Based on observations working with Host Managed SMR devices and conversations with fellow developers, here are some rules that we should consider breaking.
  <br />
  <br />
  <b>1. Allow read beyond zone Write Pointer, returning zeros or host specified data pattern for unwritten LBAs (similar to reading unmapped/trimmed sectors).</b>
  <br />
  <br />
  <img class="" src="/images/posts/keep_calm_and_break_rules/read_beyond_write_pointer.png" style="width: 45%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  This will allow current system initialization procedures to function without error and simplify host side implementation.
  <br />
  <br />
  <hr />
  <br />
  <b>2. Allow read/write operations to span zones.</b>
  <br />
  <br />
  <img class="" src="/images/posts/keep_calm_and_break_rules/read_and_write_spanning_zones.png" style="width: 80%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  This will eliminate the need to split I/Os along zone boundaries, thus increasing I/O efficiency and simplifying host side implementation (especially when there are <a href="https://lwn.net/Articles/637035/">multiple</a> zone sizes).
  <br />
  <br />
  <hr />
  <br />
  <b>3. Allow write commands addressed to zone starting LBA to implicitly reset zone Write Pointer.</b>
  <br />
  <br />
  <div style="text-align:center;">State of zone as write command is issued</div>
  <img class="" src="/images/posts/keep_calm_and_break_rules/implicit_zone_reset.png" style="width: 45%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <div style="text-align:center;">State of zone after write command is processed</div>
  <img class="" src="/images/posts/keep_calm_and_break_rules/implicit_zone_reset_result.png" style="width: 45%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  This is a potentially dangerous proposition - as a stray write could accidentally reset a zone and delete all its contents. However, this proposal will eliminate the need to send and wait for the completion of an extra reset Write Pointer command in the I/O path. Moreover, RESET WRITE POINTER EXT as defined currently in ZAC is a non-queued command, which cannot be mixed with NCQ commands (e.g. common read/write commands) without performance penalties.
  <br />
  <br />
  <hr />
  <br />
  <b>4. Allow write commands to start beyond zone Write Pointer, filling gap (unwritten LBAs) with zeros or host defined data pattern.</b>
  <br />
  <br />
  <div style="text-align:center;">State of zone as write command is issued</div>
  <img class="" src="/images/posts/keep_calm_and_break_rules/implicit_write_same.png" style="width: 45%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <div style="text-align:center;">State of zone after write command is processed</div>
  <img class="" src="/images/posts/keep_calm_and_break_rules/implicit_write_same_result.png" style="width: 45%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  This will eliminate the need for host to send unnecessary write commands just to advance the Write Pointer (so it could write to a specific LBA), leading to better performance and simpler implementation.
  <br />
  <br />
  <b>Conclusion</b>
  <br />
  As mentioned <a href="/blog/2015/08/26/another-layer-of-indirection/">previously</a> - the benefits of having SMR must greatly outweigh the cost of its adoption. It is essential that storage vendors provide an easy and efficient transition path to SMR. Sometimes that means breaking some rules along the way.
</div>