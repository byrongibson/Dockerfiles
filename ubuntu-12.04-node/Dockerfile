# Docker official ubuntu 12.04 LTS
FROM ubuntu:12.04

# SETUP
RUN cd /tmp
RUN apt-get update -y
#RUN apt-get upgrade -y  #fails using Docker's ubuntu
RUN apt-get install wget build-essential automake -y
RUN wget http://nodejs.org/dist/latest/node-v0.10.26.tar.gz
RUN wget http://nodejs.org/dist/latest/SHASUMS256.txt

# CHECKSUM
# RUN checksum: continue on success, exit Docker build on fail.  Requires some 
# script-fu to reduce output down to a single 0 or non-zero POSIX exit code.  
# Details in footnote [1].  TLDR:  run checksum, merge sderr into sdout (2>$1) before 
# piping stout to grep, grep for checksum success code OK, suppress all output except 
# exit codes with -qs flags on grep.  Returns 0 exit code if checksum succeeds, 1 if
# not.  Docker breaks and exits on non-zero exit code.
RUN sha256sum -c SHASUM256.txt 2>&1|grep -qs OK

# INSTALL
RUN tar -xvf node-v0.10.26.tar.gz && cd node-v0.10.26
RUN ./configure && make && make install

# CLEANUP
RUN apt-get purge wget build-essential automake -y && apt-get autoremove -y


# [1]:Docker automatically exits Dockerfile build if any RUN command returns a non-zero 
# (error) exit code.  Problem here is that node.js's SHASUMS256.txt contains many lines
# for lots of different files - source, binaries for different platforms, etc. - only 
# one of which will succeed at this checksum and return 0 exit code.  All other lines 
# will fail and be sent to sderr.  sterr is not included by default in pipes, only sdout, 
# causing this command to return a jumble error data prior to the pipe.  Docker then 
# interprets this as non-zero error codes and exits, even if the checksum succeeds.  
# Solution is to manually merge sderr into stout, then pipe to grep, grep for the OK 
# (case sensitive), and suppress all output except error codes with -qs.  If OK is 
# is present in the combined output, this line will return a 0 exit code.  If not, 
# will return 1.  You can manually check the exit code with `echo $?`.

