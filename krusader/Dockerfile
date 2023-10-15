FROM debian:bullseye-slim

WORKDIR /app

ENV XML_NS="http://openbox.org/3.4/rc"

ENV APP_TITLE="Krusader - ROOT PRIVILEGES"

ENV VNC_PASSWORD="password"

ENV GEOMETRY="1920x1080"

ENV DISPLAY=":0"

RUN apt update && apt upgrade && apt install -y tigervnc-standalone-server dbus-x11 openbox novnc xmlstarlet kate breeze-icon-theme krusader

RUN echo 'test ! -d ~/.vnc && mkdir ~/.vnc; test ! -d ~/.config/openbox && mkdir -p ~/.config/openbox; \
    test ! -f ~/.vnc/xstartup && echo "dbus-launch --exit-with-session openbox" > ~/.vnc/xstartup && chmod +x ~/.vnc/xstartup; \
    test ! -f ~/.config/openbox/rc.xml && \
    xmlstarlet ed -N a=${XML_NS} -s "//a:applications" -t elem -n "application" /etc/xdg/openbox/rc.xml | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t attr -n "title" -v "${APP_TITLE}" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "maximized" -v "yes" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "decor" -v "no" | \
    xmlstarlet ed -d "//comment()" > ~/.config/openbox/rc.xml; \
    printf "${VNC_PASSWORD}\n${VNC_PASSWORD}\n\n" | vncpasswd && \
    grep -wq "${APP_TITLE}" ~/.config/openbox/rc.xml && grep -wq openbox ~/.vnc/xstartup && \    
    vncserver ${DISPLAY} -name "${APP_TITLE}" -geometry ${GEOMETRY} && \
    websockify -D --web=/usr/share/novnc/ 6080 localhost:5900 && \
    /usr/bin/krusader' > startup.sh && chmod +x startup.sh
    
ENTRYPOINT [ "bash", "-c", "/app/startup.sh" ]

EXPOSE 6080