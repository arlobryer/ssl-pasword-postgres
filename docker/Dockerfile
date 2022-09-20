# This Dockerfile contains the image specification of our database
FROM postgres:14.5-alpine

COPY ./certs/out/postgresdb.key /var/lib/postgresql
COPY ./certs/out/postgresdb.crt /var/lib/postgresql

COPY ./certs/out/myCA.crt /var/lib/postgresql
COPY ./certs/out/myCA.crl /var/lib/postgresql

COPY ./pg_hba.conf /config/pg_hba.conf
COPY ./postgresql.conf /config/postgresql.conf

RUN chown 0:70 /var/lib/postgresql/postgresdb.key && chmod 640 /var/lib/postgresql/postgresdb.key
RUN chown 0:70 /var/lib/postgresql/postgresdb.crt && chmod 640 /var/lib/postgresql/postgresdb.crt

RUN chown 0:70 /var/lib/postgresql/myCA.crt && chmod 640 /var/lib/postgresql/myCA.crt
RUN chown 0:70 /var/lib/postgresql/myCA.crl && chmod 640 /var/lib/postgresql/myCA.crl
