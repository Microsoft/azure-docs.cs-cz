---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769658"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Nasazení souboru ZIP pomocí rest api 

K nasazení souboru ZIP do aplikace v Azure můžete použít [nasazení služby REST API.](https://github.com/projectkudu/kudu/wiki/REST-API) Chcete-li nasadit, odešlete požadavek POST na https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Požadavek POST musí obsahovat soubor ZIP v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication. Další informace naleznete v [odkazu na nasazení push zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Pro ověřování HTTP BASIC potřebujete pověření pro nasazení služby App Service. Postup nastavení přihlašovacích údajů k nasazení najdete v tématu [Nastavení a obnovení přihlašovacích údajů na úrovni uživatele](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>S cURL

Následující příklad používá nástroj cURL k nasazení souboru ZIP. Nahraďte `<deployment_user>`zástupné symboly `<zip_file_path>`, a `<app_name>`. Po zobrazení výzvy pomocí příkazu cURL zadejte heslo.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Tento požadavek aktivuje nabízené nasazení z nahraného souboru ZIP. Aktuální a minulé nasazení můžete zkontrolovat `https://<app_name>.scm.azurewebsites.net/api/deployments` pomocí koncového bodu, jak je znázorněno v následujícím příkladu cURL. Znovu nahraďte `<app_name>` názvem aplikace `<deployment_user>` a uživatelským jménem přihlašovacích údajů pro nasazení.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) nahrát soubor ZIP. Nahraďte `<group-name>`zástupné symboly `<app-name>`, a `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Tento požadavek aktivuje nabízené nasazení z nahraného souboru ZIP. 

Chcete-li zkontrolovat aktuální a minulé nasazení, spusťte následující příkazy. Znovu vyměňte `<deployment-user>` `<deployment-password>`zástupné `<app-name>` symboly , a zástupné symboly .

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
