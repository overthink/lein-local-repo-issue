# lein-local-repo-test

A small repro for https://github.com/technomancy/leiningen/issues/1140

```text
$ lein version
Leiningen 2.7.1 on Java 1.7.0_121 OpenJDK 64-Bit Server VM

$ git clone https://github.com/overthink/lein-local-repo-issue                                                                                                                    
Cloning into 'lein-local-repo-issue'...
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 0), reused 9 (delta 0), pack-reused 0
Unpacking objects: 100% (9/9), done.
Checking connectivity... done.

$ cd lein-local-repo-issue

# note we have a local repo defined
$ cat profiles.clj 
{:system {:local-repo "/tmp/localrepo"}}

# make sure we don't have any kibit jars already
$ rm -rf ~/.m2/repository/jonase/ /tmp/localrepo

$ lein kibit
Retrieving cider/cider-nrepl/0.9.1/cider-nrepl-0.9.1.pom from clojars
Retrieving org/clojure/tools.nrepl/0.2.10/tools.nrepl-0.2.10.pom from central
Retrieving org/clojure/pom.contrib/0.1.2/pom.contrib-0.1.2.pom from central
Retrieving org/sonatype/oss/oss-parent/7/oss-parent-7.pom from central
Retrieving org/tcrawley/dynapath/0.2.3/dynapath-0.2.3.pom from central
Retrieving lein-try/lein-try/0.4.3/lein-try-0.4.3.pom from clojars
Retrieving lein-kibit/lein-kibit/0.1.3/lein-kibit-0.1.3.pom from clojars
Retrieving jonase/kibit/0.1.3/kibit-0.1.3.pom from clojars
Retrieving org/clojure/clojure/1.8.0/clojure-1.8.0.pom from central
Retrieving org/clojure/core.logic/0.8.11/core.logic-0.8.11.pom from central
Retrieving org/clojure/clojure/1.6.0/clojure-1.6.0.pom from central
Retrieving org/clojure/tools.cli/0.3.5/tools.cli-0.3.5.pom from central
Retrieving org/clojure/clojure/1.4.0/clojure-1.4.0.pom from central
Retrieving org/sonatype/oss/oss-parent/5/oss-parent-5.pom from central
Retrieving org/clojure/tools.namespace/0.2.11/tools.namespace-0.2.11.pom from central
Retrieving org/tcrawley/dynapath/0.2.3/dynapath-0.2.3.jar from central
Retrieving org/clojure/tools.nrepl/0.2.10/tools.nrepl-0.2.10.jar from central
Retrieving org/clojure/tools.namespace/0.2.11/tools.namespace-0.2.11.jar from central
Retrieving org/clojure/clojure/1.8.0/clojure-1.8.0.jar from central
Retrieving org/clojure/tools.cli/0.3.5/tools.cli-0.3.5.jar from central
Retrieving org/clojure/core.logic/0.8.11/core.logic-0.8.11.jar from central
Retrieving lein-kibit/lein-kibit/0.1.3/lein-kibit-0.1.3.jar from clojars
Retrieving lein-try/lein-try/0.4.3/lein-try-0.4.3.jar from clojars
Retrieving jonase/kibit/0.1.3/kibit-0.1.3.jar from clojars
Retrieving cider/cider-nrepl/0.9.1/cider-nrepl-0.9.1.jar from clojars
Could not find artifact jonase:kibit:jar:0.1.3
This could be due to a typo in :dependencies or network issues.
If you are behind a proxy, try setting the 'http_proxy' environment variable.

# Above we can see it fetched kibit. And yes, it's in the local repo:
$ find /tmp/localrepo/jonase                                                                                                                                   
/tmp/localrepo/jonase
/tmp/localrepo/jonase/kibit
/tmp/localrepo/jonase/kibit/0.1.3
/tmp/localrepo/jonase/kibit/0.1.3/kibit-0.1.3.pom.sha1
/tmp/localrepo/jonase/kibit/0.1.3/_maven.repositories
/tmp/localrepo/jonase/kibit/0.1.3/kibit-0.1.3.pom
/tmp/localrepo/jonase/kibit/0.1.3/kibit-0.1.3.jar.sha1
/tmp/localrepo/jonase/kibit/0.1.3/kibit-0.1.3.jar

# And it's NOT in the default repo -- as expected.
$ find ~/.m2/repository/jonase
find: `/home/mark/.m2/repository/jonase': No such file or directory


#  But `lein kibit` fails to find it. Again, just to show the issue more obviously:
$ lein kibit
Could not find artifact jonase:kibit:jar:0.1.3
This could be due to a typo in :dependencies or network issues.
If you are behind a proxy, try setting the 'http_proxy' environment variable.

# now turn off the local repo
$ mv profiles.clj profiles.clj.off

# now it fetches the missing jars and works! (subprocess failed is because kibit exits non-zero)
$ lein kibit
Retrieving jonase/kibit/0.1.3/kibit-0.1.3.pom from clojars
Retrieving jonase/kibit/0.1.3/kibit-0.1.3.jar from clojars
At /home/mark/tmp/x/lein-local-repo-issue/src/test/core.clj:4:
Consider using:
  42
instead of:
  (do 42)
Subprocess failed
```
