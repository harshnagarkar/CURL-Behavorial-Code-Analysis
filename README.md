![curl logo](https://cdn.rawgit.com/curl/curl-www/master/logo/curl-logo.svg)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/63/badge)](https://bestpractices.coreinfrastructure.org/projects/63)
[![Coverity passed](https://scan.coverity.com/projects/curl/badge.svg)](https://scan.coverity.com/projects/curl)
[![Build Status](https://travis-ci.org/curl/curl.svg?branch=master)](https://travis-ci.org/curl/curl)
[![Coverage Status](https://coveralls.io/repos/github/curl/curl/badge.svg)](https://coveralls.io/github/curl/curl)

Curl is a command-line tool for transferring data specified with URL
syntax. Find out how to use curl by reading [the curl.1 man
page](https://curl.haxx.se/docs/manpage.html) or [the MANUAL
document](https://curl.haxx.se/docs/manual.html). Find out how to install Curl
by reading [the INSTALL document](https://curl.haxx.se/docs/install.html).

libcurl is the library curl is using to do its job. It is readily available to
be used by your software. Read [the libcurl.3 man
page](https://curl.haxx.se/libcurl/c/libcurl.html) to learn how!

You find answers to the most frequent questions we get in [the FAQ
document](https://curl.haxx.se/docs/faq.html).

# Behavorial Code Analysis of lib architecture Hotspot forked at Sep 2017**

*1. Analyze your codebase and identify any architectural hotspots. Suggest which
files in each* of the architectural hotspots should be considered for
refactoring. Do any files suggest that they should be refactored but show signs
that refactoring has already been conducted? If so, list those files and at what
timeframe (Month and Year) shows signs of refactoring and what attributes lead
you to that conclusion. *Undergraduate students should evaluate a minimum of
three architectural hotspots in their analysis while graduate students should
analyze a minimum of five (if five exists).*

My top three Architectural hotspots are these. Lib, Test, and src,

Lib is the biggest hotspot.

Files that can be refactored in the lib folder hotspot.

I choose my domain to be the \`lib\` folder in the curl project. Refactoring
targets

>   url.c needs to be refactored main reason being its complexity is almost
>   above 10000 ws. It has a Large brain method Curl_setopt and Deeply nested
>   logic in check_noproxy method.

>   ftp.c needs to be refactored again seeing complexity which is around 6700
>   ish. This file has a Large Brain method ftp_statemach_act. It has a deeply
>   nested logic in ftp_parse_url_path.

>   Http.c needs to be refactored which has a complexity of 6700 ish. It has a
>   large brain method Curl_http. Also, it has a deeply nested logic in
>   Curl_add_custom_headers .

>   I see no false positive with the hotspots I selected. They are no
>   significant sign of reactivation.

2. Look at the code of the hotspots that are currently still in the codebase and
determine what refactoring could be done. For this question, pick one file. You
do not have to actually do the refactoring but rather identify a refactoring
technique that can be performed.

>   As the largest hotspot is url.c which has the highest complexity. We need to
>   refactor the code so that it is more readable and simpler while preserving
>   or improving its performance and standards(Like robustness).

1.  We identified before that file has a large brain method \> Curl_setopt. This
    function itself has a complexity of 443 with \> 1337 lines of code.

>   On viewing code, I see at least 100 switch conditions and the code is very
>   hard to read even after comments because of all these conditions and also
>   some even have if and else statements in it and for loops in switch
>   statements.

1.  One way to refactor this large brain is to group by feature or \>
    component-wise these switch statements and possibly create another \> class
    or file. This modularization will make code more readable \> and

>   2. Another thing we identified was the file has a deeply nested login in
>   method chweck_noproxy.

1.  This function is 5 layers deep. So in order to solve this problem, \> we
    need to reduce loops and loose complexity or create a grouping \> of
    condition which will reduce this problem.

3. Look at the XRay analysis of your codebase. Is there any unexpected coupling
of files? If so, which files and wherein the code is the coupling occurring. Why
is the coupling occurring? Do you suggest any changes to the files to remove
this coupling?

There are no significant coupling in the repository, yet top few,

Files that stand out

1.  Multi-double.c- multi-single.c \|- Dependency issue. Seems like \> double is
    dependent on single. \>
    -----------------------------------------------------------------------72%

2.  Lib503.c-Lib504.c \|- seem unrelated, looks like the same dependency \>
    -------------71%

3.  Lib526h.c- Lib530.c \|- seem unrelated, looks same like dependency \> issue
    ------69%

4.  Multi-debugcallback.c-multi-double.c \|- seems unrelated. Looks like \>
    function multi-double is prone to failure and has an error \> handling \>
    mechanism----------------------------68%

5.  Lib518.c-Lib537.c \|- seems unrelates. Looks like same dependency \>
    issue.---------------------------------------------------------------------------------------67%

6.  Lib504.c-Lib507.c \|- seems unrelated. Looks like same dependency \>
    issue----------------------------------------------------------------------------------------66%

7.  lib526.c-Lib533.c \|- seems unrelated. Looks like same dependency \>
    issue---------------------------------------------------------------------------------------65%

8.  Let look at the first two file function:-

curl/docs/examples/multi-double.c/main

And

curl/docs/examples/multi-single.c/main

Reading comments we see that that the two files are doing the same HTTP
transfer. One is doing it for a single transfer while one is doing it for
multiple transfers. Although you will say that it's an example and refactoring
it doesn't matter, I say a good coder should refactor all code possible. I see
that the single Http transfer function needs a wrapper function. This allows
access to single and multiple function calls with just a change of parameters in
a loop

2. Lets inspect next two coupling

curl/tests/libtest/lib503.c/test

And

curl/tests/libtest/lib504.c/test

If you inspect the code and read comments you see that 503 test function is used
to handle HTTP tests while 504 is handling the HTTPS test. The only difference
between the two functions is extra parameters in easy_setopt function and error
handling. We can probably combine these two functions together, reducing testing
file size and optimizing code.

3. Let's look at the third file set

curl/tests/libtest/lib526.c/test

And

curl/tests/libtest/lib530.c/test

If we look closely at both the functions, they are test conditions used for
testing the survival of curl API on crashing conditions. The lib 526 files tests
for crash conditions related to various other condition linked in files like
lib527, lib532, etc. Whereas file 530 is also checking for one such type of
condition. So why they are coupling? The main reason is as curl API changes its
behaviors, the test conditions need to be updated. Especial the crash conditions
change every time the software gets more robust hence the coupling of the file.

I think this coupling can be unlinked by integrating the file 530 methods in
526.

