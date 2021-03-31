---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018415"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Nasazení souboru ZIP s rozhraními REST API 

K nasazení souboru. zip do aplikace v Azure můžete použít [rozhraní REST API služby nasazení](https://github.com/projectkudu/kudu/wiki/REST-API) . K nasazení odešlete požadavek POST na https://<app_name>. scm.azurewebsites.net/api/zipdeploy. Požadavek POST musí v těle zprávy obsahovat soubor. zip. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication. Další informace najdete v referenčních informacích k [nasazení push. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Pro základní ověřování HTTP budete potřebovat přihlašovací údaje pro nasazení App Service. Informace o nastavení přihlašovacích údajů pro nasazení najdete v tématu [nastavení a resetování přihlašovacích údajů na úrovni uživatele](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>S kudrlinkou

V následujícím příkladu je k nasazení souboru. zip použit nástroj kudrlinkou. Nahraďte zástupné symboly `<deployment_user>` , `<zip_file_path>` a `<app_name>` . Po zobrazení výzvy otočením zadejte heslo.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Tento požadavek spustí nasazení push z nahraného souboru. zip. Aktuální a minulá nasazení můžete zkontrolovat pomocí `https://<app_name>.scm.azurewebsites.net/api/deployments` koncového bodu, jak je znázorněno v následujícím příkladu. Nahraďte `<app_name>` názvem vaší aplikace a `<deployment_user>` uživatelským jménem přihlašovacích údajů pro nasazení.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) nahrání souboru. zip. Nahraďte zástupné symboly `<group-name>` , `<app-name>` a `<zip-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Tento požadavek spustí nasazení push z nahraného souboru. zip. 

Chcete-li zkontrolovat aktuální nasazení a předchozí, spusťte následující příkazy. Znovu nahraďte `<deployment-user>` `<deployment-password>` `<app-name>` zástupné symboly, a.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
