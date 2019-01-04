---
title: Kurz – Nasazení aplikace Service Fabric Mesh | Microsoft Docs
description: Zjistěte, jak pomocí sady Visual Studio publikovat aplikaci Azure Service Fabric Mesh složenou z webu ASP.NET Core, který komunikuje s back-endovou webovou službou.
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
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ec282bc1159e8a8cf21b88b8430bbf3067686528
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788612"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Kurz: Nasazení aplikace Service Fabric mřížky

V tomto kurzu, který je třetí částí série, se dozvíte, jak publikovat webovou aplikaci Azure Service Fabric Mesh přímo ze sady Visual Studio.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Publikování aplikace do Azure pomocí sady Visual Studio
> * Zkontrolovat stav nasazení aplikace
> * Zobrazení všech aplikací aktuálně nasazených v předplatném

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvořit aplikaci Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladění aplikace Service Fabric Mesh spuštěné v místním clusteru pro vývoj](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Nasazení aplikace Service Fabric Mesh
> * [Upgrade aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

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

Pokud chcete projekt Service Fabric Mesh publikovat do Azure, klikněte v sadě Visual Studio pravým tlačítkem na **todolistapp** a vyberte **Publikovat**.

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Zvolte **Umístění**. Tento článek používá umístění **USA – východ**.

V části **Skupina prostředků** vyberte **\<Vytvořit novou skupinu prostředků...>**. Zobrazí se dialogové okno, ve kterém vytvoříte novou skupinu prostředků. Tento článek pracuje s umístěním **USA – východ** a názvem skupiny **sfmeshTutorial1RG** (pokud vaše organizace má více lidí, kteří používají stejné předplatné, zvolte jedinečný název skupiny).  Kliknutím na **Vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

V dialogovém okně **Publikovat aplikaci Service Fabricu** vyberte v části **Azure Container Registry** možnost **\<Vytvořit nový registr kontejneru...>**. V dialogovém okně **Vytvořit registr kontejneru** použijte pro **registr kontejneru** jedinečný název. Zadejte **umístění** (tento kurz používá **USA – východ**). V rozevírací nabídce vyberte **skupinu prostředků**, kterou jste vytvořili v předchozím kroku, například **sfmeshTutorial1RG**. Nastavte **skladovou položku** na **Basic** a pak stisknutím **Vytvořit** vytvořte privátní registr kontejneru Azure a vraťte se do dialogového okna Publikovat.

![Dialogové okno nového registru kontejneru Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

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

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh 
K dokončení zbývajících kroků můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Zkontrolovat stav nasazení aplikace

V tuto chvíli je vaše aplikace nasazena. Na její stav se můžete podívat pomocí příkazu `app show`. 

Název aplikace pro účely tohoto kurzu je `todolistapp`. Podrobnosti o aplikace získáte pomocí následujícího příkazu:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Zobrazení všech aplikací aktuálně nasazených v předplatném

Seznam aplikací, které jste nasadili v předplatném, zobrazíte pomocí příkazu app list.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Publikovat aplikaci do Azure
> * Zkontrolovat stav nasazení aplikace
> * Zobrazit všechny aplikace aktuálně nasazené v předplatném

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Upgradovat aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
