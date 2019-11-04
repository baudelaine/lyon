# Backup

## Set environments

```
APIKEY=
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
