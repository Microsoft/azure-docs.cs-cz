---
title: "Nasazení aplikace do Azure App Service pomocí souboru ZIP nebo válečných | Microsoft Docs"
description: "Informace o nasazení aplikace do služby Azure App Service se soubor ZIP (nebo soubor WAR pro vývojáře v jazyce Java)."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: 41fb529f6b4ae923f2920919306324c86a2baa45
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Nasazení aplikace do Azure App Service pomocí souboru ZIP nebo WAR

Tento článek ukazuje, jak použít soubor ZIP nebo soubor WAR pro nasazení vaší webové aplikace na [Azure App Service](app-service-web-overview.md). 

Toto nasazení souboru ZIP používá stejnou službu Kudu nasazení průběžné na základě integrace této zajišťuje. Kudu podporuje následující funkce pro nasazení soubor ZIP: 

- Odstraňování souborů zbyly z předchozí nasazení.
- Možnost Zapnout výchozí proces sestavení, která zahrnuje obnovení balíčků.
- [Vlastní nastavení nasazení](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), včetně spouštění skriptů nasazení.  
- Protokoly nasazení. 

Další informace najdete v tématu [Kudu dokumentaci](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Nasazení souboru WAR nasadí vaše [WAR](https://wikipedia.org/wiki/WAR_(file_format)) souboru do služby App Service ke spouštění vaší webové aplikace Java. V tématu [soubor WAR nasazení](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pokud chcete provést kroky v tomto článku:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.

## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste stáhli soubory v souboru ZIP, nejdřív extrahujte soubory. Například pokud jste stáhli soubor ZIP z Githubu, nemůžete nasadit tento soubor jako-je. GitHub přidá další vnořené adresáře, které nefungují službou App Service. 
>

V místní okno terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář musí obsahovat vstupní soubor do vaší webové aplikace, jako například _index.html_, _index.php_, a _app.js_. Může také obsahovat soubory balíčku správy jako _project.json_, _composer.json_, _package.json_, _bower.json_a _requirements.txt_.

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Nasadit soubor ZIP pomocí rozhraní příkazového řádku Azure

Přesvědčte se, že tato verze rozhraní příkazového řádku Azure je 2.0.21 nebo novější. Chcete zobrazit verzi, spustíte `az --version` příkazu v okně terminálu.

Nahrávaný soubor ZIP nasazení do webové aplikace pomocí [az webapp nasazení zdroj konfigurace zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) příkaz.  

Následující příklad nasadí soubor ZIP, který jste nahráli. Pokud používáte místní instalace rozhraní příkazového řádku Azure, zadejte cestu k souboru ZIP místní pro `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci. Pokud je nakonfigurovaný nějaký vlastní proces sestavení, spustí se také. Další informace najdete v tématu [Kudu dokumentaci](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Nasazení souboru WAR

K nasazení souboru WAR do služby App Service, poslat https://<app_name>.scm.azurewebsites.net/api/wardeploy požadavek POST. Požadavek POST musí obsahovat soubor .war v textu zprávy. Přihlašovací údaje nasazení pro vaši aplikaci jsou uvedeny v požadavku pomocí ověřování HTTP BASIC. 

Pro ověřování HTTP BASIC budete potřebovat přihlašovací údaje nasazení služby App Service. Chcete-li zjistit, jak nastavit přihlašovací údaje nasazení, přečtěte si téma [nastavit a resetovat přihlašovací údaje individuální](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Pomocí cURL

Následující příklad používá nástroj cURL k nasazení souboru .war. Nahraďte zástupné symboly `<username>`, `<war_file_path>`, a `<app_name>`. Po zobrazení výzvy cURL, zadejte heslo.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) poslat žádost, která obsahuje soubor .war. Nahraďte zástupné symboly `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, a `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

## <a name="next-steps"></a>Další postup

Pro pokročilejší scénáře nasazení, zkuste [nasazení do Azure s Gitem](app-service-deploy-local-git.md). Na základě Git nasazení do Azure umožňuje verzí, obnovení balíčků, MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Kudu: Nasazení ze souboru zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Přihlašovací údaje pro nasazení služby Azure App Service](app-service-deploy-ftp.md)
