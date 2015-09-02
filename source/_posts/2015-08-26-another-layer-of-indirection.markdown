---
layout: post
title: "Another Layer Of Indirection"
date: 2015-08-26 23:58:11 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <b>Introduction</b>
  <br />
  "Any problem in computer science can be solved by another layer of <a href="https://en.wikipedia.org/wiki/Indirection">indirection</a>". Shingle Magnetic Recording (SMR) is no different - the only “difficulty” is in determining where to add the layer of indirection to enable maximum flexibility and efficiency. Basically, we want to insert a “SMR translation layer" that takes in random I/Os and outputs a sequential I/O stream. This layer could be implemented as a library, file system routine, driver, application or firmware - each with their own set of trade-offs. <span style="background-color:#FFFFCC;">In general, higher indirection/abstraction layers will have access to more semantic and system level information. This could lead to better performance and more room for innovation.</span> For example, the initiator application will have more knowledge about the importance and intent of an I/O than firmware on the target device.
  <br />
  <br />
  <img class="" src="/images/posts/smr_translation_layer.png" style="width: 350px; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <img class="" src="/images/posts/layers_of_information.png" style="width: 500px; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <b>Layers of SMR</b>
  <br />
  With these thoughts in mind, here are some approaches to enabling SMR.
  The orange boxes indicate where the “SMR translation layer" could be implemented for the various solutions. As one can see from the diagram below, where “SMR translation layer" could be inserted varies across the spectrum depending on the hardware and software deployed.
  <br />
  <br />
  <img class="" src="/images/posts/smr_stack.png" style="width: 500px; height: auto; display: block; margin-left: auto; margin-right: auto">
  <br />
  <br />
  <ul style="margin-top: 0px;">
    <li>
      <b>Enlightened user application</b>
      <br />
      Companies with system software engineering capabilities could develop or modify their applications to be SMR-aware and reap the benefits of increased capacity immediately without (much) ecosystem support. SMR awareness at this level enables great flexibility and efficiency as the application manages its own data allocation. However, it is not very scalable in terms of extending SMR support across all user applications. It also requires potential cooperation and co-development with SMR device manufacturers (e.g. error code propagation and interpretation).
    </li>
    <br />
    <li>
      <b>SMR software library</b>
      <br />
      Companies with moderate level of software engineering capabilities could develop or modify their applications to utilize libraries (e.g. <a href="https://github.com/hgst/libzbc">libzbc</a>) that provide SMR support. However, this requires modification of all applications that use SMR and ecosystem support (library development). It also introduces the complexity of dealing with library API changes.
    </li>
    <br />
    <li>
      <b>SMR-friendly file system</b>
      <br />
      Building a SMR-friendly file system is an attractive solution as it allows most applications to work with SMR devices without modification. The file system hides the complexity of SMR behind the familiar POSIX/Unix interface of open/close/read/write. At WD, we have <a href="/projects">modified</a> New Implementation of a Log-structured File System (NILFS) and Linear Tape File System (LTFS) to work with single and multi-zoned Host Managed SMR drives. However, file systems are notoriously hard to debug and to become trustworthy. It is also hard to convince people and companies to switch to a new file system just to support SMR devices. The situation may be different if major existing file systems become SMR-aware (e.g. <a href="https://github.com/Seagate/SMR_FS-EXT4">ext4</a>, <a href="https://git.kernel.org/cgit/fs/xfs/xfs-documentation.git/tree/design/xfs-smr-structure.asciidoc">XFS</a>).
    </li>
    <br />
    <li>
      <b>SMR driver</b>
      <br />
      Going lower, we could build a SMR-aware block/filter driver. This allows file systems to work transparently with SMR devices and requires no application change. At WD, we are currently working on a <a href="https://en.wikipedia.org/wiki/Device_mapper">device mapper</a> target to support Host Managed SMR devices (more on this in later posts). However, SMR awareness at this level may not provide as much efficiency and flexibility as compared to some of the previous use models. Additionally, we may be constrained by functionalities and limitations of layers below (e.g. libATA, miniports).
    </li>
    <br />
    <li>
      <b>Device Managed SMR</b>
      <br />
      Finally, we could always punt the work of managing SMR behavior to the device. After all, device manufacturers are the storage experts and they "created" this problem in the first place! By implementing SMR handling at the lowest level, file systems and applications will work transparently with SMR devices - from the host perspective, a Device Managed SMR drive works exactly like a conventional <a href="https://en.wikipedia.org/wiki/Perpendicular_recording drive">PMR</a> drive. Have we found a panacea to our SMR problem? Maybe. It depends on our usage model and performance expectations. For example, just how "transparent" is this solution? Unlike traditional drives - the performance characteristics of a Device Managed SMR drive may change as we fill it to capacity (at some point read-modify-write will be required to change device contents). Can applications really transparently deal with this behavior? Furthermore, this "transparency" comes at a cost - in terms of performance, flexibility and scalability.
      <br />
      <br />
      <ul style="margin-top: 0px;">
        <li>
          Performance - at the device level, only limited amount of semantic information is available from the host. It is up to the device to interpret the meaning and importance of each read and write command. This limits the amount of optimization that could be implemented. For example, the device might kick off a garbage collection process while the host is busy flushing important data to disk; and an application can experience drastically different I/O completion times while executing the same operation.
        </li>
        <br />
        <li>
          Flexibility - since each vendor might implement SMR handling differently, device behavior and performance may differ by product line, manufacturer or even firmware version. For example, an I/O bound application might work as expected with a homogeneous drive population but exhibits unexpected behavior/bottlenecks when drive population is heterogeneous.
        </li>
        <br />
        <li>
          Scalability - the amount of <a href="https://en.wikipedia.org/wiki/Disk_buffer">memory</a> and processing power on each device is fixed at manufacturing time and specified by the storage vendor. This means the amount of resources available for SMR handling cannot be dynamically reallocated and scaled according to host software demand. This is important as storage devices are already (or are becoming increasingly) the bottleneck for many compute operations.
        </li>
      </ul>
    </li>
  </ul>
  Ultimately, there is no free lunch. Someone at some layer needs to pay the price for managing SMR. This can range from enlightened application down to enhanced firmware and through methods such as over-provisioning, usage of host resources, or <a href="https://en.wikipedia.org/wiki/Hybrid_drive">hybrid storage</a>. There is no one solution that fits all use cases - the additional layer of indirection for SMR is application and environment dependent. 
  <br />
  <br />
  <b>Conclusion</b>
  <br />
  Fundamentally, SMR is not a "feature" for the end-user (unless you really want a <a href="https://en.wikipedia.org/wiki/Write_once_read_many">WORM</a> drive), it is a capacity enabler for device manufacturers. No user is willing to pay extra just to have SMR. The benefits of having SMR must greatly outweigh the cost of its adoption for us to see significant espousal in developing and modifying software and hardware to be SMR-aware/friendly. As we've seen, SMR adoption is a complex problem. It cannot be solved by a single vendor or in a single layer; it requires the whole ecosystem to work together to build a comprehensive solution for all use cases.
</div>