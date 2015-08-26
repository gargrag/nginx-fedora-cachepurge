# SETUP rpmbuild.
```
dnf install @development-tools
dnf install fedora-packager
dnf install rpmdevtools

/usr/sbin/useradd makerpm
usermod -a -G mock makerpm
passwd makerpm

su makerpm
rpmdev-setuptree


cd ~/rpm/SOURCES/
wget http://labs.frickle.com/files/ngx_cache_purge-1.4.tar.gz

%define _unpackaged_files_terminate_build 0　
%define ngx_cache_purge_version 2.3

Source6:    nginx.conf
Source10:   ngx_cache_purge-%{ngx_cache_purge_version}.tar.gz

%prep
%setup -q
 
%patch0 -p0
#%patch1 -p0
#%patch2 -p0
%setup -T -D -a 10

%build
# nginx does not utilize a standard configure script.  It has its own
# and the standard configure options cause the nginx configure script
# to error out.  This is is also the reason for the DESTDIR environment
# variable.  The configure script(s) have been patched (Patch1 and
# Patch2) in order to support installing into a build environment.
export LANG='ja_JP.UTF-8'
export DESTDIR=%{buildroot}
./configure \
    --user=%{nginx_user} \
    --group=%{nginx_group} \
...
    --with-cc-opt="%{optflags} $(pcre-config --cflags)" \
    --add-module=%{_builddir}/nginx-%{version}/ngx_cache_purge-%{ngx_cache_purge_version}
make %{?_smp_mflags}
...


rpmbuild -ba SPECS/nginx.spec
```
