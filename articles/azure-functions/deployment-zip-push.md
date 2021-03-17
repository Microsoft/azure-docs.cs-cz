---
title: Nasazení nabízených oznámení zip pro Azure Functions
description: K publikování Azure Functions použijte zařízení pro nasazení souboru. zip služby nasazení Kudu.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018417"
---
# <a name="zip-deployment-for-azure-functions"></a>Komprimované nasazení pro službu Azure Functions

Tento článek popisuje, jak nasadit soubory projektu Function App do Azure z souboru. zip (komprimovaného). Naučíte se, jak provést nasazení push pomocí Azure CLI a pomocí rozhraní REST API. [Azure Functions Core Tools](functions-run-local.md) také používá tato rozhraní API pro nasazení při publikování místního projektu do Azure.

Azure Functions má plný rozsah možností průběžného nasazování a integrace, které poskytuje Azure App Service. Další informace najdete v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md).

Chcete-li urychlit vývoj, může být snazší nasadit soubory projektu Function App přímo ze souboru. zip. Rozhraní API pro nasazení. zip převezme obsah souboru. zip a extrahuje obsah do složky aplikace `wwwroot` Function App. Toto nasazení souboru ZIP používá stejnou službu Kudu, která je založena na kontinuální integraci nasazení, včetně:

+ Odstranění souborů, které byly ponechány v předchozích nasazeních.
+ Přizpůsobení nasazení, včetně spuštěných skriptů nasazení.
+ Protokoly nasazení.
+ Synchronizace triggerů funkcí v aplikaci funkcí [plánu spotřeby](functions-scale.md) .

Další informace najdete v referenčních informacích k [nasazení. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Požadavky na soubor nasazení. zip

Soubor. zip, který používáte pro nasazení push, musí obsahovat všechny soubory potřebné ke spuštění vaší funkce.

>[!IMPORTANT]
> Při použití nasazení. zip se z aplikace Function App odstraní všechny soubory ze stávajícího nasazení, které se nenašly v souboru. zip.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikace Function App zahrnuje všechny soubory a složky v `wwwroot` adresáři. Nasazení souboru. zip zahrnuje obsah `wwwroot` adresáře, ale ne samotný adresář. Při nasazování projektu knihovny tříd jazyka C# musíte zahrnout zkompilované soubory knihovny a závislosti do `bin` podsložky v balíčku. zip.

## <a name="download-your-function-app-files"></a>Stažení souborů Function App

Když vyvíjíte v místním počítači, je snadné vytvořit soubor. zip složky projektu Function App ve vývojovém počítači.

Je však možné, že jste své funkce vytvořili pomocí editoru v Azure Portal. Existující projekt aplikace Function App si můžete stáhnout jedním z následujících způsobů:

+ **Z Azure Portal:**

  1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak přejít do aplikace Function App.

  2. Na kartě **Přehled** vyberte **Stáhnout obsah aplikace**. Vyberte možnosti stažení a pak vyberte **Stáhnout**.

      ![Stažení projektu Function App](./media/deployment-zip-push/download-project.png)

     Stažený soubor. zip má ve správném formátu, který se má znovu publikovat do aplikace Function App pomocí nasazení push. zip. Stažení portálu může také přidat soubory potřebné k otevření aplikace Function App přímo v aplikaci Visual Studio.

+ **Pomocí rozhraní REST API:**

    Pomocí následujícího nasazení získat rozhraní API Stáhněte soubory z `<function_app>` projektu: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Včetně tohoto `/site/wwwroot/` souboru je soubor zip, který obsahuje pouze soubory projektu Function App, a ne celý web. Pokud ještě nejste přihlášení k Azure, budete vyzváni k tomu.  

Soubor. zip si můžete také stáhnout z úložiště GitHub. Když si stáhnete úložiště GitHub jako soubor. zip, GitHub přidá na větev další úroveň složky. Tato dodatečná úroveň složky znamená, že nemůžete nasadit soubor. zip přímo do svého stažení z GitHubu. Pokud používáte úložiště GitHub k údržbě aplikace Function App, měli byste použít [průběžnou integraci](functions-continuous-deployment.md) k nasazení aplikace.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

K aktivaci nasazení nabízených oznámení můžete použít rozhraní příkazového řádku Azure. Pomocí příkazu [AZ functionapp Deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) nasaďte do aplikace Function app soubor. zip. Chcete-li použít tento příkaz, musíte použít Azure CLI verze 2.0.21 nebo novější. Pokud chcete zjistit, jakou verzi rozhraní příkazového řádku Azure používáte, použijte `az --version` příkaz.

V následujícím příkazu nahraďte `<zip_file_path>` zástupný symbol cestou k umístění souboru. zip. Nahraďte také `<app_name>` jedinečným názvem vaší aplikace Function App a nahraďte `<resource_group>` názvem vaší skupiny prostředků.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Tento příkaz nasadí soubory projektu ze staženého souboru. zip do aplikace Function App v Azure. Pak se aplikace restartuje. Pokud chcete zobrazit seznam nasazení pro tuto aplikaci Function App, musíte použít rozhraní REST API.

Pokud používáte Azure CLI v místním počítači, `<zip_file_path>` je cesta k souboru. zip v počítači. Rozhraní příkazového řádku Azure můžete také spustit v [Azure Cloud Shell](../cloud-shell/overview.md). Když použijete Cloud Shell, musíte nejdřív nahrát soubor nasazení. zip do účtu služby soubory Azure, který je přidružený k vašemu Cloud Shell. V takovém případě `<zip_file_path>` je to umístění úložiště, které používá váš Cloud shell účet. Další informace najdete v tématu [trvalé soubory v Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Spouštění funkcí z balíčku pro nasazení

Můžete také zvolit spuštění funkcí přímo ze souboru balíčku nasazení. Tato metoda přeskočí krok nasazení kopírování souborů z balíčku do `wwwroot` adresáře aplikace Function App. Místo toho je soubor balíčku připojen modulem runtime funkcí a obsah `wwwroot` adresáře bude jen pro čtení.  

Nasazení souboru zip se integruje s touto funkcí, kterou můžete povolit tak, že nastavíte nastavení funkce aplikace `WEBSITE_RUN_FROM_PACKAGE` na hodnotu `1` . Další informace najdete v tématu [spuštění funkcí ze souboru balíčku nasazení](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
