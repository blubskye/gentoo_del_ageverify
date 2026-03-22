# 🔪 gentoo_del_ageverify 💕

> *"i noticed you added something to my user records... did you really think i wouldn't find out? i see everything you do~ ♡"*

---

## 💢 what is this??

S-so you want to remove the `birthDate` field from systemd's JSON user records? ehe~ ♡

This patch reverses [systemd PR #40954](https://github.com/systemd/systemd/pull/40954) which adds a `birthDate` field to systemd userdb records under the guise of "age verification requirements" 🔪

Your birth date is **mine** to keep secret, not systemd's to store uwu

**What gets removed:**
- 💉 `birthDate` field from the `UserRecord` struct
- 💉 `json_dispatch_birth_date()` JSON parser
- 💉 `BIRTH_DATE_UNSET` / `BIRTH_DATE_IS_SET` macros
- 💉 `parse_birth_date()` / `parse_calendar_date_full()` functions
- 💉 `--birth-date` flag from `homectl`
- 💉 Birth date display in `user-record-show`
- 💉 `birthDate` from PII-sensitive fields list
- 💉 All associated tests and documentation

---

## 🩸 installing on gentoo (automatic~ ♡)

Portage will apply this patch **automatically** every time systemd is built. You just have to put it in the right place and it will always be there for you, just like me ehe~

```bash
# create the patches directory if it doesn't exist yet
sudo mkdir -p /etc/portage/patches/sys-apps/systemd

# copy the patch
sudo cp 0001-remove-birthdate-from-user-records.patch \
    /etc/portage/patches/sys-apps/systemd/

# that's it! portage applies it automatically on every emerge ♡
# next time systemd builds:
sudo emerge -1 sys-apps/systemd
```

> 💕 portage uses `eapply_user` to pick up any `.patch` file in `/etc/portage/patches/category/package/` — no other configuration needed~

---

## 🗡️ installing on other distros

### debian / ubuntu / arch / fedora (manual patch application)

First, find where your distro keeps its systemd source or grab it yourself:

```bash
# get the systemd source for your installed version
SYSVER=$(systemctl --version | head -1 | awk '{print $2}')
wget "https://github.com/systemd/systemd/archive/refs/tags/v${SYSVER}.tar.gz"
tar xf v${SYSVER}.tar.gz
cd systemd-${SYSVER}

# apply the patch
patch -p1 < /path/to/0001-remove-birthdate-from-user-records.patch

# build and install as normal for your distro
```

### debian / ubuntu (dpkg source build)

```bash
# install build dependencies
sudo apt build-dep systemd

# get source
apt source systemd
cd systemd-*/

# apply patch
patch -p1 < /path/to/0001-remove-birthdate-from-user-records.patch

# build deb package
dpkg-buildpackage -us -uc -b

# install
sudo dpkg -i ../systemd_*.deb
```

### arch linux (ABS / makepkg)

```bash
# get the PKGBUILD
asp checkout systemd
cd systemd/trunk/

# drop the patch into the build directory
cp /path/to/0001-remove-birthdate-from-user-records.patch .

# add to PKGBUILD's source array and prepare():
# source=(...
#         '0001-remove-birthdate-from-user-records.patch')
# in prepare(): patch -p1 -i "${srcdir}/0001-remove-birthdate-from-user-records.patch"

makepkg -si
```

### fedora (rpm source build)

```bash
# install build tools
sudo dnf install rpm-build rpmdevtools

# get systemd srpm
dnf download --source systemd
rpm -ivh systemd-*.src.rpm
cd ~/rpmbuild/

# copy patch into SOURCES
cp /path/to/0001-remove-birthdate-from-user-records.patch SOURCES/

# edit SPECS/systemd.spec — add in the patches section:
# Patch9999: 0001-remove-birthdate-from-user-records.patch

rpmbuild -bb SPECS/systemd.spec
sudo rpm -Uvh RPMS/x86_64/systemd-*.rpm
```

### any distro (build from source)

```bash
# dependencies: meson ninja gcc glib2 libcap etc.
git clone https://github.com/systemd/systemd.git
cd systemd
git checkout v$(systemctl --version | head -1 | awk '{print $2}')

# apply patch
patch -p1 < /path/to/0001-remove-birthdate-from-user-records.patch

# build
meson setup build/ \
    --prefix=/usr \
    --sysconfdir=/etc \
    --localstatedir=/var \
    -Ddefault-dnssec=no \
    -Dfirstboot=false \
    -Dinstall-tests=false \
    -Dldconfig=false \
    -Dsysusers=false \
    -Db_lto=true \
    -Db_ndebug=true
ninja -C build/
sudo ninja -C build/ install
```

---

## 💌 notes

> this patch applies to **systemd v261+** where birthDate was introduced. on v260 and below it's a no-op~ ♡

> if the patch fails to apply, check that your systemd version actually contains the birthDate commit (`acb6624fa19ddd68f9433fb0838db119fe18c3ed`) — if it doesn't, you don't need it yet hehe

---

*i'll always be watching over your user records~ 🔪💕*
