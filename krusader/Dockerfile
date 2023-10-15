FROM debian:bullseye-slim

WORKDIR /app

ENV XML_NS="http://openbox.org/3.4/rc"

ENV APP_TITLE="Krusader - ROOT PRIVILEGES"

ENV VPN_PASSWORD="password"

ENV GEOMETRY="1920x1080"

ENV DISPLAY=":0"

RUN apt update && apt upgrade && apt install -y tigervnc-standalone-server dbus-x11 openbox novnc xmlstarlet kate breeze-icon-theme krusader && \
    echo 'if [ ! -f ~/.config/openbox/rc.xml ]; then mkdir -p ~/.config/openbox; \
    xmlstarlet ed -N a=${XML_NS} -s "//a:applications" -t elem -n "application" /etc/xdg/openbox/rc.xml | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t attr -n "title" -v "${APP_TITLE}" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "maximized" -v "yes" | \
    xmlstarlet ed -N a=${XML_NS} -s "//a:application[last()]" -t elem -n "decor" -v "no" | \
    xmlstarlet ed -d "//comment()" > ~/.config/openbox/rc.xml; fi && \
    echo "dbus-launch --exit-with-session openbox" > ~/.vnc/xstartup && chmod +x ~/.vnc/xstartup && \
    printf "${VPN_PASSWORD}\n${VPN_PASSWORD}\n\n" | vncpasswd && \
    vncserver ${DISPLAY} -name "${APP_TITLE}" -geometry ${GEOMETRY} && \
    websockify -D --web=/usr/share/novnc/ 6080 localhost:5900 && \
    /usr/bin/krusader' > startup.sh && chmod +x startup.sh
    
ENTRYPOINT [ "bash", "-c", "/app/startup.sh" ]

EXPOSE 6080