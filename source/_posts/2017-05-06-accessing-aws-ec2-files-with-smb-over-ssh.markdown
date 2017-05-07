---
layout: post
title: "Accessing AWS EC2 Files With SMB Over SSH"
date: 2017-05-06 13:54:17 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <b>The problem</b><br />
  Recently I had to spin up a couple of Ubuntu EC2 instances for development work. However, I didn't want to give up the comfort of my familiar graphical editor on the laptop, nor install additional software on my local system.<br />
  <br />
  <b>The solution</b><br/>
  </br>
  <img class="" src="/images/posts/ec2_smb_ssh_tunnel/smb_over_ssh.png" style="width: 80%; height: auto; display: block; margin-left: auto; margin-right: auto">
  </br>
  <b>Setup EC2 instance</b><br />
  <u>Enable ssh port forwarding</u><br />
  Add the following lines to /etc/ssh/sshd_config<br />
  <div style="font-family: monospace"> 
    # Port forwarding<br /> 
    AllowTcpForwarding yes<br /> 
  </div>
  <br />
  <u>Restart sshd</u><br />
  <div style="font-family: monospace"> 
    sudo /etc/init.d/ssh restart<br />
  </div>
  <br />
  <u>Enable Samba</u><br />
  <div style="font-family: monospace">
    sudo apt-get install samba<br />
    sudo smbpasswd -a &lt;user name&gt;<br />
    sudo smbpasswd -e &lt;user name&gt;<br />
  </div>
  <br />
  <u>Make a backup of the Samba config file</u><br />
  <div style="font-family: monospace">
    sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bk<br />
  </div>
  <br />
  <u>Create a Samba share</u><br />
  Add the following lines to /etc/samba/smb.conf<br />
  <div style="font-family: monospace">
    [share]<br />
    path = &lt;path to share directory&gt;<br />
    valid users = &lt;user name&gt;<br />
    read only = no<br />
  </div>
  <br />
  <u>Restart smb and test the config file</u><br />
  <div style="font-family: monospace">
    sudo service smbd restart<br />
    testparm<br />
  </div>
  <br />
  <b>Setup local system</b><br />
  <u>Start ssh port forwarding</u><br />
  <div style="font-family: monospace">
    sudo ssh -L 8080:localhost:445 -i ~/.ssh/id_rsa &lt;username&gt;@&lt;EC2 instance IP&gt;<br />
  </div>
  <br />
  <u>We can now connect to EC2's SMB share using</u><br />
  <div style="font-family: monospace">
    smb://127.0.0.1:8080 (MacOS) or \\\127.0.0.1:8000 (Windows)<br />
  </div>
  <br />
  <b>Notes</b><br />
  <ul style="margin-top: 0px;">
  <li>Instead of tunneling SMB over SSH, we could have installed sshfs (requires FUSE)
  <li>I blame <a href="https://www.cs.cornell.edu/">Cornell CS</a> for my unhealthy addiction to Visual Studio but that's another story for another time...<br/>
  <img class="" src="/images/posts/ec2_smb_ssh_tunnel/meme.jpg" style="width: 40%; height: auto; display: block;">
  </ul>
</div>