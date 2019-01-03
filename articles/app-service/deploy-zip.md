---
title: Nasazení kódu pomocí souboru ZIP nebo WAR - službě Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci do služby Azure App Service pomocí souboru ZIP (nebo soubor WAR pro vývojáře v Javě).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.custom: seodec18
ms.openlocfilehash: 011caaad2f9cb3b0b891df172002dcb6b6aa8206
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607047"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Nasazení aplikace do služby Azure App Service pomocí souboru ZIP nebo WAR

Tento článek ukazuje, jak použít k nasazení vaší webové aplikace na soubor ZIP nebo WAR file [služby Azure App Service](overview.md). 

Toto nasazení souboru ZIP používá stejnou službu Kudu nasazení průběžné integrace pomocí tohoto využívá. Kudu podporuje následující funkce pro nasazení souboru ZIP: 

- Odstraňování souborů přetrvávající ze předchozí nasazení.
- Možnost zapnutí výchozí proces sestavení, která zahrnuje obnovení balíčků.
- [Vlastní nastavení nasazení](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), včetně spouštění skriptů nasazení.  
- Protokoly nasazení. 

Další informace najdete v tématu [Kudu dokumentaci](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Nasazení souboru WAR nasadí váš [WAR](https://wikipedia.org/wiki/WAR_(file_format)) soubor do služby App Service pro spuštění vaší webové aplikace v Javě. Zobrazit [soubor WAR nasadit](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.

## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste si stáhli soubory v souboru ZIP, nejdřív extrahujte soubory. Například pokud jste stáhli soubor ZIP z Githubu, nemůžete nasadit tento soubor jako-je. GitHub přidá další vnořené adresáře, které nefungují s App Service. 
>

V místním okně terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář by měl obsahovat vstupní soubor do vaší webové aplikace, jako například _index.html_, _index.php_, a _app.js_. Může také obsahovat soubory sady management package jako _project.json_, _composer.json_, _package.json_, _bower.json_a _souboru requirements.txt_.

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Nasazení souboru ZIP pomocí Azure CLI

Ujistěte se, že je vaše verze Azure CLI 2.0.21 nebo novější. Pokud chcete zobrazit verzi máte, spusťte `az --version` příkazu v okně terminálu.

Nasaďte nahraný soubor ZIP do vaší webové aplikace s použitím [az webapp nasazení zdroj config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) příkazu.  

Tento příklad nasadí soubor ZIP, který jste nahráli. Pokud používáte místní instalaci Azure CLI, zadejte cestu k vaší místní soubor ZIP pro `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci. Pokud je nakonfigurovaný nějaký vlastní proces sestavení, spustí se také. Další informace najdete v tématu [Kudu dokumentaci](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Nasazení souboru WAR

K nasazení souboru WAR do služby App Service, odešlete požadavek POST https://<app_name>.scm.azurewebsites.net/api/wardeploy. Požadavek POST musí obsahovat soubor .war v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication. 

HTTP BASIC authentication je nutné přihlašovací údaje pro nasazení služby App Service. Jak nastavit přihlašovací údaje pro nasazení najdete v tématu [nastavit a resetovat přihlašovací údaje na úrovni uživatele](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Pomocí cURL

Nástroj cURL nasaďte soubor .war v následujícím příkladu. Nahraďte zástupné symboly `<username>`, `<war_file_path>`, a `<app_name>`. Po zobrazení výzvy curl zadejte heslo.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) odeslat požadavek, který obsahuje soubor .war. Nahraďte zástupné symboly `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, a `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další postup

Pro pokročilejší scénáře nasazení, zkuste [nasazení do Azure pomocí Gitu](deploy-local-git.md). Nasazení z Gitu do Azure umožňuje správu verzí, obnovení balíčku, nástroj MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Kudu: Nasazení ze souboru zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Přihlašovací údaje pro nasazení služby Azure App Service](deploy-ftp.md)
