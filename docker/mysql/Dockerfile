FROM mysql:8.0

# NOTE: relative to build context, which is assumed to be relative to the root of this git repo
# copy the mysql dump(s) to the location that the image uses to initialize database $MYSQL_DATABASE
COPY ./sqldumps/ /docker-entrypoint-initdb.d/
