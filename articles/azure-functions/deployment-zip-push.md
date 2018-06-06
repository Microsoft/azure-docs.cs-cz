---
title: ZIP nabízené nasazení pro Azure Functions | Microsoft Docs
description: Vlastnosti nasazení soubor .zip služby nasazení Kudu použijte k publikování Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/29/2018
ms.author: glenga
ms.openlocfilehash: 91c16ad5a6bf8babffc0b83d801626932688631e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699950"
---
# <a name="zip-push-deployment-for-azure-functions"></a>ZIP nabízené nasazení pro Azure Functions 
Tento článek popisuje postup nasazení soubory projektu funkce aplikace do Azure ze souboru ZIP (komprimované). Zjistíte, jak provést nasazení push, pomocí rozhraní příkazového řádku Azure i pomocí rozhraní REST API. 

Azure Functions nabízí celou řadu průběžné možnosti nasazení a integraci, které jsou k dispozici ve službě Azure App Service. Další informace najdete v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md). 

Pro rychlejší iterace během vývoje je často snazší nasazování soubory projektu funkce aplikace přímo z komprimovaný soubor ZIP. Toto nasazení soubor .zip používá stejnou službu Kudu, že zajišťuje nepřetržité integrace nasazení na základě, včetně:

+ Odstranění souborů, které byly ponechány z dřívějších nasazení.
+ Vlastní nastavení nasazení, včetně spouštění skriptů nasazení.
+ Protokoly nasazení.
+ Synchronizuje se aktivuje funkce v [plánu spotřeby](functions-scale.md) aplikaci funkce.

Další informace najdete v tématu [odkaz nasazení nabízené .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Požadavky na soubor .zip nasazení
Soubor .zip, který používáte pro nasazení nabízené musí obsahovat všechny soubory projektu ve vaší aplikaci funkce, včetně kódu funkce. 

>[!IMPORTANT]
> Pokud použijete nasazení nabízené .zip, se odstraní všechny soubory ze stávajícího nasazení, které se nenacházejí v souboru ZIP z vaší aplikace. funkce.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Funkce aplikace obsahuje všechny soubory a složky v `wwwroot` adresáře. Nasazení soubor ZIP obsahuje obsah `wwwroot` adresáře, ale ne adresář.  

## <a name="download-your-function-app-files"></a>Stahování souborů aplikace – funkce

Při vývoji na místním počítači, je snadné vytvořit soubor .zip funkce složky projektu aplikace na vašem vývojovém počítači. 

Možná jste ale vytvořili funkcí pomocí editoru na portálu Azure. Si můžete stáhnout existující projekt aplikace funkce v jednom z těchto způsobů: 

+ **Z portálu Azure:** 

    1. Přihlaste se k [portál Azure](https://portal.azure.com)a poté přejděte k vaší aplikaci funkce.

    2. Na **přehled** vyberte **stažení obsahu aplikace**. Vyberte možnosti stahování a pak vyberte **Stáhnout**.     

        ![Stáhněte si projekt aplikace – funkce](./media/deployment-zip-push/download-project.png)

    Soubor stažený .zip je ve správném formátu znovu ji publikovat do funkce aplikace pomocí nabízených nasazení .zip. Stažení portálu můžete také přidat soubory potřebné pro otevřete aplikaci funkce přímo v sadě Visual Studio.

+ **Pomocí rozhraní REST API:** 

    Použít následující nasazení získat rozhraní API se stáhnout soubory z vaší `<function_app>` projektu: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Včetně `/site/wwwroot/` zajišťuje, váš soubor zip obsahuje pouze soubory projektu funkce aplikace a není celý web. Pokud už nejste přihlášení do Azure, budete vyzváni k tomu. Všimněte si, že odesláním příspěvku na žádost o `api/zip/` rozhraní API je discoraged považuje metodu nasazení zip popsaných v tomto tématu. 

Soubor ZIP můžete také stáhnout z úložiště Githubu. Uvědomte si, že když si stáhnete úložiště GitHub jako soubor ZIP, Githubu přidá úroveň další složky pro větev. To další složky úrovně znamená, že nemůžete nasadit přímo jako soubor .zip ji stáhnout z webu GitHub. Pokud používáte úložiště GitHub udržovat funkce aplikace, měli byste použít [průběžnou integraci](functions-continuous-deployment.md) k nasazení své aplikace.  

## <a name="cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít k aktivaci nabízených nasazení. Soubor ZIP aplikace funkce nabízené nasadit pomocí [az functionapp nasazení zdroj konfigurace zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) příkaz. Chcete-li použít tento příkaz, musíte použít Azure CLI verze 2.0.21 nebo novější. Pokud chcete zjistit, jaká verze rozhraní příkazového řádku Azure používáte, použijte `az --version` příkaz.

V následujícím příkazu nahraďte `<zip_file_path>` zástupný symbol cestu k umístění ve vašem souboru ZIP. Také nahraďte `<app_name>` s jedinečným názvem aplikace funkce. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Tento příkaz nasadí soubory projektu ze souboru ZIP staženého do funkce aplikace v Azure. Následně restartuje aplikace. Chcete-li zobrazit seznam nasazení pro tuto aplikaci funkce, je nutné použít rozhraní REST API.

Pokud používáte rozhraní příkazového řádku Azure v místním počítači, `<zip_file_path>` je cesta k souboru ZIP ve vašem počítači. Rozhraní příkazového řádku Azure můžete také spouštět [prostředí cloudu Azure](../cloud-shell/overview.md). Pokud používáte cloudové prostředí, musíte nejprve nahrát soubor ZIP nasazení Azure Files účtu, který je spojen s své cloudové prostředí. V takovém případě `<zip_file_path>` je umístění úložiště, které používá váš účet cloudové prostředí. Další informace najdete v tématu [zachovat soubory v prostředí cloudu Azure](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
