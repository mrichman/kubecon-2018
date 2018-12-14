# Slides from Kubecon 2018

I downloaded all the slides using this script:

```bash
#!/bin/bash

mkdir kubecon
DAYS=("2018-12-11" "2018-12-13" "2018-12-12")
for DAY in "${DAYS[@]}"; do
  #Super shitty pipefest because of grep matched groups sadness
  LINKS=($(curl https://kccna18.sched.com/${DAY}/overview | grep -oEi "f='(.*)' cl" | cut -d\' -f 2 | tr '\n' ' '))
  for LINK in "${LINKS[@]}"; do
    echo "Requesting https://kccna18.sched.com${LINK}"
    #Find file link on event page
    FILE_URL=$(curl -s https://kccna18.sched.com${LINK} | grep "file-uploaded" | cut -d\" -f 4)
    #If there's an uploaded file on the page, download it
    if [ -n "${FILE_URL}" ]; then
      FILENAME=${LINK#https:*}
      FILENAME="$DAY-${FILENAME##/*/}.${FILE_URL##*.}"
      curl -o "kubecon/${FILENAME}" -s "${FILE_URL}"
    fi
  done
done
```

