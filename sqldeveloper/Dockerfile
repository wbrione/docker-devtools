FROM oraclelinux:9-slim

WORKDIR /app

ARG REP_URL="https://yum.oracle.com/repo/OracleLinux/OL9/developer/EPEL/x86_64/"

ARG PKG_URL="https://download.fileeagle.com/files/2023/06/sqldeveloper-23.1.0.097.1607.noarch.rpm"

ENV XML_NS="http://openbox.org/3.4/rc"

ENV APP_TITLE="Oracle SQL Developer"

ENV VNC_PASSWORD="password"

ENV GEOMETRY="1920x1080"

ENV DISPLAY=":0"

RUN microdnf update && microdnf install -y yum-utils && yum-config-manager --add-repo=${REP_URL} && \
    microdnf install -y java-11-openjdk java-11-openjdk-devel openbox tigervnc-server novnc wget xmlstarlet && \
    wget ${PKG_URL} -O sqldeveloper.rpm && rpm -i sqldeveloper.rpm && rm sqldeveloper.rpm && \
    microdnf remove -y yum-utils wget

RUN echo 'test ! -d ~/.config/openbox && mkdir -p ~/.config/openbox; test ! -f ~/.config/openbox/rc.xml && \
    xmlstarlet ed -N a=${XML_NS} -s "//a:applications" -t elem -n "application" /etc/xdg/openbox/rc.xml | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t attr -n "title" -v "${APP_TITLE}" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "maximized" -v "yes" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "decor" -v "no" | \
    xmlstarlet ed -d "//comment()" > ~/.config/openbox/rc.xml; \
    printf "${VNC_PASSWORD}\n${VNC_PASSWORD}\n\n" | vncpasswd && \
    test ! -f ~/.vnc/xstartup && vncserver ${DISPLAY} && vncserver -kill ${DISPLAY}; \
    grep -wq openbox ~/.vnc/xstartup || echo "dbus-launch --exit-with-session openbox" >> ~/.vnc/xstartup; \
    grep -wq "${APP_TITLE}" ~/.config/openbox/rc.xml && grep -wq openbox ~/.vnc/xstartup && \
    vncserver ${DISPLAY} -name "${APP_TITLE}" -geometry ${GEOMETRY} && \
    websockify -D --web=/usr/share/novnc/ 6080 localhost:5900 && \
    /opt/sqldeveloper/sqldeveloper.sh' > startup.sh && chmod +x startup.sh && \

ENTRYPOINT [ "bash", "-c", "/app/startup.sh" ]

EXPOSE 6080