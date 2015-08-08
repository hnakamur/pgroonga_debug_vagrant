# -*- mode: ruby -*-
# vi: set ft=ruby :

setup_script =<<SCRIPT
sudo apt-get update

# build postgresql with debug option
# See http://lets.postgresql.jp/documents/technical/sourcetree/3
sudo apt-get -y install curl tar bzip2 build-essential libreadline-dev zlib1g-dev ctags
cd /home/vagrant
curl -sLO https://ftp.postgresql.org/pub/source/v9.4.4/postgresql-9.4.4.tar.bz2
tar xf postgresql-9.4.4.tar.bz2
sudo chown -R vagrant: postgresql-9.4.4
cd postgresql-9.4.4
./configure --enable-cassert --enable-debug CFLAGS="-ggdb -O0 -fno-omit-frame-pointer"
make
sudo make install
./src/tools/make_ctags
sudo useradd --home /usr/local/pgsql/data postgres
sudo sed -i 's|secure_path="|&/usr/local/pgsql/bin:|' /etc/sudoers
sudo mkdir /usr/local/pgsql/data
sudo chown postgres /usr/local/pgsql/data
echo 'export PATH=/usr/local/pgsql/bin:$PATH' >> /home/vagrant/.bashrc
export PATH=/usr/local/pgsql/bin:$PATH
sudo -u postgres initdb -D /usr/local/pgsql/data
sudo -u postgres postgres -D /usr/local/pgsql/data >logfile 3>&1 &

# build groonga from source
sudo apt-get -y install curl tar build-essential pkg-config zlib1g-dev liblzo2-dev libmsgpack-dev libzmq-dev libevent-dev libmecab-dev
cd /home/vagrant
curl -sLO http://packages.groonga.org/source/groonga/groonga-5.0.6.tar.gz
tar xf groonga-5.0.6.tar.gz
chwon -R vagrant: groonga-5.0.6
cd groonga-5.0.6
./configure --enable-debug --enable-fmalloc --enable-memory-debug \
  --with-mecab \
  CFLAGS="-ggdb -fno-omit-frame-pointer" \
  CXXFLAGS="-ggdb -fno-omit-frame-pointer"
make
sudo make install
find . -name '*.[ch]' | xargs ctags -a -f tags --c-kinds=+dfmstuv

# build pgroonga from source
sudo apt-get -y install git
cd /home/vagrant
git clone https://github.com/pgroonga/pgroonga
sudo chown -R vagrant: pgroonga
cd pgroonga
make DEBUG=1
sudo make install
find . -name '*.[ch]' | xargs ctags -a -f tags --c-kinds=+dfmstuv

cd /home/vagrant
find . -name '*.[ch]' | xargs ctags -a -f tags --c-kinds=+dfmstuv

sudo -u postgres createdb -E utf8 test
sudo -u postgres psql -c "CREATE EXTENSION pgroonga;" test

# install gdb
sudo apt-get -y install gdb
# add alias for gdb to attach to psql background process
cat <<'EOF' >> /home/vagrant/.bashrc
alias gdbpgsql='sudo gdb postgres `ps auxww | grep '\''[p]ostgres.*idle'\'' | awk '\''{print $2}'\''`'
EOF
# Modify libstdc++.so.6.0.19-gdb.py to fix the following error
#  File "/usr/share/gdb/auto-load/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.19-gdb.py", line 63, in <module>
#      from libstdcxx.v6.printers import register_libstdcxx_printers
#      ImportError: No module named 'libstdcxx'
sudo sed -i.orig '/^libdir =/a\
if not pythondir in sys.path:\
    sys.path.insert(0, pythondir)\
' /usr/share/gdb/auto-load/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.19-gdb.py

# install pt (the platinum searcher)
if [ -x /home/vagrant/bin/pt ]; then
  cd /home/vagrant
  curl -sLO https://github.com/monochromegane/the_platinum_searcher/releases/download/v1.7.8/pt_linux_amd64.tar.gz
  tar xf pt_linux_amd64.tar.gz
  mkdir bin
  mv pt_linux_amd64/pt bin/
  rm -r pt_linux_amd64
  echo 'export PATH=$HOME/bin:$PATH' >> /home/vagrant/.bashrc
fi
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
