| <h1 style="font-size:60px;">Run Docker in Google Colab</h1> |
|:-:|
| <img src="assets/docker-in-colab.gif" width="66%"> |

## Just Copy-Paste-Run

```python
# Copyright 2024 Drengskapur
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# @title {display-mode:"form"}
# @markdown <br/><br/><center><img src="https://cdn.jsdelivr.net/gh/drengskapur/docker-in-colab/assets/docker.svg" height="150"><img src="https://cdn.jsdelivr.net/gh/drengskapur/docker-in-colab/assets/colab.svg" height="150"></center><br/>
# @markdown <center><h1>Docker in Colab</h1></center><center>github.com/drengskapur/docker-in-colab<br/><br/><br/><b>udocker("run hello-world")</b></center><br/>
def udocker_init():
    import os
    if not os.path.exists("/home/user"):
        !pip install udocker > /dev/null
        !udocker --allow-root install > /dev/null
        !useradd -m user > /dev/null
    print(f'Docker-in-Colab 1.1.0\n')
    print(f'Usage:     udocker("--help")')
    print(f'Examples:  https://github.com/indigo-dc/udocker?tab=readme-ov-file#examples')

    def execute(command: str):
        user_prompt = "\033[1;32muser@pc\033[0m"
        print(f"{user_prompt}$ udocker {command}")
        !su - user -c "udocker $command"

    return execute

udocker = udocker_init()
```

## Syntax

Docker-in-Colab is built on top of [udocker](https://github.com/indigo-dc/udocker).

Change

```text
udocker [general_options] <command> [command_options] <command_args>
```

to

```text
udocker("[general_options] <command> [command_options] <command_args>")
```

For example, `udocker pull hello-world` becomes `udocker("pull hello-world")`.

```python
udocker("--help")
```

```text
user@pc:/content$ udocker --help

Syntax:
  udocker  [general_options] <command>  [command_options]  <command_args>

  udocker [-h|--help|help]        :Display this help and exits
  udocker [-V|--version|version]  :Display udocker and tarball version and exits

General options common to all commands must appear before the command:
  -D, --debug                   :Debug
  -q, --quiet                   :Less verbosity
  --insecure                    :Allow insecure non authenticated https
  --repo=<directory>            :Use repository at directory
  --allow-root                  :Allow execution by root NOT recommended
  --config=<conf_file>          :Use configuration <conf_file>

Commands:
  --help [command]              :Command specific help
  showconf                      :Print all configuration options

  search <repo/expression>      :Search dockerhub for container images
  pull <repo/image:tag>         :Pull container image from dockerhub
  create <repo/image:tag>       :Create container from a pulled image
  run <container_id|name>       :Execute created container
  run <repo/image:tag>          :Pull, create and execute container

  images -l                     :List container images
  ps -m -s                      :List created containers
  name <container_id> <name>    :Give name to container
  rmname <name>                 :Delete name from container
  rename <name> <new_name>      :Change container name
  clone <container_id>          :Duplicate container
  rm  <container-id|name>       :Delete container
  rmi <repo/image:tag>          :Delete image
  tag <repo/image:tag> <repo2/image2:tag2> :Tag image

  import <tar> <repo/image:tag> :Import tar file (exported by docker)
  import - <repo/image:tag>     :Import from stdin (exported by docker)
  export -o <tar> <container>   :Export container directory tree to file
  export - <container>          :Export container directory tree to stdin
  load -i <exported-image>      :Load image from file (saved by docker)
  load                          :Load image from stdin (saved by docker)
  save -o <imagefile> <repo/image:tag>  :Save image with layers to file

  inspect -p <repo/image:tag>   :Print image or container metadata
  verify <repo/image:tag>       :Verify a pulled image
  manifest inspect <repo/image:tag> :Print manifest metadata

  udocker manifest inspect centos/centos8
  udocker pull --platform=linux/arm64 centos/centos8
  udocker tag centos/centos8  mycentos/centos8:arm64

  protect <repo/image:tag>      :Protect repository
  unprotect <repo/image:tag>    :Unprotect repository
  protect <container>           :Protect container
  unprotect <container>         :Unprotect container

  mkrepo <top-repo-dir>         :Create another repository in location
  setup --execmode=<mode>       :Change container execution mode
  setup --nvidia                :Setup container to use nvidia GPU
  setup --purge                 :clean mountpoints and files created by udocker
  setup --fixperm               :attempt to fix file permissions

  login                         :Login into docker repository
  logout                        :Logout from docker repository

Examples:
  udocker search expression
  udocker search quay.io/expression
  udocker search --list-tags myimage
  udocker pull myimage:mytag
  udocker images
  udocker create --name=mycontainer  myimage:mytag
  udocker ps -m -s
  udocker inspect mycontainer
  udocker inspect -p mycontainer

  udocker manifest inspect centos/centos8
  udocker pull --platform=linux/arm64 centos/centos8
  udocker tag centos/centos8  mycentos/centos8:arm64

  udocker run  mycontainer  cat /etc/redhat-release
  udocker run --hostauth --hostenv --bindhome  mycontainer
  udocker run --user=root  mycontainer  yum install firefox
  udocker run --hostauth --hostenv --bindhome mycontainer  firefox
  udocker run --entrypoint="" mycontainer  /bin/bash -i
  udocker run --entrypoint="/bin/bash" mycontainer -i

  udocker clone --name=anotherc mycontainer
  udocker rm anotherc

  udocker mkrepo /data/myrepo
  udocker --repo=/data/myrepo load -i docker-saved-repo.tar
  udocker --repo=/data/myrepo images
  udocker --repo=/data/myrepo run --user=$USER  myimage:mytag

  udocker export -o myimage.tar mycontainer
  udocker import myimage.tar mynewimage
  udocker create --name=mynewc mynewimage
  udocker export --clone -o mycontainer.tar mycontainer
  udocker import --clone mycontainer.tar

Notes:
 * by default the binaries, images and containers are placed in
      $HOME/.udocker
 * by default the following host directories are mounted in the
   container:
      /dev /proc /sys /etc/resolv.conf /etc/host.conf /etc/hostname
 * to prevent the mount of the above directories use:
      run  --nosysdirs  <container>
 * additional host directories to be mounted are specified with:
      run --volume=/data:/mnt --volume=/etc/hosts  <container>
      run --nosysdirs --volume=/dev --volume=/proc  <container>
 * udocker provides several execution modes that offer different
   approaches and technologies to execute containers, they
   can be selected using the setup command. See the setup help.
      udocker setup --execmode=F3 fedx
      udocker setup --execmode=R1 fedx
      udocker setup --execmode=S1 fedx
      udocker setup --help
 * udocker facilitates the usage of nvidia drivers within containers
      udocker setup --nvidia fedx

See: https://github.com/indigo-dc/udocker/blob/master/SUMMARY.md
```

## References

* Jorge Gomes, Emanuele Bagnaschi, Isabel Campos, Mario David, Luís Alves,
  João Martins, João Pina, Alvaro López-García, Pablo Orviz,
  Enabling rootless Linux Containers in multi-user environments: The udocker tool,
  Computer Physics Communications, Available online 6 June 2018,
  ISSN 0010-4655, <https://doi.org/10.1016/j.cpc.2018.05.021>
