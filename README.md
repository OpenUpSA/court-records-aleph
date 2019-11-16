# Deploy guide for OpenCourts.org.za

'aleph.env' contains prod deploy specific secrets.

1. `docker-compose.yml` and `aleph.env` for 'Aleph' in directory
2. Set up the database and search indexes: `docker-compose run --rm shell aleph upgrade`
2. `docker-compose up -d` exposes Aleph on port 8080
3. Nginx reverse proxy with LetsEncrypt on https://opencourts.org.za
4. Alephdata is configured to upload files to S3 in `aleph.env` this requires CORS bucket permissions to be set:

SHOW CORS: `aws s3api get-bucket-cors --bucket opencourts`

SET CORS: `aws s3api put-bucket-cors --bucket opencourts --cors-configuration file://cors.json`

cors.json:
```
{
  "CORSRules": [
    {
      "AllowedOrigins": ["https://opencourts.org.za"],
      "AllowedHeaders": ["*"],
      "AllowedMethods": ["PUT", "POST", "DELETE"],
      "MaxAgeSeconds": 3000,
      "ExposeHeaders": ["x-amz-server-side-encryption"]
    },
    {
      "AllowedOrigins": ["*"],
      "AllowedHeaders": ["Authorization"],
      "AllowedMethods": ["GET"],
      "MaxAgeSeconds": 3000
    }
  ]
}
```

