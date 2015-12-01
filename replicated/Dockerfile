FROM tutum/mongodb

WORKDIR /root

RUN apt-get update && apt-get install -y wget
RUN wget -O dataset.json https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/dataset.json

ADD config1.conf /root/config1.conf
ADD config2.conf /root/config2.conf
ADD config3.conf /root/config3.conf

VOLUME  /data/db1 /data/db2 /data/db3
EXPOSE 27017 28017 27018 28018 27019 28019

ADD init.sh /root/init.sh
RUN chmod +x init.sh

RUN echo "rs.initiate()" >> init0.js

RUN echo "mongod --config config1.conf ; mongod --config config2.conf ; mongod --config config3.conf " > servers.sh 
RUN echo "mongo --port 27017 init0.js" >> servers.sh
RUN echo "sleep 7 ; mongoimport --port 27017 --db test --collection restaurants --file dataset.json" >> servers.sh

# FIX: for sole reason shards are 'forgotten' after mongoimport
#      that's why we need to initiate, import then shard
RUN echo "./init.sh" >> servers.sh
RUN echo "sleep 7 ; mongo --port 27017 init.js" >> servers.sh

RUN chmod +x servers.sh
ENTRYPOINT  ./servers.sh && /bin/bash
