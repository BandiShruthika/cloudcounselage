#!/bin/bash

# Database credentials
DB_USER="admin"  # MongoDB username you created
DB_PASS="password"  #password associated with the MongoDB user
DB_NAME="admin"  #name of the MongoDB database you want to back up

# AWS S3 bucket
S3_BUCKET="mybackupbucketforproject"  

# Timestamp
TIMESTAMP=$(date +"%Y%m%d%H%M%S")

# Backup directory
BACKUP_DIR="/tmp/mongodb-backups"

# Create backup directory if it doesn't exist
mkdir -p $BACKUP_DIR

# Dump MongoDB data
mongodump --username $DB_USER --password $DB_PASS --authenticationDatabase admin --db $DB_NAME --out $BACKUP_DIR

# Compress the backup
tar -zcvf $BACKUP_DIR/$TIMESTAMP.tar.gz $BACKUP_DIR/$DB_NAME

# Upload to S3
aws s3 cp $BACKUP_DIR/$TIMESTAMP.tar.gz s3://$S3_BUCKET/

# Clean up
rm -rf $BACKUP_DIR
