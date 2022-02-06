# Kattis C++ skeleton
This repository is a project skeleton for projects solving kattis
problems using c++. The goal is to make it easier to start coding
and to use some of all the tools available for c++. Essentially, the
skeleton contains two parts: Configuration for a set of tools for c++
development; and tools for working with Kattis from the command line.

## Warning
* Never put your solutions to kattis problems in a public
  repository. The problems are used for education, by some companies
  and potentially by competitions. By making solutions publicly
  available you may cause all sorts of issues for students (including
  yourself), companies, teachers, etc.
* All software is provided as is, meaning no warranties are provided
  for the correctness. As a result, always backup your work in case
  something goes wrong. The standing recommendation is to use git.


## License
This software is license under MIT license and the 3rd party software
have the following licenses (please check each sub-repository for
complete license information):

| Software | License |
|----------|---------|
| kattis-cli | MIT |

## Dependency
The software in the repository has the following dependencies:

| Software | Version | Optional |
|----------|---------|----------|
| CMake    | >= 3.10 | Required |
| g++      | >= 11.2.0_3* | Required |
| python3  | >= 3.9.10* | Required |
| python request library | >= 2.27.1* | Required |
| clang-tidy | Any | Optional |
| clang-format | Any | Optional |

\* Older versions might work as well.

## Installation
The easiest way of using the skeleton is to either create a fork of
the software (on github) or to add this repository as a branch in some
other git repository (locally or stored on some server). To do the
later one can do as follows:

```Bash
$ mkdir kattis
$ cd kattis
$ git initi
$ git remote add skeleton git@github.com:enikko/kattis_cpp_skeleton.git
$ git fetch skeleton
$ git checkout -b skeleton-repo skeleton/master
$ git checkout -b master
$ git submodule init
$ git submodule update
$ git remote add origin [url-to-private-kattis-repo-at-some-server]
$ git push -u origin master
```

This will create two branches in your local repository. `master` is
the branch you solves kattis problems on and stores
them. `skeleton-repo` is a branch that is set to track this
repo. Essentially, this is a convenience branch (that is strictly not
needed since one can rebase directly from the github repo but it might
seem a bit more advanced according to some) for upgrading to any
potential fixes in the skeleton.

The next step is to setup the kattis-cli configuration as described at
(https://open.kattis.com/help/submit).

Assuming that all the dependencies are installed, you are now ready to
start solving problems.

### Updating skeleton
If the skeleton has been updated, you can easily update your own local
copy by either doing a merge or a rebase. This is done as follows
(assuming you have done an installation as above):

```Bash
$ git checkout skeleton-repo
$ git pull
$ git checkout - # To to the previous branch
$ git rebase skeleton-repo # or git merge
```

After this you'll have to solve the merge conflicts that might have
happened.

## Usage
To show the usage of the skeleton, we will demonstrate how one can
solve the hello problem in kattis (of course without giving away the
code). This guide assumes that you have installed the skeleton as
above and the current directory is the git root.

The first step is to edit the `.skeleton_config` file so that the name
and the email is correct. Thereafter, we instantiate the skeleton for
our problem using the script `new_problem` that takes the kattis id of
the problem as an argument:

```bash
$ ./bin/new_problem hello
```

This will create a new folder named `hello` under the `src/problems/`
folder. In it there is an empty folder called `tests`. Normally, this
folder will contain all the test cases that the script was able to
fetch from the problem description. However, in the case of this
problem (and potentially other problems as well), it failed to extract
any test cases. Please also note that the fetching process is
heuristically, which means that there may be errors in the fetched
test cases (none has been reported so far but it is a
possibility). For the hello problem there is only one relevant test
case so we can add it manually:

```bash
$ touch src/problems/hello/tests/0.in
$ echo -n Hello World! > src/problems/hello/tests/0.ans
```

This creates an empty dummy input and a file containing the expected output.

In addition to creating the tests folder, the
`problem_skeleton.cpp` has been instantiated (using the data in
`.skeleton_config`) to the file `src/problems/hello/hello.cpp`. The
next step is to add the code to solve the problem. Then we are ready
to build, test and submit the solution as follows:

```bash
$ echo add_problem(hello) >> CMakeLists.txt
$ mkdir build && cd build
$ cmake ..
$ make hello
$ ctest -R hello
```

In the order of the commands this does:

1. Add the hello problem to the cmake file to tell us that it should
   be built and to generate test instructions for it. Note that this
   can also be done by simply adding the `add_problem(hello)` to the
   `CMakeLists.txt` file in any editor.
2. Create a build directory and move to it.
3. Run cmake to generate all the build files.
4. Build the hello program that solves the hello problem.
5. Run the local tests for the hello program and if the all pass
   submit the problem.

## Updating problem and library skeletons
One of the features of the kattis cpp skeleton is the problem and
library skeleton files that are used to instantiate a new problem or
library file. Instantiating a skeleton file is done with:

```bash
$ ./bin/instantiate_skeleton (problem|library) PROJECT_ROOT_DIR
(problem_id|libary_name)
```

Running this script will create a copy of the `problem_skeleton.cpp`
file in the folder for the provided problem id, if the specified type
is problem, and otherwise `library_skeleton.hpp` in the `src/include`
directory. The generated file will have all the occurrences on the
form `{{some.config.path}}` replaced with the corresponding value in
the `.skeleton_config` file. For example, if the string
`{{Author.name}}` exists in the skeleton file then the it will be
replaced with the  value of `name` in the section `Author` in the
`.skeleton_config` file.

The `.skeleton_config` file should be a valid, case sensitive, python
configuration file
((https://docs.python.org/3/library/configparser.html)) but otherwise
there are no restrictions on the file. Furthermore, the values
`{{Library.name}}` and `{{Problem.name}}` are set by the scripts and
does not have to be present in the config file.
