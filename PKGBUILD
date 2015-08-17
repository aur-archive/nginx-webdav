# $Id$
# Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Maintainer: Bartłomiej Piotrowski <barthalion@gmal.com>
# Contributor: Miroslaw Szot <mss@czlug.icis.pcz.pl>
# Modified by Florian Bäuerle <florian.bae@gmail.com>

_cfgdir=/etc/nginx
_tmpdir=/var/lib/nginx

pkgname=nginx-webdav
pkgver=1.2.3
pkgrel=1
pkgdesc="lightweight HTTP server and IMAP/POP3 proxy server including Module for FULL WebDAV support"
arch=('i686' 'x86_64')
depends=('pcre' 'zlib' 'openssl')
makedepends=('passenger')
optdepends=('passenger')
url="http://nginx.org"
license=('custom')
install=nginx.install
backup=(${_cfgdir:1}/fastcgi.conf
		${_cfgdir:1}/fastcgi_params
		${_cfgdir:1}/koi-win
		${_cfgdir:1}/koi-utf
		${_cfgdir:1}/mime.types
		${_cfgdir:1}/nginx.conf
		${_cfgdir:1}/scgi_params
		${_cfgdir:1}/uwsgi_params
		${_cfgdir:1}/win-utf
		etc/logrotate.d/nginx)
source=(http://nginx.org/download/nginx-$pkgver.tar.gz
		https://github.com/arut/nginx-dav-ext-module/tarball/master
		rc.d
		service
		nginx.logrotate)
md5sums=('0a986e60826d9e3b453dbefc36bf8f6c'
         '5267ca60e98f94a5bcf55939a7b9c192'
         'f62c7c9b5a53471d4666a4c49ad363fb'
         '62d494d23aef31d0b867161f9fffa6eb'
         'b38744739022876554a0444d92e6603b')

build() {
	cd "$srcdir"/nginx-$pkgver

	./configure \
        --prefix=$_cfgdir \
        --conf-path=$_cfgdir/nginx.conf \
        --sbin-path=/usr/sbin/nginx \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/lock/nginx.lock \
        --user=http --group=http \
        --http-log-path=/var/log/nginx/access.log \
        --error-log-path=/var/log/nginx/error.log \
        --http-client-body-temp-path=$_tmpdir/client-body \
        --http-proxy-temp-path=$_tmpdir/proxy \
        --http-fastcgi-temp-path=$_tmpdir/fastcgi \
        --http-scgi-temp-path=$_tmpdir/scgi \
        --http-uwsgi-temp-path=$_tmpdir/uwsgi \
        --with-imap --with-imap_ssl_module \
        --with-ipv6 --with-pcre-jit \
        --with-file-aio \
        --with-http_dav_module \
        --with-http_gzip_static_module \
        --with-http_realip_module \
        --with-http_ssl_module \
        --with-http_stub_status_module \
        --add-module=/usr/lib/passenger/ext/nginx \
        --add-module="$srcdir"/arut-nginx-dav-ext-module*/
        #--with-http_mp4_module \
        #--with-http_realip_module \
        #--with-http_addition_module \
        #--with-http_xslt_module \
        #--with-http_image_filter_module \
        #--with-http_geoip_module \
        #--with-http_sub_module \
        #--with-http_flv_module \
        #--with-http_random_index_module \
        #--with-http_secure_link_module \
        #--with-http_degradation_module \
        #--with-http_perl_module \

	make
}

package() {
	cd "$srcdir"/nginx-$pkgver
	make DESTDIR="$pkgdir" install

	install -d "$pkgdir"/etc/logrotate.d
	install -m644 "$srcdir"/nginx.logrotate "$pkgdir"/etc/logrotate.d/nginx

	sed -e 's|\<user\s\+\w\+;|user html;|g' \
		-e '44s|html|/usr/share/nginx/html|' \
		-e '54s|html|/usr/share/nginx/html|' \
		-i "$pkgdir"/etc/nginx/nginx.conf
	rm "$pkgdir"/etc/nginx/*.default

	install -d "$pkgdir"/$_tmpdir

	install -d "$pkgdir"/usr/share/nginx
	mv "$pkgdir"/etc/nginx/html/ "$pkgdir"/usr/share/nginx

	install -D -m755 "$srcdir"/rc.d "$pkgdir"/etc/rc.d/nginx
	install -Dm644 "$srcdir"/service "$pkgdir"/usr/lib/systemd/system/nginx.service
	install -D -m644 LICENSE "$pkgdir"/usr/share/licenses/nginx/LICENSE
	rm -rf "$pkgdir"/var/run
}
