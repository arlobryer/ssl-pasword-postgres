# Secure certificate and password only Postgres in Docker


# Prerequisites

You'll need to have Docker installed and be minimally familiar with how to use it. This is one of the main departures from the guide above which doesn't properly enforce certificates from the server side as far as I can tell.

You'll need to set up certificates. To do this use [certstrap](https://github.com/square/certstrap) for an easier time. This can be installed with Brew on a Mac.

Run the following from within the `certs` directory to generate your Certificate Authority (CA):

- `certstrap init --common-name myCA`

This will generate the following:
- `myCA.crt` - the CA certificate
- `myCA.key` - the CA certificate key that is used to sign certificate requests
- `myCA.crl` - the Certificate Revocation List (a list of revoked certificates)


## Server Certificates
Next, request some certificate key pairs from the CA:
```
certstrap request-cert --common-name postgresdb
certstrap sign postgresdb --CA myCA
```

These certificates will be the server side certs. See `postgresql.conf` for how they're used and set up.

## Client Certificates
`pg_hba.conf` forces all clients to connect with both a certificate and password. Do the following to generate a certificate for the `postgres` user (basically a repeat of the above):

```
certstrap request-cert --common-name postgres
certstrap sign postgres --CA myCA
```

Change the permissions on the certificte as required:
`chmod 0600 <path to certificate>/postgres.key`

Note that the cn (Common Name) of the client certificate _must_ be the same as the username. See https://www.postgresql.org/docs/current/auth-cert.html.
## Building / starting / connecting to the container
From scratch, you should be able to just `docker-compose up` the first time.

To rebuild everything (rather than just restart:

```docker-compose build --no-cache```

Connect to the running database using:

```psql "host=localhost port=5433  user=postgres password=postgres sslcert=<path to>/postgres.crt sslkey=<path to>/postgres.key"```


### Credits

 https://dev.to/danvixent/how-to-setup-postgresql-with-ssl-inside-a-docker-container-5f3




