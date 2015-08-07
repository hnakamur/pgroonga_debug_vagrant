# -*- mode: ruby -*-
# vi: set ft=ruby :

setup_script =<<SCRIPT
sudo apt-get update && \
sudo apt-get -y install gdb

# install postgresql server with dev packages and debug symbols
sudo apt-get -y install curl && \
echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' | sudo sh -c 'cat > /etc/apt/sources.list.d/postgresql.list' && \
curl -s https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && \
sudo apt-get update && \
sudo apt-get -y install postgresql-9.4 postgresql-server-dev-9.4 postgresql-9.4-dbg

# build groonga from source
sudo apt-get -y install wget tar build-essential zlib1g-dev liblzo2-dev libmsgpack-dev libzmq-dev libevent-dev libmecab-dev && \
cd /usr/local/src && \
wget http://packages.groonga.org/source/groonga/groonga-5.0.6.tar.gz && \
tar xvzf groonga-5.0.6.tar.gz && \
cd groonga-5.0.6 && \
./configure --enable-debug --enable-fmalloc --enable-memory-debug --with-mecab && \
make && \
sudo make install

# build pgroonga from source
sudo apt-get -y install git pkg-config && \
cd /usr/local/src && \
git clone https://github.com/pgroonga/pgroonga && \
cd pgroonga && \
make DEBUG=1 && \
sudo make install

sudo service postgresql start && \
sudo -u postgres psql -c "CREATE USER pgroonga WITH SUPERUSER PASSWORD 'pgroonga'" && \
sudo -u postgres createdb -O pgroonga -E utf8 pgroonga
sudo -u postgres psql -c "CREATE EXTENSION pgroonga;" pgroonga
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.synced_folder ".", "/vagrant", type: "nfs"
  config.vm.provider "vrtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 4
  end
  config.vm.provision "shell", inline: setup_script
end
