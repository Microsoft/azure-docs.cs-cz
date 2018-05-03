
Pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) vytvořte aplikaci v plánu služby App Service `myAppServicePlan`. 

Tato webová aplikace poskytuje prostor pro hostování vašeho rozhraní API a adresu URL, na které si můžete nasazenou aplikaci zobrazit.

V následujícím příkazu nahraďte *\<app_name >* jedinečným názvem. Pokud název `<app_name>` není jedinečný, zobrazí se chybová zpráva „Web se zadaným názvem <název_aplikace> již existuje“. Výchozí adresa URL webové aplikace je `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Po vytvoření webové aplikace se v rozhraní příkazového řádku Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```