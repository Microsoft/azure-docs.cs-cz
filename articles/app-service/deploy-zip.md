---
title: Nasazení kódu se souborem ZIP nebo WAR
description: Zjistěte, jak nasadit aplikaci do služby Azure App Service pomocí souboru ZIP (nebo souboru WAR pro vývojáře jazyka Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945137"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Nasazení aplikace do služby Azure App Service se souborem ZIP nebo WAR

V tomto článku se ukazuje, jak pomocí souboru ZIP nebo WAR nasadit webovou aplikaci do [služby Azure App Service](overview.md). 

Toto nasazení souboru ZIP používá stejnou službu Kudu, která pohání nasazení založená na průběžné integraci. Kudu podporuje následující funkce pro nasazení souborů ZIP: 

- Odstranění souborů, které zbyly z předchozího nasazení.
- Možnost zapnout výchozí proces sestavení, který zahrnuje obnovení balíčku.
- Přizpůsobení nasazení, včetně spuštění skriptů nasazení.  
- Protokoly nasazení. 
- Omezení velikosti souboru 2048 MB.

Další informace naleznete v [dokumentaci kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Nasazení souboru WAR nasazuje soubor [WAR](https://wikipedia.org/wiki/WAR_(file_format)) do služby App Service ke spuštění webové aplikace Java. Viz [Nasazení souboru WAR](#deploy-war-file).

## <a name="prerequisites"></a>Požadavky

Postup v tomto článku můžete provést [tak, že vytvoříte aplikaci služby App Service](/azure/app-service/)nebo použijete aplikaci, kterou jste vytvořili pro jiný kurz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Výše uvedený koncový bod nefunguje pro Linux App Services v tuto chvíli. Zvažte použití FTP nebo [rozhraní API pro nasazení zip.](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Nasazení souboru ZIP pomocí azure cli

Nasadit nahraný soubor ZIP do webové aplikace pomocí příkazu [config-ZIP pro nasazení az webapp.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

Následující příklad nasadí soubor ZIP, který jste nahráli. Při použití místní instalace příkazového příkazového příkazu Kontu Azure zadejte cestu k místnímu souboru ZIP pro aplikaci `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci.

Ve výchozím nastavení modul nasazení předpokládá, že soubor ZIP je připraven ke spuštění tak, jak je, a nespustí žádnou automatizaci sestavení. Chcete-li povolit stejnou automatizaci sestavení `SCM_DO_BUILD_DURING_DEPLOYMENT` jako v [nasazení Gitu](deploy-local-git.md), nastavte nastavení aplikace spuštěním následujícího příkazu v [prostředí Cloud :](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Další informace naleznete v [dokumentaci kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Nasazení souboru WAR

Chcete-li nasadit soubor WAR do služby App Service, odešlete požadavek POST společnosti `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. Požadavek POST musí obsahovat soubor .war v textu zprávy. Přihlašovací údaje pro nasazení vaší aplikace jsou zahrnuté v požadavku s použitím HTTP BASIC Authentication.

Při `/api/wardeploy` nasazování souborů WAR používejte vždy. Toto rozhraní API rozšíří soubor WAR a umístí jej na sdílenou jednotku. použití jiných nasazení API může mít za následek nekonzistentní chování. 

Pro ověřování HTTP BASIC potřebujete pověření pro nasazení služby App Service. Postup nastavení přihlašovacích údajů k nasazení najdete v tématu [Nastavení a obnovení přihlašovacích údajů na úrovni uživatele](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>S cURL

Následující příklad používá nástroj cURL k nasazení souboru .war. Nahraďte `<username>`zástupné symboly `<war-file-path>`, a `<app-name>`. Po zobrazení výzvy pomocí příkazu cURL zadejte heslo.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>S využitím PowerShellu

Následující příklad používá [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) nahrát soubor .war. Nahraďte `<group-name>`zástupné symboly `<app-name>`, a `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Další kroky

Pokročilejší scénáře nasazení nawebujte nasazení [do Azure pomocí Gitu](deploy-local-git.md). Nasazení na základě Gitu do Azure umožňuje správu verzí, obnovení balíčků, MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Kudu: Nasazení ze souboru zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Přihlašovací údaje pro nasazení služby Azure App Service](deploy-ftp.md)
