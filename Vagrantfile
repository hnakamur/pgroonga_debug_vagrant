# -*- mode: ruby -*-
# vi: set ft=ruby :

setup_script =<<SCRIPT
sudo apt-get update

# build postgresql with debug option
# See http://lets.postgresql.jp/documents/technical/sourcetree/3
sudo apt-get -y install curl tar bzip2 build-essential libreadline-dev zlib1g-dev gdb
cd ~vagrant
curl -sLO https://ftp.postgresql.org/pub/source/v9.4.4/postgresql-9.4.4.tar.bz2
tar xf postgresql-9.4.4.tar.bz2
cd postgresql-9.4.4
./configure --enable-cassert --enable-debug CFLAGS="-ggdb -O0 -fno-omit-frame-pointer"
make
sudo make install
sudo useradd --home /usr/local/pgsql/data postgres
sudo sed -i 's|secure_path="|&/usr/local/pgsql/bin:|' /etc/sudoers
sudo mkdir /usr/local/pgsql/data
sudo chown postgres /usr/local/pgsql/data
echo 'export PATH=/usr/local/pgsql/bin:$PATH' >> ~vagrant/.bash_profile
export PATH=/usr/local/pgsql/bin:$PATH
sudo -u postgres initdb -D /usr/local/pgsql/data
sudo -u postgres postgres -D /usr/local/pgsql/data >logfile 2>&1 &

# build groonga from source
sudo apt-get -y install curl tar build-essential pkg-config zlib1g-dev liblzo2-dev libmsgpack-dev libzmq-dev libevent-dev libmecab-dev
cd ~vagrant
curl -sLO http://packages.groonga.org/source/groonga/groonga-5.0.6.tar.gz
tar xf groonga-5.0.6.tar.gz
cd groonga-5.0.6
./configure --enable-debug --enable-fmalloc --enable-memory-debug --with-mecab
make
sudo make install

# build pgroonga from source
sudo apt-get -y install git
cd ~vagrant
git clone https://github.com/pgroonga/pgroonga
cd pgroonga
make DEBUG=1
sudo make install

sudo -u postgres createdb -E utf8 test
sudo -u postgres psql -c "CREATE EXTENSION pgroonga;" test
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.33.10"
#  config.vm.synced_folder ".", "/vagrant", type: "nfs"
  config.vm.provider "vrtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 4
  end
#  config.vm.provision "shell", inline: setup_script
end
