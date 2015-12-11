FROM tutum/mongodb

WORKDIR /root

RUN apt-get update && apt-get install -y wget
RUN wget -O dataset.json https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/dataset.json

ADD conf1c.conf /root/conf1c.conf
ADD conf2c.conf /root/conf2c.conf
ADD conf3c.conf /root/conf3c.conf
ADD conf1s.conf /root/conf1s.conf
ADD conf2s.conf /root/conf2s.conf

VOLUME  /data/db1c /data/db2c /data/db3c /data/db1s /data/db2s
EXPOSE  27017 28017 27018 28018 27019 28019
EXPOSE  27020 28020
EXPOSE  27021 28021 27022 28022

RUN echo "mongod --config conf1c.conf ; mongod --config conf2c.conf ; mongod --config conf3c.conf " > servers.sh 
RUN echo "mongos  --configdb localhost:27017,localhost:27018,localhost:27019 --port 27020  --logpath /var/log/mongodb/mongos.log &" >> servers.sh
RUN echo "mongod --config conf1s.conf ; mongod --config conf2s.conf" >> servers.sh 

RUN echo "sh.addShard(\"localhost:27021\");" > init.js
RUN echo "sh.addShard(\"localhost:27022\");" >> init.js
RUN echo "sh.enableSharding(\"test\");" >> init.js
RUN echo "sh.shardCollection(\"test.restaurants\", { \"borough\" : 1 });" >> init.js
RUN echo "sh.splitAt(\"test.restaurants\", { \"borough\" : \"D\" });" >> init.js

RUN echo "sleep 5; mongo --port 27020 init.js" >> servers.sh

RUN echo "sleep 10; mongoimport --db test --collection restaurants --port 27020 --file dataset.json" >> servers.sh

RUN chmod +x servers.sh

ENTRYPOINT ./servers.sh && /bin/bash
