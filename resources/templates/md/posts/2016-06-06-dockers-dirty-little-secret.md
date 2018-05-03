{:title "Docker's dirty little secret"
 :layout :post
 :author "Jeff Terrell"
 :tags ["coding" "development" "docker"]
 :toc false}

Docker isn't what I thought it was.

I thought it provided a way to run docker images on any host that supported docker. As it turns out, that's not true.

I first encountered something fishy when I found <a href="https://hub.docker.com/r/auchida/freebsd/">this docker image</a> on Docker Hub, whose description said, "FreeBSD Docker host is required to run this image." I thought, "How is that even possible?" And sure enough, I could run the image just fine with a FreeBSD host. But when I tried to run it on my iMac, it silently failed. How could this be?

<!-more->

I posed the question to the <a href="https://lists.freebsd.org/mailman/listinfo/freebsd-virtualization">FreeBSD Virtualization mailing list</a>, and I got <a href="https://lists.freebsd.org/pipermail/freebsd-virtualization/2016-June/004495.html">an informative reply</a> from <a href="http://www.pix.net/staff/lidl/index.html">Kurt Lidl</a>. In a nutshell, the problem is that the system calls that the image running inside the docker container makes don't necessarily line up with what the docker host supports.

To explain that further, here's some background on how docker works, at a lower level. The interface between the docker image and the docker container is system calls: the same as the interface between processes and the kernel. If you've taken a course in operating systems [that featured a \*nix OS], you're probably familiar with `fork`, `exec`, `mmap`, or `select`, to name a few. The process accesses resources from the OS through system calls, and this is exactly how the docker container accesses resources from the docker host.

If you're on a Linux host and running a Linux docker image, you're effectively just running the docker image's process in a jail (or <a href="https://en.wikipedia.org/wiki/Cgroups">cgroup</a>) so that it's isolated from other processes. If you're on a Mac OS X host and running a Linux image, that works too because you're actually running the image process inside the "<a href="https://docs.docker.com/machine/overview/">Docker Machine</a>", which is really just a Linux VM inside (for example) a VirtualBox hypervisor. (Windows does effectively the same thing.) And a Linux image will also run fine on a FreeBSD host (apart from mounting volumes, which <a href="https://lists.freebsd.org/pipermail/freebsd-virtualization/2016-January/004082.html">isn't supported yet</a>), because FreeBSD has a Linux compatibility layer that makes it able to respond properly to Linux system calls.

The surprising thing happens when you have a non-Linux docker image, such as <a href="https://hub.docker.com/r/lexaguskov/freebsd/">this relatively popular FreeBSD one</a>. Such an image will only run on a FreeBSD host. Why? Because the image's process will be making system calls, and Linux (i.e. the OS of the docker container) won't respond appropriately to the FreeBSD system calls that the process is making.

Indeed, this is docker's dirty little secret: no non-Linux OS can run natively in a docker container. FreeBSD can't. <a href="http://stackoverflow.com/questions/26825748/docker-image-for-windows">Windows can't</a>. Nor OpenBSD, nor NetBSD. Nor Solaris, nor Illumos, nor SmartOS. Let alone something exotic line Plan9 or BeOS. The <a href="https://docs.docker.com/engine/understanding-docker/">official documentation page about docker architecture</a> doesn't mention this, by the way.

To adapt Henry Ford, you can run whatever OS you want in docker, so long as it's Linux.

So, what if I run Linux and I want to try out another OS? Can I use docker? Yes...but it would be a hassle. Your docker image would need to run KVM (or some other actual hypervisor), which itself would need to run a FreeBSD virtual machine. And KVM would need to proxy various things from the docker container to the FreeBSD guest, including an IP address, network traffic, standard I/O, commands to run, and mounted volumes. Furthermore, you'd have to hard-code in the Dockerfile the amount of system resources like RAM and disk space that the FreeBSD guest had access to. What a mess.

Either that, or skip docker and run KVM directly.

Now, docker's not a bad tool. I'm not bashing docker. But it's important to know that it isn't itself a hypervisor. That's fine—you can get a lot of mileage from it because it cuts out some functionality you often don't need. But the downside of that tradeoff seems to be poorly understood. Hence this blog post. (The more you know...)

I'm interested in exploring some alternatives that are getting a lot less attention than docker. BSD jails provide isolation for a process—and that process could be a hypervisor running another OS. Similarly, from what I understand, Solaris seems to have solved this problem years ago in a well engineered way with their "zones", and free forks of Solaris exist, including SmartOS and Illumos.

I haven't traveled far enough down any of these roads to find the potholes. Have you? I'd be interested to hear about it in the comments below.
