---
title: Nasazení zip push pro funkce Azure
description: K publikování funkcí Azure použijte zařízení pro nasazení souborů ZIP ve službě nasazení Kudu.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769659"
---
# <a name="zip-deployment-for-azure-functions"></a>Komprimované nasazení pro službu Azure Functions

Tento článek popisuje, jak nasadit soubory projektu aplikace funkce do Azure ze souboru ZIP (komprimovaný). Dozvíte se, jak provést nabízené nasazení, a to jak pomocí azure cli a pomocí rest API. [Nástroje Azure Functions Core Tools](functions-run-local.md) také používá tato nasazení API při publikování místního projektu do Azure.

Funkce Azure nabízí celou řadu možností průběžného nasazení a integrace, které poskytuje služba Azure App Service. Další informace najdete [v tématu Průběžné nasazení pro funkce Azure](functions-continuous-deployment.md).

Chcete-li urychlit vývoj, může být snazší nasadit soubory projektu aplikace funkce přímo ze souboru ZIP. Rozhraní API pro nasazení .zip převezme obsah souboru ZIP `wwwroot` a extrahuje obsah do složky aplikace funkce. Toto nasazení souboru ZIP používá stejnou službu Kudu, která pohání nasazení založená na průběžné integraci, včetně:

+ Odstranění souborů, které zbyly z dřívějších nasazení.
+ Přizpůsobení nasazení, včetně spuštění skriptů nasazení.
+ Protokoly nasazení.
+ Funkce synchronizace se aktivuje v aplikaci funkce [Plán spotřeby.](functions-scale.md)

Další informace naleznete v [odkazu na nasazení zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Požadavky na soubor ZIP nasazení

Soubor ZIP, který používáte pro nabízenou nasazení, musí obsahovat všechny soubory potřebné ke spuštění funkce.

>[!IMPORTANT]
> Při použití nasazení ZIP budou z aplikace funkce odstraněny všechny soubory z existujícího nasazení, které nejsou nalezeny v souboru ZIP.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikace funkce obsahuje všechny soubory a `wwwroot` složky v adresáři. Nasazení souboru ZIP zahrnuje obsah `wwwroot` adresáře, nikoli však samotný adresář. Při nasazování projektu knihovny tříd jazyka C# je nutné zahrnout `bin` soubory zkompilované knihovny a závislosti do podsložky v balíčku ZIP.

## <a name="download-your-function-app-files"></a>Stažení souborů aplikací funkcí

Při vývoji v místním počítači je snadné vytvořit soubor ZIP složky projektu aplikace funkce ve vývojovém počítači.

Je však možné, že jste vytvořili funkce pomocí editoru na webu Azure Portal. Existující projekt aplikace funkce si můžete stáhnout jedním z těchto způsobů:

+ **Z portálu Azure:**

  1. Přihlaste se na [portál Azure](https://portal.azure.com)a přejděte do aplikace funkce.

  2. Na kartě **Přehled** vyberte **Stáhnout obsah aplikace**. Vyberte možnosti stahování a pak vyberte **Stáhnout**.

      ![Stažení projektu aplikace funkce](./media/deployment-zip-push/download-project.png)

     Stažený soubor ZIP je ve správném formátu, který má být znovu publikován do aplikace funkcí pomocí nasazení push .zip. Stažení portálu můžete také přidat soubory potřebné k otevření aplikace funkce přímo v sadě Visual Studio.

+ **Použití api REST:**

    Ke stažení souborů z `<function_app>` projektu použijte následující rozhraní GET API nasazení: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Včetně `/site/wwwroot/` zajišťuje, že soubor zip obsahuje pouze soubory projektu aplikace funkce a ne celý web. Pokud ještě nejste přihlášení k Azure, budete o to požádáni.  

Soubor ZIP můžete také stáhnout z úložiště GitHub. Když si stáhnete úložiště GitHub jako soubor ZIP, GitHub přidá další úroveň složky pro větev. Tato další úroveň složky znamená, že soubor ZIP nelze nasadit přímo při stahování z GitHubu. Pokud používáte úložiště GitHub k údržbě aplikace funkcí, měli byste k nasazení aplikace použít [průběžnou integraci.](functions-continuous-deployment.md)  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

K aktivaci nabízeného nasazení můžete použít azure cli. Push nasadit soubor ZIP do aplikace funkce pomocí [příkazu az functionapp nasazení zdroj config-zip.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Chcete-li použít tento příkaz, musíte použít Azure CLI verze 2.0.21 nebo novější. Chcete-li zjistit, jakou verzi příkazového příkazu Azure používáte, použijte `az --version` příkaz.

V následujícím příkazu `<zip_file_path>` nahraďte zástupný symbol cestou k umístění souboru ZIP. Nahraďte `<app_name>` také jedinečný název aplikace funkce `<resource_group>` a nahraďte název skupiny prostředků.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Tento příkaz nasazuje soubory projektu ze staženého souboru ZIP do aplikace funkce v Azure. Potom restartuje aplikaci. Chcete-li zobrazit seznam nasazení pro tuto aplikaci funkce, musíte použít rest API.

Pokud používáte Azure CLI v místním `<zip_file_path>` počítači, je cesta k souboru ZIP v počítači. Azure CLI můžete spustit taky v [Azure Cloud Shell](../cloud-shell/overview.md). Když používáte Cloud Shell, musíte nejprve nahrát soubor zip nasazení do účtu Azure Files, který je přidružený k cloudovému prostředí. V takovém `<zip_file_path>` případě je umístění úložiště, které používá váš účet Cloud Shell. Další informace najdete v tématu [Persist files in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Spuštění funkcí z balíčku nasazení

Můžete také spustit funkce přímo ze souboru balíčku nasazení. Tato metoda přeskočí krok nasazení kopírování souborů z `wwwroot` balíčku do adresáře aplikace funkce. Místo toho je soubor balíčku připojen pomocí runtime Functions `wwwroot` a obsah adresáře se stane jen pro čtení.  

Nasazení zipu se integruje s touto funkcí, `WEBSITE_RUN_FROM_PACKAGE` kterou můžete `1`povolit nastavením aplikace funkce na hodnotu . Další informace naleznete v [tématu Spuštění funkcí ze souboru balíčku nasazení](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
