# DNPM:DIP dev env

Simplified docker based DNPM:DIP environment for development purposes

## Start environment

The default configuration uses HTTP port `80` on `localhost`.

```
docker compose up
```

## Web-UI and other useful REST-URLs

To use the web based UI visit http://localhost/ after docker compose started all services.
Login with username `admin` and password `devpass` - or username/password you have set in `dev.env`.

Other helpful URIs with examples:

### Request generated test data

Use http://localhost/api/mtb/fake/data/patient-record to request generated test data for MTB patient records and save
the generated data into file named `fakedata.json`.

```
curl \
  http://localhost/api/mtb/fake/data/patient-record \
  -o fakedata.json
```

### Send a patient record

Use http://localhost/api/mtb/etl/patient-record to send MTB patient records

```
curl \
  -d @fakedata.json \
  -H "Content-Type: application/json+v2" \
  http://localhost/api/mtb/etl/patient-record
```

To pretty print the validation result, use `jq` (if installed):

```
curl \
  -d @fakedata.json \
  -H "Content-Type: application/json+v2" \
  http://localhost/api/mtb/etl/patient-record \
  | jq
```

This will print out something like

```
{
  "patient": "cb15196a-1191-492e-ab94-c50a52e60a78",
  "issues": [
    ... Reported issues ...
  ]
}
```

### Delete a patient record

Use http://localhost/api/mtb/etl/patient-record/{patient} to delete a patient record using the `patient`-ID from
the validation response above.

```
curl \
  -X DELETE \
  http://localhost/api/mtb/etl/patient/cb15196a-1191-492e-ab94-c50a52e60a78
```

## Configuration (Optional)

Edit file `dev.env` (or `dev-ssl.env` for HTTPS) as required. Default values for HTTP are:

```
DEV_PORT=80
DEV_BASE_URL=http://localhost:${DEV_PORT}
DEV_USERNAME=admin
DEV_PASSWORD=devpass
```

Start environment using modified `dev.env` file:

```
docker compose --env-file dev.env up
```

## HTTPS based configuration

To start with encrypted HTTPS use:

```
docker compose --env-file dev-ssl.env up
```

This will use self signed SSL-certificates for `localhost` from `config/ssl-dev-fixture`.
