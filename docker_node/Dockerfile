 FROM ubuntu
 MAINTAINER Ha Minh minhh@gmail.com

 # Fetch Nodejs from the official repo (binary .. no hassle to build, etc.)
 ADD http://nodejs.org/dist/v0.10.19/node-v0.10.19-linux-x64.tar.gz /opt/

 # Untar and add to the PATH
 RUN cd /opt && tar xzf node-v0.10.19-linux-x64.tar.gz
 RUN ln -s /opt/node-v0.10.19-linux-x64 /opt/node
 RUN echo "export PATH=/opt/node/bin:$PATH" >> /etc/profile

 # A little cheat for upstart ;)
 RUN dpkg-divert --local --rename --add /sbin/initctl
 RUN ln -s /bin/true /sbin/initctl

 # Update apt sources list to fetch mongodb and a few key packages
 RUN echo "deb http://archive.ubuntu.com/ubuntu precise universe" >> /etc/apt/sources.list
 RUN apt-get update
 RUN apt-get install -y python git
 RUN apt-get install -y mongodb

 # Finally - we wanna be able to SSH in
 RUN apt-get install -y openssh-server
 RUN mkdir /var/run/sshd

 # And we want our SSH key to be added
 RUN mkdir /root/.ssh && chmod 700 /root/.ssh
 ADD id_rsa.pub /root/.ssh/authorized_keys
 RUN chmod 400 /root/.ssh/authorized_keys && chown root. /root/.ssh/authorized_keys

 # Expose a bunch of ports .. 22 for SSH and 3000 for our node app
 EXPOSE 22 3000

 ENTRYPOINT ["/usr/sbin/sshd", "-D"]
