FROM debian:bullseye-slim

WORKDIR /app

ARG XML_NS="http://openbox.org/3.4/rc"

ENV APP_TITLE="aMule"

ENV APP_USER="amule"

ENV APP_GROUP="amule"

ENV APP_UID="1000"

ENV APP_GID="1000"

ENV APP_DATA="/data"

ENV APP_HOME="${APP_DATA}/.aMule"

ENV VNC_PASSWORD="password"

ENV GEOMETRY="1920x1080"

ENV DISPLAY=":0"

RUN apt update && apt upgrade -y && apt install -y tigervnc-standalone-server dbus-x11 openbox novnc xmlstarlet

RUN apt install -y sudo amule

RUN echo "dbus-launch --exit-with-session openbox" > xstartup && chmod +x xstartup && \
    xmlstarlet ed -N a=${XML_NS} -s "//a:applications" -t elem -n "application" /etc/xdg/openbox/rc.xml | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t attr -n "title" -v "${APP_TITLE}" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "maximized" -v "yes" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "decor" -v "no" | \
    xmlstarlet ed -d "//comment()" > rc.xml && apt remove --auto-remove -y xmlstarlet && \
    cp -p /usr/share/novnc/vnc.html /usr/share/novnc/index.html

RUN groupadd -g ${APP_GID} ${APP_GROUP} && useradd -d ${APP_DATA} -u ${APP_UID} -g ${APP_GID} ${APP_USER}

RUN echo 'set -x; \
    id -u ${APP_USER} | grep -qw ${APP_UID} || usermod -u ${APP_UID} ${APP_USER} || exit 1; \
    id -g ${APP_USER} | grep -qw ${APP_GID} || usermod -g ${APP_GID} ${APP_GROUP} && groupmod -g ${APP_GID} ${APP_GROUP}; \
    CFG_HOME="$(getent passwd ${APP_USER} | cut -d: -f6)"; \
    test ! -d ${APP_HOME} && mkdir -p ${APP_HOME}; \
    test ! -d ${CFG_HOME}/.vnc && mkdir -p ${CFG_HOME}/.vnc; \
    test ! -d ${CFG_HOME}/.config/openbox && mkdir -p ${CFG_HOME}/.config/openbox; \
    test ! -f ${CFG_HOME}/.vnc/xstartup && cp /app/xstartup ${CFG_HOME}/.vnc/; \
    test ! -f ${CFG_HOME}/.config/openbox/rc.xml && cp /app/rc.xml ${CFG_HOME}/.config/openbox/; \
    chown -Rh ${APP_UID}:${APP_GID} ${APP_HOME} ${CFG_HOME} && \
    printf "${VNC_PASSWORD}\n${VNC_PASSWORD}\n\n" | sudo -u ${APP_USER} vncpasswd && \
    sudo -u ${APP_USER} vncserver ${DISPLAY} -name "${APP_TITLE}" -geometry ${GEOMETRY} && \
    sudo -u ${APP_USER} websockify -D --web=/usr/share/novnc/ 6080 localhost:5900 || \
    exit 1;' > startup.sh && chmod +x startup.sh

RUN echo 'sudo -u ${APP_USER} /usr/bin/amule' >> startup.sh

ENTRYPOINT [ "bash", "-c", "/app/startup.sh" ]

EXPOSE 6080
EXPOSE 4662
EXPOSE 4672
