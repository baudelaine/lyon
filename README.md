# Backup

## Set environments

```
APIKEY=dI_zq3flnS-c467NgdHqxJqkMdqTd5JMhN_fkukIT2u8
URL=https://gateway-fra.watsonplatform.net/discovery/api
CONF_FILE=configurations.json
```

## Configurations

### Get environment_id

```
ENVID=$(curl -u 'apikey:'${APIKEY} ${URL}/v1/environments?version=2019-04-30 | jq -r '.environments[] | select(.name=="byod") | .environment_id')
```

### Get configurations

```
echo "[]" > ${CONF_FILE}
```

```
for CONFID in $(curl -u 'apikey:'${APIKEY} ${URL}/v1/environments/${ENVID}/configurations?version=2019-04-30 | jq -r '.configurations[].configuration_id'); do CONFDETAIL=$(curl -u 'apikey:'${APIKEY} ${URL}/v1/environments/${ENVID}/configurations/$CONFID/?version=2019-04-30); jq --argjson CD "$CONFDETAIL" '.[. | length] |= . + $CD' ${CONF_FILE} | sponge ${CONF_FILE}; done
```

```
jq '. | length' ${CONF_FILE}
```

## Training data

```
TD_FILE=training-data.json
```

### Get the training data

```
echo "[]" > ${TD_FILE}
```

```
for COLLID in $(curl -u 'apikey:'${APIKEY} ${URL}/v1/environments/${ENVID}/collections?version=2019-04-30 | jq -r '.collections[].collection_id'); do TD=$(curl -u 'apikey:'${APIKEY} ${URL}/v1/environments/${ENVID}/collections/$COLLID/training_data?version=2019-04-30); jq --argjson td "$TD" '.[. | length] |= . + $td' ${TD_FILE} | sponge ${TD_FILE};done
```



## Queries

	$curl -u 'apikey:'${APIKEY} ${URL}/v1/logs?version=2019-04-30



# Connect to Discovery

	ACTION_URL=https://eu-de.functions.cloud.ibm.com/api/v1/namespaces/sebastien.gautier%40fr.ibm.com_dev/actions/action0?blocking=true
	ACTION_CRED=bf132e20-7...:q14V6XEw...
	ACTION_OUTPUT=action0-output.json

```
cat > param.json << EOF
{
"collection_id": "228451b6-978c-4662-9875-4a5242a458bd",
"environment_id": "0533ae0e-8529-4c9f-8fdc-26a90a6171bc",
"iam_apikey": "dI_zq3flnS-c....",
"url": "https://gateway-fra.watsonplatform.net/discovery/api",
"input": "qu'est ce qu'une donnée à caractère personnel ?"
}
EOF
```

	curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -d @params.json   -u ${ACTION_CRED} -X POST ${ACTION_URL} | jq .response.result.passages[].passage_text | tee ${ACTION_OUTPUT}



