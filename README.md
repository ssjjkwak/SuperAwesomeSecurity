# Super Awesome Security

**We provide back-end server, not front. So you should use api testing tools like postman**

## Image
https://storage.googleapis.com/software_security/ku-security.vmdk. 


You can download the image that all requirements are already installed

- root user : security
- root password : dodam1905!

You can run this image on Virtual Box(We already test it)

**Builded file already exists**

If you want to run this, Move `/home/security/KoreaUniversity_SoftwareSecurity_GIT_CTF/Test`.
Execute `run.sh [network-interface] [port]` & The log is `secure_chat.log`

More details, see **Run part**

## Dependency
**If you download the image, this part isn't needed**
- Docker
- Mysql & postfix(You can use this by build & run script in /resources/docker/)
- openssl 3(https://github.com/openssl/openssl/blob/master/INSTALL.md)
  - Follow the guide in link.
- spdlog(https://github.com/gabime/spdlog)
  - apt install libspdlog-dev
- fmt(https://github.com/fmtlib/fmt)
  1. Clone the repo
  2. `cd fmt` -> `mkdir build` -> `cd build` -> `cmake -DCMAKE_BUILD_TYPE=Release ..` -> `make` -> `sudo make install` 
- cpprestsdk(https://github.com/Microsoft/cpprestsdk/wiki/How-to-build-for-Linux)
  - Follow the guide in link
- g++ 10.3.0(https://launchpad.net/~savoury1/+archive/ubuntu/gcc-10)
  - Follow the guide in link to add ppa-repository
  - `apt-get install g++-10` -> `ln -sf /usr/bin/g++ /usr/bin/g++-10`
- boost(https://www.boost.org/users/history/version_1_80_0.html)
  - Download the files
  - Follow this guide. (https://stackoverflow.com/questions/12578499/how-to-install-boost-on-ubuntu)
- mysql connector/c++ 8.0(https://dev.mysql.com/doc/connector-cpp/8.0/en/connector-cpp-installation-binary.html)
  - Follow the guide in link

## Build
**If you use a virtual machine, and you want to access the server at host, not vm, you should do port-forwading**
- (Notice) You should add current user in `docker` group(Run docker without `sudo`)
  - If you use the image, skip this
- Run `src/cpp/com/security/chat/build.sh`
- Run `src/resource/docker/build.sh & run.sh`
  - When execute `run.sh`, If error messages like "already used name" are noticed,
    1. Check current running containers(`docker ps`)
    2. If "mysql" or "postfix" container are running, execute `docker stop mysql postfix`
    3. Execute `docker container prune`
    4. Execute `./run.sh`
- Check mysql is ready. And then, inject schema in db by `mysql -h [docker-private-ip of mysql] -u security -p chat < resources/sql/table.sql`
  - To find private ip, 
    1. docker network inspect bridge
    2. Check container -> the ip of mysql. It may be `172.17.0.x`
  - You can easily check whether mysql is running or not,
    1. `docker exec -it mysql /bin/bash`
    2. `mysql -u security -p` -> password: 1123
    3. If you enter mysql shell, then ok!
- Check DB_HOST in `run.sh`
  - DB_HOST is your "docker-private-ip of mysql"
  
## Run
- Run `src/cpp/com/security/chat/run.sh [network interface] [port]` like `run.sh lo 9000`
  - If you want to access apis at host, you should specify [non-loop-back interface] & port-forwarding VM with host
- You should check mysql & postfix container are running. See **Build** part
- You can access apis in the virtual machine

## Test
**https://[your-ip-address]:[your-port]/[some-path]**

- Check the api in `src/resource/api.json`
- You can test them via some tools like **postman**
  - You can show well-formed api docs when you import this in **postman**
  - If you want to use some browser, we recommand **firefox** 
- Initial company name & password is 'company', '1123'.

## Database
You can show database information in `run.sh`
db name : chat
db user : security
db password : 1123

You can also the schema in resources/sql/table.sql files

## Runtime Environment
You can access environment by `ssh ctf@34.64.114.124`
All requirements are already installed.

user : ctf
password: security

## Test API Server opens, `https://34.64.114.124:9000`
  - You can login by the api
  ```
  POST /auth/login?type=company
  body : 
    {
      "name" : "company",
      "password" : "1123"
     }
     
  Response :
      {
    "code": 200,
    "data": {
        "array": [
            {
                "company": {
                    "id": 1,
                    "name": "company"
                }
            },
            {
                "session": {
                    "id": 11442710240445645052,
                    "token": "GU12vwRsZ4CNuziSM2GqMWWd2QGmkm4VXNL9HpQVEWxo8T0Hatnj4AGDirIE7VSQtkZUC8gTDe3E6L7eQCpzROV5TWskm6nUwbxO50MEZsn8VOhKOjwdwxSImlQZiZF7jj8OXXmCtp768cGPbG3gDy1DxVFtGs16r6Muqf7kPd4hOML5j1QX02GJ9KkuALNTICydB8kLNUgYAgfJlFjsKj8gXuVeO1sDRzXbIfw4wGr3F80ebWJKUTZXsg1dKMPV"
                }
            }
        ]
    },
    "message": "AuthController[LOGIN](/auth/login?type=company) : ok"
    }
  ```
  **See api.json**
