---
title: Nasazení kódu pomocí souboru ZIP nebo WAR – Azure App Service | Microsoft Docs
description: Naučte se, jak nasadit aplikaci pro Azure App Service se souborem ZIP (nebo souborem WAR pro vývojáře v jazyce Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 83951f6408094b8d1e04d19650a5f2ef596be988
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801148"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Nasazení aplikace pro Azure App Service pomocí souboru ZIP nebo WAR

V tomto článku se dozvíte, jak pomocí souboru ZIP nebo WAR nasadit webovou aplikaci na [Azure App Service](overview.md). 

Toto nasazení souboru ZIP používá stejnou službu Kudu, která je založena na kontinuální integraci nasazení. Kudu podporuje následující funkce nasazení souboru ZIP: 

- Odstranění souborů zbývajících z předchozího nasazení.
- Možnost zapněte výchozí proces sestavení, který zahrnuje obnovení balíčku.
- [Přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), včetně spuštěných skriptů nasazení.  
- Protokoly nasazení. 
- Omezení velikosti souboru 2048 MB.

Další informace najdete v [dokumentaci k Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Nasazení souboru WAR nasadí váš soubor [War](https://wikipedia.org/wiki/WAR_(file_format)) pro App Service ke spuštění vaší webové aplikace v jazyce Java. Viz [nasazení souboru WAR](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Postup dokončení kroků v tomto článku:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.

## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste soubory stáhli v souboru ZIP, nejprve soubory rozbalte. Pokud jste například stáhli soubor ZIP z GitHubu, nemůžete tento soubor nasadit tak, jak je. GitHub přidá další vnořené adresáře, které nefungují s App Service. 
>

V místním okně terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář by měl obsahovat vstupní soubor vaší webové aplikace, jako je například _index. html_, _index. php_a _App. js_. Může také obsahovat soubory správy balíčků jako _Project. JSON_, _skladatele. JSON_, _Package. JSON_, _Bower. JSON_a _požadavky. txt_.

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Výše uvedený koncový bod nefunguje v současnosti pro Linux App Services. Zvažte místo toho použití FTP nebo [rozhraní API pro nasazení zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) .

## <a name="deploy-zip-file-with-azure-cli"></a>Nasazení souboru ZIP pomocí Azure CLI

Ujistěte se, že je verze Azure CLI 2.0.21 nebo novější. Pokud chcete zjistit, kterou verzi máte, `az --version` spusťte příkaz v okně terminálu.

Nasaďte nahraný soubor ZIP do webové aplikace pomocí příkazu [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

Následující příklad nasadí soubor ZIP, který jste nahráli. Pokud používáte místní instalaci rozhraní příkazového řádku Azure CLI, zadejte cestu k místnímu souboru ZIP `--src`pro.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci.

Ve výchozím nastavení modul pro nasazení předpokládá, že soubor ZIP je připraven ke spuštění, a nespustí automatizaci sestavení. Pokud chcete povolit stejné automatizace sestavení jako v [nasazení Git](deploy-local-git.md), nastavte `SCM_DO_BUILD_DURING_DEPLOYMENT` nastavení aplikace spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Další informace najdete v [dokumentaci k Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Nasadit soubor WAR

Chcete-li nasadit soubor WAR pro App Service, odešlete požadavek POST `https://<app_name>.scm.azurewebsites.net/api/wardeploy`na. Požadavek POST musí obsahovat soubor .war v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication.

Pro základní ověřování HTTP budete potřebovat přihlašovací údaje pro nasazení App Service. Informace o nastavení přihlašovacích údajů pro nasazení najdete v tématu [nastavení a resetování přihlašovacích údajů na úrovni uživatele](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>S kudrlinkou

V následujícím příkladu je k nasazení souboru. War použit nástroj kudrlinkou. Nahraďte zástupné `<war-file-path>`symboly `<username>`, `<app-name>`a. Po zobrazení výzvy otočením zadejte heslo.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) pro nahrání souboru. War. Nahraďte zástupné `<app-name>`symboly `<group-name>`, `<war-file-path>`a.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další postup

V případě pokročilejších scénářů nasazení zkuste [nasadit do Azure pomocí Gitu](deploy-local-git.md). Nasazení založené na Gitu do Azure umožňuje správu verzí, obnovování balíčků, MSBuild a další.

## <a name="more-resources"></a>Další materiály

* [Kudu Nasazení ze souboru ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Přihlašovací údaje pro nasazení Azure App Service](deploy-ftp.md)
