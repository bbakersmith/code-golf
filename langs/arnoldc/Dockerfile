FROM adoptopenjdk/openjdk16:x86_64-debian-jdk-16.0.1_9-slim as builder

RUN mkdir /empty

RUN jlink                                 \
    --add-modules java.base,jdk.compiler  \
    --compress=2                          \
    --module-path /opt/java/openjdk/jmods \
    --output /opt/jdk

RUN curl http://lhartikk.github.io/ArnoldC.jar > /usr/lib/ArnoldC.jar

FROM alpine:3.14

RUN apk add --no-cache bash

FROM scratch

COPY --from=0 /bin/cat /bin/rm            /bin/
COPY --from=0 /lib/x86_64-linux-gnu       /lib/x86_64-linux-gnu/
COPY --from=0 /lib64/ld-linux-x86-64.so.2 /lib64/
COPY --from=0 /opt/jdk                    /opt/jdk
COPY --from=0 /empty                      /proc
COPY --from=0 /empty                      /tmp
COPY --from=0 /usr/lib/locale             /usr/lib/locale/
COPY --from=0 /usr/lib/ArnoldC.jar        /usr/lib/

# bash is used for the /usr/bin/java script. Pattern copied from java lang Dockerfile.
COPY --from=1 /bin/bash                 /bin/
COPY --from=1 /lib/ld-musl-x86_64.so.1  /lib
COPY --from=1 /usr/lib/libncursesw.so.6 \
              /usr/lib/libreadline.so.8 /usr/lib/

COPY arnoldc /usr/bin/

ENTRYPOINT ["/usr/bin/arnoldc"]

CMD ["--version"]
