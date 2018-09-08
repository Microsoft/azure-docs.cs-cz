---
title: Zazipovat nabízené nasazení pro službu Azure Functions | Dokumentace Microsoftu
description: Použití zařízení nasazení souboru ZIP služba pro nasazení Kudu k publikování Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 53a5de98ccdae4d4a73d8dc7b150c8e333024b33
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095160"
---
# <a name="zip-deployment-for-azure-functions"></a>Nasazení ZIP pro službu Azure Functions

Tento článek popisuje, jak nasadit soubory projektu funkce aplikace do Azure ze souboru ZIP (komprimované). Zjistíte, jak provést nabízené nasazení pomocí rozhraní příkazového řádku Azure a rozhraní REST API. [Nástroje Azure Functions Core](functions-run-local.md) také používá tato rozhraní API nasazení při publikování místní projekt do Azure.

Azure Functions nabízí široké spektrum průběžného nasazování a integrace možnosti, které jsou k dispozici ve službě Azure App Service. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md).

Pro rychlejší vývoj, možná bude snazší nasazování souborů projektu funkce aplikace přímo ze souboru ZIP. Rozhraní API nasazení ZIP načte obsah souboru ZIP a extrahuje obsah do `wwwroot` složky vaší aplikace function App. Toto nasazení souboru ZIP používá stejnou službu Kudu této umožňuje nepřetržité integrace nasazení na základě, včetně:

+ Odstranění souborů, které byly ponechány z předchozích nasazení.
+ Vlastní nastavení nasazení, včetně spouštění skriptů nasazení.
+ Protokoly nasazení.
+ Aktivační události funkcí v synchronizaci [plánu Consumption](functions-scale.md) aplikace function app.

Další informace najdete v tématu [odkaz nasazení ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Požadavky na soubor .zip nasazení

Soubor .zip, který používáte pro nabízené nasazení musí obsahovat všechny soubory potřebné ke spuštění vaší funkce.

>[!IMPORTANT]
> Pokud použijete nasazení ZIP, jsou odstraněny soubory ze stávajícího nasazení, které se nacházejí v souboru .zip z vaší aplikace function app.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikace function app obsahuje všechny soubory a složky v `wwwroot` adresáře. Nasazení souboru ZIP obsahuje obsah `wwwroot` adresáře, ale ne k adresáři, samotného. Při nasazování projektu knihovny tříd jazyka C#, musíte zahrnout soubory zkompilované knihovny a závislosti v `bin` podsložce v balíčku .zip.

## <a name="download-your-function-app-files"></a>Stahování souborů aplikace – funkce

Při vývoji v místním počítači, je snadné vytvořit soubor .zip složky projektu aplikace funkcí ve svém vývojovém počítači.

Může jste ale vytvořili funkce pomocí editoru na webu Azure Portal. Si můžete stáhnout existující projekt aplikace funkcí v jednom z těchto způsobů:

+ **Na webu Azure Portal:**

    1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a potom přejděte ke své aplikaci function app.

    2. Na **přehled** kartu, vyberte možnost **stáhnout obsah aplikace**. Vyberte si některou možnost stažení a pak vyberte **Stáhnout**.

        ![Stáhněte si projekt aplikace funkcí](./media/deployment-zip-push/download-project.png)

    Soubor ZIP staženého je ve správném formátu se znovu publikovat aplikaci function App pomocí nabízené nasazení ZIP. Stažení portálu můžete také přidat soubory potřebné k otevření aplikace function app přímo v sadě Visual Studio.

+ **Pomocí rozhraní REST API:**

    Pomocí následující nasazení získat rozhraní API stahovat soubory z vašeho `<function_app>` projektu: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Včetně `/site/wwwroot/` zajišťuje váš soubor zip obsahuje jenom soubory projektu pro funkce aplikace a ne celý web. Pokud nejste ještě přihlášení do Azure, zobrazí výzva k tomu.  

Soubor .zip můžete také stáhnout z úložiště GitHub. Když si stáhnete úložiště GitHub jako soubor ZIP, GitHub přidá úroveň dodatečné složky pro větev. To další složky úrovně znamená, že nemůžete nasadit přímo jako soubor .zip ho stáhnou z Githubu. Pokud používáte úložiště GitHub udržovat aplikace function app, měli byste použít [kontinuální integrace](functions-continuous-deployment.md) k nasazení vaší aplikace.  

## <a name="cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít k aktivaci nabízené nasazení. Nabízené nasazení soubor ZIP do aplikace function app pomocí [az functionapp deployment zdroj config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) příkazu. Pokud chcete použít tento příkaz, je nutné použít Azure CLI verze 2.0.21 nebo novější. Pokud chcete zjistit, jaká verze rozhraní příkazového řádku Azure používáte, použijte `az --version` příkazu.

V následujícím příkazu nahraďte `<zip_file_path>` zástupný symbol cestu k umístění souboru .zip. Také nahraďte `<app_name>` s jedinečným názvem aplikace function App. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

Tento příkaz nasadí soubory projektu ze souboru ZIP staženého aplikaci function App v Azure. Poté restartuje aplikaci. Chcete-li zobrazit seznam nasazení pro tuto aplikaci function app, musíte použít rozhraní REST API.

Pokud používáte rozhraní příkazového řádku Azure v místním počítači, `<zip_file_path>` je cesta k souboru ZIP ve vašem počítači. Azure CLI můžete také spustit v [Azure Cloud Shell](../cloud-shell/overview.md). Při použití Cloud Shell, musíte nejprve nahrát soubor ZIP nasazení Azure Files účtu, který je spojen s vaší služby Cloud Shell. V takovém případě `<zip_file_path>` je umístění úložiště, který používá váš účet Cloud Shell. Další informace najdete v tématu [trvalému ukládání souborů ve službě Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Spuštění funkce z balíčku pro nasazení

Můžete také spouštět funkce přímo ze souboru balíčku nasazení. Tato metoda přeskočí krok nasazení z balíčku pro kopírování souborů `wwwroot` adresáře aplikace function App. Místo toho je připojený soubor balíčku tak, že modul runtime služby Functions a obsah `wwwroot` adresáře budou jen pro čtení.  

> [!NOTE]
> Možnost spuštění z balíčku pro nasazení vaší aplikace function app je ve verzi preview.

Nasazení ZIP se integruje se s touto funkcí, které lze povolit nastavením nastavení aplikace funkcí `WEBSITE_RUN_FROM_ZIP` hodnotě `1`. Další informace najdete v tématu [spouštět funkce ze souboru balíčku nasazení](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
