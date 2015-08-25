---
layout: post
title: "Host Managed SMR Simulator"
date: 2015-08-23 11:37:24 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <b>Introduction</b>
  <br />
  <a href="https://en.wikipedia.org/wiki/Shingled_magnetic_recording">Shingle Magnetic Recording</a> is a disruptive technology that will enable areal density growth for the hard drive industry by partially overlapping tracks. Shingling requires physical writes to be sequential, which opens the question of how to address this behavior at a system level. Two general approaches contemplated are to either handle block management in the device (Device Managed SMR) or in host software (Host Managed SMR) via Zone ATA or Zoned Block command sets (ZAC/ZBC).
  <br />
  <br />
  <b>Trade-offs between Host Managed and Device Managed solutions</b>
  <br />
  There are trade-offs to each approach. For example, Device Managed SMR drives are backwards compatible with existing file systems and BIOS' but may introduce unpredictable or uncontrollable performance. While Host Managed SMR drives provide and suffer the opposite.
  <br />
  <br />
  <b>Host Managed SMR protocol constraints</b>
  <br />
  ZBC and ZAC protocols restrict what IOs could be sent to the device and this in turn simplifies HW/FW behavior and implementation. Major differences/restrictions include:
  <br/>
  <ul style="margin-top: 0px;">
    <li>New device type (may not work with existing OS/HBA/BIOS)</li>
    <li>Sequential write - writes have to start at zone Write Pointer</li>
    <li>Writes have to be 4k aligned in SMR zones</li>
    <li>Reads cannot start or extend beyond the zone Write Pointer</li>
    <li>Read/Write commands cannot span zones (some exceptions may apply)</li>
  </ul>
  <b>Host Managed SMR device simulator</b>
  <br />
  This week, WD is releasing a SMR simulator to facilitate host-side software/file system development.
  SMR-Simulator is a simple tool that captures host software behavior and determines its “friendliness” to SMR technology. We hope SMR-Simulator will enable open source developers to experiment and become familiar with SMR functionalities and behaviors without the need to access real SMR (ZBC/ZAC) HW.
  <br />
  <br />
  <b>Web site</b>
  <br />
  <a href="https://github.com/westerndigitalcorporation/SMR-Simulator">https://github.com/westerndigitalcorporation/SMR-Simulator</a>
  <br />
  <b>Behavior expectations</b>
  <br />
  <a href="https://github.com/westerndigitalcorporation/SMR-Simulator/wiki/SMR-Simulator-Behavior-Expectations">https://github.com/westerndigitalcorporation/SMR-Simulator/wiki/SMR-Simulator-Behavior-Expectations</a>
  <br />
  <br />
  <img class="float-left" src="/images/posts/magnetic_pole.png" style="width: 300px; height: auto;">
</div>