---
layout: post
title:  "AWS: Add a new volume to instance, create partitions and mount it"
date:   2020-02-20 09:30:00 +0100
categories: aws
---

If You need to add a new volume to an AWS Instance and You happen to be using CloudFormation then this should do the trick.

This are the fragments of the CloudFormation template:

{% highlight bash %}
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      BlockDeviceMappings:
        - DeviceName: /dev/xvdb
          Ebs:
            VolumeSize: 30
            VolumeType: gp2
            Encrypted: true
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -x
          # --------------- 
          # Prepare filesystem and mount new volume
          # --------------- 
          sgdisk /dev/nvme1n1 --clear --typecode=8300 --new=0:0:0
          mkfs.xfs /dev/nvme1n1p1
          mkdir -p /mnt/new_volume
          mount /dev/nvme1n1p1 /mnt/new_volume
          NEW_VOLUME_UUID=`lsblk /dev/nvme1n1p1 -n -o UUID`
          echo "UUID=${NEW_VOLUME_UUID}     /mnt/new_volume     xfs    defaults,noatime  1   1" >> /etc/fstab
{% endhighlight %}
