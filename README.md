# test
#!/bin/bash

# define log file path
LOG_FILE=$(date "+%Y-%m")".txt"

echo -e "\n"$(date "+%Y-%m-%d %H:%M:%S")", Below files start to upload to HKCDGW Server:" >> $LOG_FILE

# define files which need to upload to HK CDGW Server
local_path=/tmp/files
local_files=$local_path"/*.ref"
remote_path=/tmp/files

# loop the files that need to upload
for file in $local_files
do
  file_size=`ls -l $file TEC | awk '{print $5}'`
  last_modify_timestamp=`stat -c %Y $file`
  
  echo $file `date '+%Y-%m-%d %H:%M:%S' -d @$last_modify_timestamp`", Size:" $file_size >> $LOG_FILE
done

# Upload the files to servers
sftp cgiec@202.104.69.231 <<EOF

lcd $local_path
cd $remote_path

put *.ref
quit

EOF

# Check the upload status
if [ $? -eq 0 ];then
  echo 'Upload files to HKCDGW server successful!' >> $LOG_FILE
else
  echo 'Upload files to HKCDGW server FAIL, please check!' >> $LOG_FILE
fi
