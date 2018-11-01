---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: ed771d1817e7ee1161e0f94ec9eefb5d2dfb3e23
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50737222"
---
## <a name="rest"></a>Nasazení souboru ZIP pomocí rozhraní REST API 

Můžete použít [nasazení služby rozhraní REST API](https://github.com/projectkudu/kudu/wiki/REST-API) k nasazení souboru ZIP do vaší aplikace v Azure. Pokud chcete nasadit, odešlete požadavek POST https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Požadavek POST musí obsahovat soubor ZIP v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication. Další informace najdete v tématu [odkaz nabízené nasazení ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

HTTP BASIC authentication je nutné přihlašovací údaje pro nasazení služby App Service. Jak nastavit přihlašovací údaje pro nasazení najdete v tématu [nastavit a resetovat přihlašovací údaje na úrovni uživatele](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Pomocí cURL

Následující příklad používá nástroj cURL k nasazení souboru ZIP. Nahraďte zástupné symboly `<username>`, `<password>`, `<zip_file_path>`, a `<app_name>`. Po zobrazení výzvy curl zadejte heslo.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Tuto žádost aktivuje nabízené nasazení z odeslaného souboru ZIP. Aktuálního i staršího nasazení můžete zkontrolovat pomocí `https://<app_name>.scm.azurewebsites.net/api/deployments` koncový bod, jak je znázorněno v následujícím příkladu cURL. Znovu nahraďte `<app_name>` s názvem vaší aplikace a `<deployment_user>` uživatelskému jménu přihlašovací údaje pro nasazení.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) odeslat požadavek, který obsahuje soubor .zip. Nahraďte zástupné symboly `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, a `<app_name>`.

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

Tuto žádost aktivuje nabízené nasazení z odeslaného souboru ZIP. Ke kontrole aktuálních a minulých nasazení, spusťte následující příkazy. Znovu nahraďte `<app_name>` zástupný symbol.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
