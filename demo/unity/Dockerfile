FROM ubuntu:16.04

ENV LOG_FILE="/home/unity/server.log"

RUN useradd -ms /bin/bash unity
WORKDIR /home/unity

COPY server .
COPY server_Data ./server_Data
RUN chown unity:unity ./server && chown -R unity:unity ./server_Data && chmod 777 server && chmod +x ./server
USER unity

ENTRYPOINT ["./server", "-logFile", "/dev/stdout"]
