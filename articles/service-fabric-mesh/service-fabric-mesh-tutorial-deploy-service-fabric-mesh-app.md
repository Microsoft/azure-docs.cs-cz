---
title: 'Kurz: Nasazení aplikace Service Fabric Mesh do služby Service Fabric Mesh | Microsoft Docs'
description: Naučte se publikovat aplikaci Azure Service Fabric Mesh s webem ASP.NET Core, která komunikuje s back-endovou webovou službou.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126571"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Kurz: Nasazení webové aplikace Service Fabric Mesh

V tomto kurzu, který je třetí částí série, se dozvíte, jak publikovat webovou aplikaci Azure Service Fabric Mesh přímo ze sady Visual Studio.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Publikovat aplikaci do Azure
> * Zkontrolovat stav nasazení aplikace
> * Zobrazit všechny aplikace aktuálně nasazené v předplatném
> * Zobrazit protokoly aplikací
> * Vymazat prostředky používané aplikací

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit webovou aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladit aplikaci v místním prostředí](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Publikování aplikace do Azure

Dozvíte se, jak vytvořit aplikaci Azure Service Fabric Mesh s webovým front-endem ASP.NET a back-endovou službou s webovým rozhraním API ASP.NET Core. Potom provedete ladění aplikace v místním clusteru pro vývoj a publikujete ji do Azure. Na konci tak získáte jednoduchou aplikaci s úkoly, která předvádí volání mezi službami ve webové aplikaci Service Fabric Mesh.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Zkontrolujte, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md), což zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Dockeru a sady Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Stažení ukázkové aplikace pro seznam úkolů

Pokud jste nesestavili ukázkovou aplikaci s úkoly v [druhé části této série kurzů](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikace se nachází v adresáři `src\todolistapp`.

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Pokud chcete projekt Service Fabric Mesh publikovat v Azure, klikněte v sadě Visual Studio pravým tlačítkem myši na **ServiceFabricMeshApp** a vyberte **Publikovat**.

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Zvolte **Umístění**. Tento článek používá umístění **USA – východ**.

V části **Skupina prostředků** vyberte **\<Vytvořit novou skupinu prostředků...>**. Zobrazí se dialogové okno, ve kterém vytvoříte novou skupinu prostředků. Tento článek pracuje s umístěním **USA – východ** a názvem skupiny **sfmeshTutorial1RG** (pokud vaše organizace má více lidí, kteří používají stejné předplatné, zvolte jedinečný název skupiny).  Kliknutím na **Vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

V dialogovém okně **Publikovat aplikaci Service Fabricu** vyberte v části **Azure Container Registry** možnost **\<Vytvořit nový registr kontejneru...>**. V dialogovém okně **Vytvořit registr kontejneru** použijte pro **registr kontejneru** jedinečný název. Zadejte **umístění** (tento kurz používá **USA – východ**). V rozevírací nabídce vyberte **skupinu prostředků**, kterou jste vytvořili v předchozím kroku, například **sfmeshTutorial1RG**. **Skladovou položku** nastavte na **Basic** a kliknutím na **Vytvořit** se vraťte do dialogu pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Pokud se vám zobrazí chyba, že poskytovatel prostředků není ve vašem předplatném zaregistrován, můžete ho zaregistrovat. Nejprve se podívejte, zda je poskytovatel prostředků pro vaše předplatné dostupný:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Pokud je poskytovatel registru kontejneru (`Microsoft.ContainerRegistry`) dostupný, zaregistrujte ho v PowerShellu:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

V dialogu pro publikování klikněte na tlačítko **Publikovat** a nasaďte aplikaci Service Fabricu do Azure.

Při prvním publikování do Azure se do služby ACR (Azure Container Registry) nahraje image dockeru, což v závislosti na velikosti image může nějakou chvíli trvat. Následná publikování stejného projektu budou rychlejší. Průběh nasazení můžete monitorovat v podokně **Nástroje Service Fabric** v okně **Výstup** v sadě Visual Studio. Po dokončení nasazení se ve výstupu **Nástroje Service Fabric** zobrazí IP adresa a port aplikace v podobě adresy URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení zbývajících kroků můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI.

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.35 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete CLI nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

## <a name="install-the-az-mesh-cli"></a>Instalace Azure Mesh CLI
Na příkazovém řádku CLI proveďte toto:

1) Odeberte všechny předchozí instalace modulu Azure Service Fabric Mesh CLI.

```cli
az extension remove --name mesh
```

2)  Nainstalujte modul rozšíření Azure Service Fabric Mesh CLI. Ve verzi Preview je rozhraní Azure Service Fabric Mesh CLI vytvořeno jako rozšíření Azure CLI, ale ve verzi Public Preview bude součástí Azure CLI.

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>Kontrola stavu nasazení aplikace

V tuto chvíli je vaše aplikace nasazena. Na její stav se můžete podívat pomocí příkazu `app show`. 

Název aplikace pro účely tohoto kurzu je `ServiceMeshApp`. Podrobnosti o aplikace získáte pomocí následujícího příkazu:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Zobrazení všech aplikací aktuálně nasazených v předplatném

Seznam aplikací, které jste nasadili v předplatném, zobrazíte pomocí příkazu app list.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Zobrazení protokolů aplikací

Projděte si protokoly nasazené aplikace:

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už vytvořené prostředky nepotřebujete, všechny je odstraňte. Protože jste vytvořili novou skupinu prostředků za účelem hostování prostředků služby ACR a Service Fabric Mesh, můžete tuto skupinu prostředků bezpečně odstranit. Odstraní se také všechny přidružené prostředky.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Skupinu prostředků můžete také odstranit [prostřednictvím portálu](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Publikovat aplikaci do Azure
> * Zkontrolovat stav nasazení aplikace
> * Zobrazit všechny aplikace aktuálně nasazené v předplatném
> * Zobrazit protokoly aplikací
> * Vymazat prostředky používané aplikací

Nyní, když jste dokončili publikování aplikace Service Fabric Mesh do Azure, vyzkoušejte toto:

* Prohlédněte si [ukázku hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) a podívejte se na další příklad komunikace mezi službami.
* Přečtěte si o [prostředcích Service Fabric](service-fabric-mesh-service-fabric-resources.md).
* Přečtěte si o [Cloud Shellu](https://docs.microsoft.com/azure/cloud-shell/overview).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest