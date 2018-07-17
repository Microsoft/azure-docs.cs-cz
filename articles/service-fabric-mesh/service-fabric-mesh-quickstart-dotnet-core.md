---
title: Rychlý start – nasazení aplikace do služby Azure Service Fabric sítě | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvořit web ASP.NET Core a její publikování do Azure Service Fabric mřížky.
services: service--fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 93b540908391e8eedd9a1b318f3443f3e782c987
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075971"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Rychlý start: Vytvoření a nasazení webové aplikace do Azure Service Fabric mřížky

Sítě Azure Service Fabric je plně spravovaná služba, která vývojářům umožňuje nasadit mikroslužbových aplikací bez nutnosti spravovat virtuální počítače, úložiště nebo sítě.

V tomto rychlém startu vytvoříte novou aplikaci Service Fabric sítě skládající se z webové aplikace ASP.NET Core spusťte ho na místní cluster pro vývoj a publikuje ho spustit v Azure.

Budete potřebovat předplatné Azure. Pokud ho nemáte, můžete snadno vytvořit bezplatné předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete. Budete také muset [nastavení vývojářského prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Vytvořit projekt Service Fabric mřížky

Otevřít Visual Studio a vyberte **souboru** > **nový** > **projektu...**

V **nový projekt** dialogové okno **hledání** pole v horní části, typ `mesh`. Vyberte **mřížky aplikace Service Fabric** šablony. (Pokud nevidíte šablonu, ujistěte se, že jste nainstalovali mřížky SDK a nástroje Visual Studio zobrazit náhled, jak je popsáno v [nastavení vývojového prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

V **název** zadejte **ServiceFabricMesh1** a **umístění** pole, nastavte cestu složky, kde budou uloženy soubory projektu.

Ujistěte se, že **vytvořit adresář pro řešení** je zaškrtnuté políčko a klikněte na tlačítko **OK** vytvořit projekt Service Fabric mřížky.

![Visual studio Service Fabric mřížky dialogové okno nového projektu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Vytvoření služby

Po kliknutí na **OK**, **nová služba Service Fabric** se zobrazí dialogové okno. Vyberte **ASP.NET Core** typ projektu, ujistěte se, že **Container OS** je nastavena na **Windows** a stiskněte klávesu **OK** vytvořit projekt ASP.NET Core . 

![Visual studio Service Fabric mřížky dialogové okno nového projektu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

**Nová webová aplikace ASP.NET Core** se zobrazí dialogové okno. Vyberte **webovou aplikaci** a potom stiskněte klávesu **OK**.

![Visual studio nové aplikace ASP.NET core](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio vytvoří projekt aplikace Service Fabric mřížky a projekt ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Vytvářejte a publikujte do místního clusteru

Image Dockeru se automaticky vytvořená a publikovaná na místní cluster, co nejdříve po načtení projektu. Tento proces může chvíli trvat. Můžete sledovat průběh nástroje Service Fabric v **výstup** podokně, pokud chcete. Vyberte **Service Fabric Tools** položce v podokně. Můžete pokračovat v práci při nasazení image dockeru.

Po vytvoření projektu stiskněte **F5** ladění služby místně. Po dokončení místní nasazení a váš projekt je spuštěná sada Visual Studio, otevře se okno prohlížeče se ukázkové webové stránky.

Až budete mít procházení v nasazované službě, můžete zastavit ladění váš projekt stisknutím kombinace kláves **Shift + F5** v sadě Visual Studio.

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Před publikováním projektu Service Fabric sítě do Azure, klikněte pravým tlačítkem na **sítě pro Service Fabric project** v sadě Visual studio a vyberte **publikování...**

![Visual studio klikněte pravým tlačítkem na projekt Service Fabric mřížky](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zobrazí se **publikovat aplikaci Service Fabric** dialogového okna.

![Dialogové okno pro publikování aplikace Visual studio Service Fabric mřížky](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Vyberte váš účet a předplatné Azure. Zvolte **umístění**. Tento článek používá **USA – východ**.

V části **skupiny prostředků**vyberte  **\<vytvořit novou skupinu prostředků... >**. Dialogové okno se zobrazí, pokud vytvoříte novou skupinu prostředků. Tento článek používá **USA – východ** umístění a název skupiny **sfmeshTutorial1RG** (Pokud má vaše organizace více uživatelů pomocí stejného předplatného, zvolte jedinečný název skupiny).  Stisknutím klávesy **vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Visual studio Service Fabric mřížky prostředku skupiny dialogové okno Nový](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Zpátky **publikovat aplikaci Service Fabric** dialogového okna, v části **Azure Container Registry**vyberte  **\<vytvořit nový registr kontejneru... >**. V **vytvoření registru kontejneru** dialogového okna, použijte jedinečný název pro **název registru kontejneru**. Zadejte **umístění** (Tento kurz používá **USA – východ**). Vyberte **skupiny prostředků** , kterou jste vytvořili v předchozím kroku v rozevíracím seznamu, například **sfmeshTutorial1RG**. Nastavte **SKU** k **základní** a potom stiskněte klávesu **vytvořit** se vraťte do dialogového okna pro publikování.

Pokud dojde k chybě, která není zaregistrovaný poskytovatel prostředků pro vaše předplatné, můžete ho zaregistrovat. Nejprve zjistěte, zda je k dispozici pro vaše předplatné poskytovatele prostředků:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Pokud zprostředkovatel registru kontejneru (`Microsoft.ContainerRegistry`) je k dispozici, zaregistrujte ho z prostředí Powershell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

![Visual studio Service Fabric mřížky prostředku skupiny dialogové okno Nový](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

V dialogovém okně Publikovat, stiskněte **publikovat** tlačítko pro nasazení aplikace Service Fabric sítě do Azure.

Při prvním publikování do Azure, převede se image dockeru pro Azure Container Registry (ACR) který trvá určitou dobu v závislosti na velikosti image. Další publikuje stejného projektu bude rychlejší. Můžete monitorovat průběh nasazení tak, že vyberete **Service Fabric Tools** podokno v sadě Visual Studio **výstup** okna. Po dokončení nasazení **Service Fabric Tools** výstupu se zobrazí IP adresa a port aplikace ve formuláři adresu URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Otevřete webový prohlížeč a přejděte na adresu URL zobrazíte webu běžícího v Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte všechny prostředky, které jste vytvořili pro účely tohoto rychlého startu. Protože jste vytvořili novou skupinu prostředků pro hostování prostředky služby ACR i sítě pro Service Fabric, můžete bezpečně odstranit tuto skupinu prostředků, což je snadný způsob, jak odstranit všechny prostředky s ním spojená.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternativně můžete odstranit skupinu prostředků [z portálu Azure portal](https://portal.azure.com).

## <a name="next-steps"></a>Další postup
Další informace o vytvoření a nasazení aplikace Service Fabric mřížky, dál najdete v tomto kurzu.
> [!div class="nextstepaction"]
> [Vytvoření a nasazení s více službami webové aplikace](service-fabric-mesh-tutorial-create-dotnetcore.md)
