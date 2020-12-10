---
title: Nasazení kódu pomocí souboru ZIP nebo WAR
description: Naučte se, jak nasadit aplikaci pro Azure App Service se souborem ZIP (nebo souborem WAR pro vývojáře v jazyce Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007904"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Nasazení aplikace pro Azure App Service pomocí souboru ZIP nebo WAR

V tomto článku se dozvíte, jak pomocí souboru ZIP nebo WAR nasadit webovou aplikaci na [Azure App Service](overview.md). 

Toto nasazení souboru ZIP používá stejnou službu Kudu, která je založena na kontinuální integraci nasazení. Kudu podporuje následující funkce nasazení souboru ZIP: 

- Odstranění souborů zbývajících z předchozího nasazení.
- Možnost zapněte výchozí proces sestavení, který zahrnuje obnovení balíčku.
- Přizpůsobení nasazení, včetně spuštěných skriptů nasazení.  
- Protokoly nasazení. 
- Omezení velikosti souboru 2048 MB.

Další informace najdete v [dokumentaci k Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Nasazení souboru WAR nasadí váš soubor [War](https://wikipedia.org/wiki/WAR_(file_format)) pro App Service ke spuštění vaší webové aplikace v jazyce Java. Viz [nasazení souboru WAR](#deploy-war-file).

> [!NOTE]
> Při použití `ZipDeploy` se soubory zkopírují jenom v případě, že se jejich časová razítka neshodují s tím, co už je nasazené Generování souboru ZIP pomocí procesu sestavení, který ukládá do mezipaměti výstupy, může mít za následek rychlejší nasazení. Další informace najdete v tématu [nasazení ze souboru ZIP nebo adresy URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku [vytvořte aplikaci App Service](./index.yml)nebo použijte aplikaci, kterou jste vytvořili pro jiný kurz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Výše uvedený koncový bod nefunguje v současnosti pro Linux App Services. Zvažte místo toho použití FTP nebo [rozhraní API pro nasazení zip](faq-app-service-linux.md#continuous-integration-and-deployment) .

## <a name="deploy-zip-file-with-azure-cli"></a>Nasazení souboru ZIP pomocí Azure CLI

Nasaďte nahraný soubor ZIP do webové aplikace pomocí příkazu [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) .  

Následující příklad nasadí soubor ZIP, který jste nahráli. Pokud používáte místní instalaci rozhraní příkazového řádku Azure CLI, zadejte cestu k místnímu souboru ZIP pro `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci.

Ve výchozím nastavení modul pro nasazení předpokládá, že soubor ZIP je připraven ke spuštění, a nespustí automatizaci sestavení. Pokud chcete povolit stejné automatizace sestavení jako v [nasazení Git](deploy-local-git.md), nastavte `SCM_DO_BUILD_DURING_DEPLOYMENT` nastavení aplikace spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Další informace najdete v [dokumentaci k Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Nasadit soubor WAR

Chcete-li nasadit soubor WAR pro App Service, odešlete požadavek POST na `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Požadavek POST musí obsahovat soubor .war v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication.

Vždy použít `/api/wardeploy` při nasazování souborů War. Toto rozhraní API rozbalí soubor WAR a umístí ho do jednotky sdíleného souboru. použití jiných rozhraní API pro nasazení může mít za následek nekonzistentní chování. 

Pro základní ověřování HTTP budete potřebovat přihlašovací údaje pro nasazení App Service. Informace o nastavení přihlašovacích údajů pro nasazení najdete v tématu [nastavení a resetování přihlašovacích údajů na úrovni uživatele](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>S kudrlinkou

V následujícím příkladu je k nasazení souboru. War použit nástroj kudrlinkou. Nahraďte zástupné symboly `<username>` , `<war-file-path>` a `<app-name>` . Po zobrazení výzvy otočením zadejte heslo.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) pro nahrání souboru. War. Nahraďte zástupné symboly `<group-name>` , `<app-name>` a `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další kroky

V případě pokročilejších scénářů nasazení zkuste [nasadit do Azure pomocí Gitu](deploy-local-git.md). Nasazení založené na Gitu do Azure umožňuje správu verzí, obnovování balíčků, MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Kudu: nasazení ze souboru ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Přihlašovací údaje pro nasazení Azure App Service](deploy-ftp.md)
