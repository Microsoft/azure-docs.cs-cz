---
title: Kurz – nasazení aplikace Service Fabric sítě
description: Zjistěte, jak pomocí sady Visual Studio publikovat aplikaci Azure Service Fabric Mesh složenou z webu ASP.NET Core, který komunikuje s back-endovou webovou službou.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: be1e477bf3f11d487b8a6705535c09ff6e2b9c3e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626729"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Kurz: Nasazení aplikace Service Fabric Mesh

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

V tomto kurzu, který je třetí částí série, se dozvíte, jak publikovat webovou aplikaci Azure Service Fabric Mesh přímo ze sady Visual Studio.

Co se v tomto kurzu naučíte:
> [!div class="checklist"]
> * Publikování aplikace do Azure pomocí sady Visual Studio
> * Kontrola stavu nasazení aplikace
> * Zobrazit všechny aplikace aktuálně nasazené v předplatném

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření aplikace Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladit aplikaci Service Fabric Mesh běžící v místním clusteru pro vývoj](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Nasazení aplikace Service Fabric Mesh
> * [Upgrade aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Ujistěte se, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md), což zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Dockeru a sady Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Stažení ukázkové aplikace pro seznam úkolů

Pokud jste nesestavili ukázkovou aplikaci s úkoly v [druhé části této série kurzů](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikace se nachází v adresáři `src\todolistapp`.

## <a name="publish-to-azure"></a>Publikování do Azure

Pokud chcete projekt Service Fabric Mesh publikovat do Azure, klikněte v sadě Visual Studio pravým tlačítkem na **todolistapp** a vyberte **Publikovat**.

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Vyberte **umístění**. Tento článek používá umístění **USA – východ**.

V části **Skupina prostředků** vyberte **\<Create New Resource Group...>** . Zobrazí se dialogové okno, ve kterém vytvoříte novou skupinu prostředků. Tento článek pracuje s umístěním **USA – východ** a názvem skupiny **sfmeshTutorial1RG** (pokud vaše organizace má více lidí, kteří používají stejné předplatné, zvolte jedinečný název skupiny).  Kliknutím na **Vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Zpátky v dialogovém okně **Service Fabric aplikace pro publikování** vyberte v části **Azure Container Registry** **\<Create New Container Registry...>** . V dialogovém okně **Vytvořit registr kontejneru** použijte pro **registr kontejneru** jedinečný název. Zadejte **umístění** (tento kurz používá **USA – východ**). V rozevírací nabídce vyberte **skupinu prostředků**, kterou jste vytvořili v předchozím kroku, například **sfmeshTutorial1RG**. Nastavte **skladovou položku** na **Basic** a pak stisknutím **Vytvořit** vytvořte privátní registr kontejneru Azure a vraťte se do dialogového okna Publikovat.

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

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh

K dokončení zbývajících kroků můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Kontrola stavu nasazení aplikace

V tuto chvíli je vaše aplikace nasazena. Na její stav se můžete podívat pomocí příkazu `app show`. 

Název aplikace pro účely tohoto kurzu je `todolistapp`. Podrobnosti o aplikace získáte pomocí následujícího příkazu:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Získat IP adresu vašeho nasazení

Pokud chcete získat IP adresu pro aplikaci, použijte následující příkaz:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Zobrazit všechny aplikace aktuálně nasazené v předplatném

Seznam aplikací, které jste nasadili v předplatném, zobrazíte pomocí příkazu app list.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Publikovat aplikaci do Azure
> * Kontrola stavu nasazení aplikace
> * Zobrazit všechny aplikace aktuálně nasazené v předplatném

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Upgrade aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: /cli/azure/install-azure-cli?view=azure-cli-latest
