FROM nginxinc/nginx-unprivileged

ENV UID=1001 \
    GID=1001 \
    USER=nginx \
    GROUP=nginx


WORKDIR /app/
COPY ./app .

COPY ./default.conf /etc/nginx/conf.d/default.conf

EXPOSE 8000/tcp
USER ${UID}:${GID}

CMD ["nginx", "-g", "daemon off;"]
