## <a name="rest"></a>Nasadit soubor ZIP pomocí rozhraní REST API 

Můžete použít [službu pro nasazení rozhraní REST API](https://github.com/projectkudu/kudu/wiki/REST-API) k nasazení souboru ZIP do vaší aplikace v Azure. Pokud chcete nasadit, poslat https://<app_name>.scm.azurewebsites.net/api/zipdeploy požadavek POST. Požadavek POST musí obsahovat soubor .zip v textu zprávy. Přihlašovací údaje nasazení pro vaši aplikaci jsou uvedeny v požadavku pomocí ověřování HTTP BASIC. Další informace najdete v tématu [odkaz nasazení nabízené .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Pro ověřování HTTP BASIC budete potřebovat přihlašovací údaje nasazení služby App Service. Chcete-li zjistit, jak nastavit přihlašovací údaje nasazení, přečtěte si téma [nastavit a resetovat přihlašovací údaje individuální](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Pomocí cURL

Následující příklad používá nástroj cURL nasazovat soubor .zip. Nahraďte zástupné symboly `<username>`, `<password>`, `<zip_file_path>`, a `<app_name>`. Po zobrazení výzvy cURL, zadejte heslo.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Tento požadavek se aktivuje nabízené nasazení z odeslaného souboru ZIP. Aktuální a starší nasazení můžete zkontrolovat pomocí https://<app_name>.scm.azurewebsites.net/api/deployments koncového bodu, jak je znázorněno v následujícím příkladu cURL. Znovu, nahraďte `<app_name>` s názvem vaší aplikace a `<deployment_user>` uživatelským jménem přihlašovací údaje nasazení.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) poslat žádost, která obsahuje soubor .zip. Nahraďte zástupné symboly `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, a `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Tento požadavek se aktivuje nabízené nasazení z odeslaného souboru ZIP. Chcete-li zkontrolovat aktuální a starší nasazení, spusťte následující příkazy. Znovu, nahraďte `<app_name>` zástupný symbol.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
