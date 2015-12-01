FROM tutum/mongodb
RUN apt-get update && apt-get install -y wget

WORKDIR /root
RUN wget -O dataset.json https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/dataset.json

VOLUME /data/db /data/log
RUN echo "mongod --fork --smallfiles --logpath /data/log/log.txt --dbpath /data/db" >> server.sh
RUN echo "mongoimport --db test --collection restaurants --drop --file dataset.json" >> server.sh

RUN chmod +x server.sh

EXPOSE 27017 28017
ENTRYPOINT ./server.sh && /bin/bash
