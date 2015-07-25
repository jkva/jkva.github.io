---
layout: post
title: Trying out Rakudo Perl6 and fixing a bug
category: open_source, programming, perl
tags: perl, perl6, oss, irc, github
---

I've been wanting to try out [Perl 6](http://www.perl6.org) for a while now. Having tried it when Rakudo Star first 
came out, it has since come a long way. There are rumours that Perl 6 will officially come out this Christmas, if 
Larry Wall is to be believed.

After following the [linux installation instructions](http://rakudo.org/how-to-get-rakudo/) I managed to successfully 
install rakudo, moarvm and panda on my vps running Debian 6 (yeah, I need to upgrade).

However, after running `panda Task::Star`, the DBDish module would fail with the following error:

```
==> Building DBIish
Compiling lib/DBDish.pm6 to mbc
Compiling lib/DBIish.pm6 to mbc
Compiling lib/DBDish/Pg.pm6 to mbc
===SORRY!=== Error while compiling lib/DBDish/Pg.pm6
Operators '~~' and '..' are non-associative and require parentheses
at lib/DBDish/Pg.pm6:148
------> ub status-is-ok($status) { $status ~~ 0.⏏.4 }
build stage failed for DBIish: Failed building lib/DBDish/Pg.pm6
```

Huh. Odd. To irc.freenode.org\#perl6 we go.

```
6:41:49 PM	jkva  RabidGravy: Gotcha :) I'm running Debian Squeeze, just finished installing rakudobrew, moarvm and panda. When running "panda install Task::Star" as per the directions, I eventually get:
6:42:43 PM	jkva  the following build error - <pastie link>
6:48:46 PM	RabidGravy  jkva, I believe that is caused by a very rcent change in rakudo.  It will  be fixed in the modules
6:49:37 PM	jkva  RabidGravy: Too bad. If I want to play with Perl6, do I have any other means at this point? Use an older version?
6:50:54 PM	TimToady  there's a .2 release that addresses that
6:51:30 PM	TimToady  though it's in a branch
6:52:52 PM	RabidGravy  jkva, https://github.com/rakudo/rakudo/tree/release/2015.07.2 specifically
6:53:21 PM	jkva  Ah, excellent. Thank you very much, I will try my luck with that one.
```

Ok, so I was in luck - a recent release fixed this problem. I checked out the tag. This time I had to compile from source, though.

```
~/rakudo$ perl Configure.pl --gen-moar --gen-nqp --backends=moar
make
make install
echo "export PATH=$(pwd)/install/bin/:\$PATH" >> ~/.bashrc
```

Installed pandas manually:

```
git clone https://github.com/tadzik/panda
cd panda
perl6 bootstrap.pl
```

Install Task::Star:

`panda install Task::Star`

Alas...

```
# Using PGDATABASE:
t/01-ConnectConfig-PG.t ... ok
t/05-mock.t ............... ok
# Connect failed with error Cannot locate native library 'libmysqlclient.so': libmysqlclient.so: cannot open shared object file: No such file or directory
t/10-mysql.t .............. ok
# Connect failed with error Cannot locate native library 'libmysqlclient.so': libmysqlclient.so: cannot open shared object file: No such file or directory
t/25-mysql-common.t ....... ok
# Connect failed with error Cannot locate native library 'libmysqlclient.so': libmysqlclient.so: cannot open shared object file: No such file or directory
# expected: ''a\.b''cd?', "\"?", $1'
#      got: (Any)
# Looks like you failed 1 test of 2
t/30-Pg.t .................
Dubious, test returned 1 (wstat 256, 0x100)
Failed 1/2 subtests
# Connect failed with error Dynamic variable %*opts not found
t/35-Pg-common.t .......... ok
# Connect failed with error Cannot locate native library 'libsqlite3.so': libsqlite3.so: cannot open shared object file: No such file or directory
t/40-sqlite-common.t ...... ok
t/41-sqlite-exec-error.t .. ok
```

Not the same error, but it seemed I needed mysql headers. After installing `libmysqlclient-dev` I got:

```
t/05-mock.t ............... ok
# Connect failed with error DBD::mysql connection failed: Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
t/10-mysql.t .............. ok
```

Figuring DBIish wasn't properly mocking the mysql interface, I went back to IRC:

```
7:56:51 PM	jkva  Wth? DBDish expects me to have mysql running for its test?
7:58:31 PM	jkva  Shouldn't that be mocked?
8:02:06 PM	jkva  Ah, I see on github it's on the agenda. Nvm.
```

I was quite wrong here, actually. The DBIish author replied to me:

```
8:30:00 PM	moritz  jkva: DBIish comes with the sqlite, mysql and postgres backends
8:30:26 PM	moritz  jkva: and it tries to run all of its tests, but skip those tests that it can't run due to missing libs
9:35:29 PM	jkva  moritz: For me the mysql test failed, and stopped Task::Star from going on to the next module. I resorted to using -notests, but would rather not have.
9:35:52 PM	jkva  moritz: ah, I understand. I have libraries installed, but not MySQL itself. So this is an error on my part. Apologies, I was mistaken.
9:36:11 PM	moritz  jkva: no, souldn't fail in that case either
9:36:23 PM	jkva  moritz: Would you like me to file an issue?
9:36:28 PM	moritz  jkva: yes please
9:36:33 PM	jkva  moritz: will do.
9:36:59 PM	moritz  jkva++
```

I opened an [issue on github](https://github.com/perl6/DBIish/issues/14) - but this was quickly sorted:

```
9:53:27 PM	moritz  jkva: ah, it's actually t/30-Pg.t that fails
9:53:42 PM	moritz  jkva: the mysql tests spew out error messages, but don't fail
9:53:55 PM	jkva  d'oh
9:54:17 PM	moritz  jkva: and the failure in 30-Pg.t should be fixed by https://github.com/perl6/DBIish/commit/cc432b0fb49e7a9c485e3b8d379f26186fe3086c (from an hour ago, roughly)
9:54:26 PM	jkva  Ha, no way
9:54:44 PM	jkva  Awesome
9:55:15 PM	jkva  Right, the mysql errors were a red herring. Thanks!
9:55:48 PM	dalek  DBIish: 7cf60c4 | moritz++ | lib/DBDish/Pg.pm6:
9:55:48 PM	dalek  DBIish: Fix a build failure with rakudo-2015.07, jkva++
9:55:48 PM	dalek  DBIish:
9:55:48 PM	dalek  DBIish: closes #14
9:55:48 PM	dalek  DBIish: review: https://github.com/perl6/DBIish/commit/7cf60c4669
9:56:08 PM	jkva  Whelp, that one was quickly sorted :P
9:56:20 PM	moritz  jkva: but the end of your paste also include an error that I hadn't seen before, which the commit above should have fixed
9:56:34 PM	jkva  moritz: glad to hear - I figured it couldn't hurt
```

After rebuilding DBIish, everything worked just fine. To me this signifies the agility with which OSS can address and 
fix problems. Having not been very involved with Perl6, its community seems quite nice. I was glad to be able to help fix an issue,
now to see what Perl6 has to offer.

