### Запуск чререз docker
```
root@mysql001-otus:~# docker search mongo
NAME                 DESCRIPTION                                     STARS     OFFICIAL
mongo                MongoDB document databases provide high avai…   10504     [OK]
circleci/mongo       CircleCI images for MongoDB                     13
litmuschaos/mongo                                                    1
corpusops/mongo      https://github.com/corpusops/docker-images/     0
uselagoon/mongo                                                      0
ekesken/mongo        docker image for mongo that is configurable …   1
jelastic/mongo       An image of the MongoDB database server main…   0
arm64v8/mongo        MongoDB document databases provide high avai…   36
vepo/mongo           Mongo image with default settings               1
s390x/mongo          MongoDB document databases provide high avai…   1
kubedb/mongo                                                         0
zinobe/mongo         mongo with custom user                          0
sacashgit/mongo                                                      0
winamd64/mongo       MongoDB document databases provide high avai…   1
amd64/mongo          MongoDB document databases provide high avai…   0
excellalabs/mongo    Dockerfile and scripts to setup a production…   2
healthcheck/mongo    https://github.com/docker-library/healthchec…   5
ccitest/mongo        CircleCI test images for Mongo                  0
pastvu/mongo                                                         0
pagarme/mongo                                                        0
cescoferraro/mongo   docker alpine mongo                             0
appsdeck/mongo                                                       0
kapost/mongo                                                         0
settlemint/mongo     A MongoDB 4 image with a replicaset setup sc…   0
quilt/mongo          MongoDB container for quilt.io                  0
root@mysql001-otus:~# docker pull mongo
Using default tag: latest
latest: Pulling from library/mongo
5a7813e071bf: Pull complete
07b085c89153: Pull complete
8170e4b21000: Pull complete
1aa9099a841a: Pull complete
bce7aaa046ee: Pull complete
a53a70bd8c23: Pull complete
9c76b9a0da25: Pull complete
990f24c1b076: Pull complete
Digest: sha256:aaad67f2dca93148e5343c03210bcfc89a0107516a4756bfa018acd6579e5b18
Status: Downloaded newer image for mongo:latest
docker.io/library/mongo:latest

root@mysql001-otus:~# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
mongo         latest    6fe2220a3a52   2 months ago   881MB

root@mysql001-otus:~# docker run -it --rm --name mongodb -d mongo
b5656e55d10f9017037d3d5bafee6ff1494e5deb4b0e74f2e045c4c4e1a4ae94

root@mysql001-otus:~# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS       NAMES
4cc841b5523f   mongo     "docker-entrypoint.s…"   About a minute ago   Up About a minute   27017/tcp   mongodb

root@4cc841b5523f:/# mongosh
Current Mongosh Log ID: 67bc7793a9076a1068544ca6
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.3.8
Using MongoDB:          8.0.4
Using Mongosh:          2.3.8

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-02-24T13:40:12.810+00:00: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine. See http://dochub.mongodb.org/core/prodnotes-filesystem
   2025-02-24T13:40:13.033+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-02-24T13:40:13.033+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-02-24T13:40:13.033+00:00: For customers running the current memory allocator, we suggest changing the contents of the following sysfsFile
   2025-02-24T13:40:13.033+00:00: We suggest setting the contents of sysfsFile to 0.
   2025-02-24T13:40:13.033+00:00: Your system has glibc support for rseq built in, which is not yet supported by tcmalloc-google and has critical performance implications. Please set the environment variable GLIBC_TUNABLES=glibc.pthread.rseq=0
   2025-02-24T13:40:13.033+00:00: vm.max_map_count is too low
   2025-02-24T13:40:13.033+00:00: We suggest setting swappiness to 0 or 1, as swapping can cause performance problems.
------

test>
```
### Запуск чререз docker-compose + mongo-express
```root@mysql001-otus:~# cat mongo.yml
#version: '3.7'
services:
  mongodb:
    image: mongo
    container_name: mongodb
    ports:
      - 27017:27017
    volumes:
      - ./mongodata:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: m_admin
      MONGO_INITDB_ROOT_PASSWORD: m_admin
    restart: always

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: m_admin
      ME_CONFIG_MONGODB_ADMINPASSWORD: m_admin
      ME_CONFIG_MONGODB_URL: mongodb://m_admin:m_admin@mongodb:27017
```
```
root@mysql001-otus:~# docker-compose -f mongo.yml up -d
[+] Running 3/3
 ✔ Network root_default            Created                                                                                                                                                   0.1s
 ✔ Container mongodb               Started                                                                                                                                                   0.3s
 ✔ Container root--1  Started

root@mysql001-otus:~# docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                                             NAMES
c48e5ef1b11f   mongo           "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   mongodb
62597499d9b8   mongo-express   "/sbin/tini -- /dock…"   7 minutes ago   Up 7 minutes   0.0.0.0:8081->8081/tcp, [::]:8081->8081/tcp       root-mongo-express-1
```
![image](https://github.com/dvlonkin/database_otus/blob/c9e07401c2cbbb7b97191ef64db9d58de2271f8b/Screenshot%202025-02-24%20181206.png)

### Загрузка данных и поиск
```
db.movie.insert([
{"_id":11,"title":"Son of the Pink Panther","year":1993,"cast":["Roberto Benigni","Herbert Lom","Claudia Cardinale"],"genres":["Comedy"]},
{"_id":12,"title":"The Dentist 2","year":1998,"cast":["Corbin Bernsen","Jillian McWhirter"],"genres":["Horror"]}])
{"_id":13,"title":"Desert Blue","year":1998,"cast":["Brendan Sexton III","Kate Hudson","Casey Affleck","Christina Ricci"],"genres":["Comedy","Drama"]},
{"_id":14,"title":"Ted","year":2012,"cast":["Mark Wahlberg","Mila Kunis","Seth MacFarlane"],"genres":["Comedy"]},
{"_id":15,"title":"The Contractor","year":2013,"cast":["Danny Trejo","Brad Rowe","Christina Cox"],"genres":["Action"]},
{"_id":16,"title":"Need for Speed","year":2014,"cast":["Aaron Paul","Dominic Cooper","Imogen Poots","Kid Cudi"],"genres":["Action"]}])
```
```
muvie> db.movie.find({"genres": "Action"})
[
  {
    _id: 15,
    title: 'The Contractor',
    year: 2013,
    cast: [ 'Danny Trejo', 'Brad Rowe', 'Christina Cox' ],
    genres: [ 'Action' ]
  },
  {
    _id: 16,
    title: 'Need for Speed',
    year: 2014,
    cast: [ 'Aaron Paul', 'Dominic Cooper', 'Imogen Poots', 'Kid Cudi' ],
    genres: [ 'Action' ]
  }
]
muvie> db.movie.find({}, {"title" : 1, _id : 0} ).sort({"title": 1})
[
  { title: 'Desert Blue' },
  { title: 'Need for Speed' },
  { title: 'Son of the Pink Panther' },
  { title: 'Ted' },
  { title: 'The Contractor' },
  { title: 'The Dentist 2' }
]

muvie> db.movie.find({$and :[{"year" : 2013}, {"genres": "Action"}]})
[
  {
    _id: 15,
    title: 'The Contractor',
    year: 2013,
    cast: [ 'Danny Trejo', 'Brad Rowe', 'Christina Cox' ],
    genres: [ 'Action' ]
  }
]
```
```
muvie> db.movie.update({"year" : {$gt: 2000}}, {$set: {"rating": 5.5}}, {multi : true} )
DeprecationWarning: Collection.update() is deprecated. Use updateOne, updateMany, or bulkWrite.
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 3,
  modifiedCount: 3,
  upsertedCount: 0
}
```
