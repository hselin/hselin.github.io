---
layout: post
title: "Keep Calm and Follow The Rules"
date: 2015-09-29 16:29:25 -0700
comments: true
categories: 
---
<div style="overflow:auto">
  <b>The problem with Host Managed SMR</b>
  <br />
  Host Managed SMR devices must be accessed using either Zoned Access (ATA) or Zoned Block (SCSI) command sets which restrict I/O operations that could be sent from the host. This results in simplified device implementation and behavior - as we shifted the burden of shingled writing to host software. <del>Hopefully</del> It is expected, that the host will have access to more compute resources (e.g. memory) and semantic/system-level information than a low-level storage device.
  <br />
  <br />
  <img class="" src="/images/posts/keep_calm_and_follow_the_rules/keep_calm_and_follow_the_rules.png" style="width: 30%; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <b>Restrictions</b>
  <br />
  Below is a list of major I/O restrictions that ZAC and ZBC enforces on the host.
  <br />
  <span style="background: yellow;">Nonconforming I/O operations will be failed by the device.</span>
  <br />
  <br />
  <li>Sequential write - writes have to start at zone Write Pointer (WP)</li>
  <li>Zone WPs could be reset (to the zone's starting LBA) by issuing a RESET WRITE POINTER command</li>
  <li>Writes have to be 4K aligned in SMR zones</li>
  <li>Reads cannot start or extend beyond the zone WP</li>
  <li>Read/Write commands cannot span zones (some exceptions may apply)</li>
  <br />
  Zone WPs are kept and maintained by the device, they are used to keep host write sequential so we don't accidentally wipe-out already written data. After each successful write, the associated zone WP is advanced to the next "unwritten" location within the zone (i.e. largest LBA written + 1).
  <br /> 
  <br />
  Here are some concrete examples of the above I/O rules.
  <br />
  <br />
  <img class="" src="/images/posts/keep_calm_and_follow_the_rules/smr_rules.png" style="width: 100%; height: auto; display: block; margin-left: auto; margin-right: auto">  
  <br />
  1. <span style="color: red;">Not allowed - write commands cannot start before zone WP.</span>
  <br />
  2. <span style="color: green;">Allowed - write commands must start at zone WP.</span>
  <br />
  3. <span style="color: green;">Allowed - write commands must start and end in the same zone.</span>
  <br />
  4. <span style="color: red;">Not allowed - write commands cannot spans multiple zones.</span>
  <br />
  5. <span style="color: red;">Not allowed - write commands cannot start after zone WP.</span>
  <br />
  6. <span style="color: green;">Allowed - read commands must start and end before zone WP.</span>
  <br />
  7. <span style="color: green;">Allowed - read commands can span up to LBA (zone WP - 1).</span>
  <br />
  8. <span style="color: red;">Not allowed - read commands cannot span multiple zones.</span>
  <br />
  9. <span style="color: red;">Not allowed - read commands cannot start on zone WP.</span>
  <br />
  10. <span style="color: red;">Not allowed - read commands cannot start after zone WP.</span>
  <br />
  <br />
  In the next <a href="/blog/2015/10/06/keep-calm-and-break-rules/">post</a> I'll talk about why <span style="background: yellow;">we should break some of these rules for efficiency.</span>
</div>