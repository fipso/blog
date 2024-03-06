---
title: Benchmarking vServer IOPS for Ethereum Nodes
description: On my journey to find a cheap and fast way to sync eth nodes I benchmarked some vServer providers
date: 2024-03-06T15:00:00+02:00
draft: true
tags: [linux, server, hosting, crypto]
---

So as I am trying to find the perfect hoster for ethereum nodes for MEV botting and also for my side hussel [blazed.sh](https://blazed.sh),
I played around with some of the vServer providers from my [other](/posts/server_provider/) post.  
  
First up for reference. This is what a real physical Ryzen 5 + Sabrent 2TB Rocket NVMe SSD system can achieve:
```
fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randrw
test: (g=0): rw=randrw, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=64
fio-3.33
Starting 1 process
Jobs: 1 (f=1): [m(1)][100.0%][r=52.2MiB/s,w=51.5MiB/s][r=13.4k,w=13.2k IOPS][eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=39153: Wed Mar  6 15:11:06 2024
  read: IOPS=80.9k, BW=316MiB/s (331MB/s)(2049MiB/6487msec)
   bw (  KiB/s): min= 3264, max=471456, per=97.10%, avg=314112.67, stdev=200805.16, samples=12
   iops        : min=  816, max=117864, avg=78528.00, stdev=50201.24, samples=12
  write: IOPS=80.8k, BW=316MiB/s (331MB/s)(2047MiB/6487msec); 0 zone resets
   bw (  KiB/s): min= 3288, max=465640, per=97.09%, avg=313674.00, stdev=200608.09, samples=12
   iops        : min=  822, max=116410, avg=78418.50, stdev=50152.02, samples=12
  cpu          : usr=11.58%, sys=51.57%, ctx=7943, majf=0, minf=8
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwts: total=524625,523951,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=316MiB/s (331MB/s), 316MiB/s-316MiB/s (331MB/s-331MB/s), io=2049MiB (2149MB), run=6487-6487msec
  WRITE: bw=316MiB/s (331MB/s), 316MiB/s-316MiB/s (331MB/s-331MB/s), io=2047MiB (2146MB), run=6487-6487msec

Disk stats (read/write):
    dm-1: ios=519200/522905, merge=0/0, ticks=76004/2250620, in_queue=2326624, util=97.49%, aggrios=529518/533413, aggrmerge=0/0, aggrticks=77184/2251124, aggrin_queue=2328308, aggrutil=96.83%
    dm-0: ios=529518/533413, merge=0/0, ticks=77184/2251124, in_queue=2328308, util=96.83%, aggrios=529518/533363, aggrmerge=0/50, aggrticks=72242/306401, aggrin_queue=378817, aggrutil=96.83%
  nvme0n1: ios=529518/533363, merge=0/50, ticks=72242/306401, in_queue=378817, util=96.83%
```
Also its worth noting that this test system is LUKS encrypted so expect even better IOPS from normal unencrypted setups.

**vServer Benchmarks**


