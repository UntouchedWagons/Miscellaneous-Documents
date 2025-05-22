
In `/etc/apt/apt.conf.d` add the following file

`99defaultrelease`:

```
APT::Default-Release "stable";
```

in `/etc/apt/sources.list.d` - add urls for testing / unstable sources

`testing.list`:

```
deb     http://mirror.csclub.uwaterloo.ca/debian/    testing main contrib non-free
deb-src http://mirror.csclub.uwaterloo.ca/debian/    testing main contrib non-free
```

run

`apt-get update`

and then install what you need with

`apt-get -t testing install something`

Be very very careful if you install stuff that has plenty of dependencies. Preferably don't do this on production.

You can as well try your luck at backports or similar repository.

## Source

https://serverfault.com/a/22418
