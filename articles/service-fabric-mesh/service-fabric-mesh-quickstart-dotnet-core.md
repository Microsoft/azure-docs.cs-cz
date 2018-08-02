---
title: Rychlý start – vytvoření a nasazení webové aplikace do Azure Service Fabric mřížky | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvořit web ASP.NET Core a její publikování do Azure Service Fabric mřížky.
services: service-fabric-mesh
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
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: d14420a363cfea23c86f63533a4ea89c5f2fd06f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412912"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Rychlý start: Vytvoření a nasazení webové aplikace do Azure Service Fabric mřížky

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě.

V tomto rychlém startu vytvoříte novou aplikaci Service Fabric sítě skládající se z webové aplikace ASP.NET Core spusťte ho na místní cluster pro vývoj a publikuje ho spustit v Azure.

Budete potřebovat předplatné Azure. Pokud ho nemáte, můžete snadno vytvořit bezplatné předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete. Budete také muset [nastavení vývojářského prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Vytvoření projektu Service Fabric Mesh

Otevřít Visual Studio a vyberte **souboru** > **nový** > **projektu...**

Do pole **Hledat** v dialogovém okně **Nový projekt** v horní části zadejte `mesh`. Vyberte šablonu **Service Fabric Mesh Application** (Aplikace Service Fabric Mesh). (Pokud tuto šablonu nevidíte, ujistěte se, že jste nainstalovali sadu SDK Mesh a verzi Preview sady VS Tools podle popisu v části [Nastavení vývojového prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

V **název** zadejte **ServiceFabricMesh1** a **umístění** pole, nastavte cestu složky, kde budou uloženy soubory projektu.

Ujistěte se, že je zaškrtnuté políčko **Vytvořit adresář pro řešení**, a kliknutím na **OK** vytvořte projekt Service Fabric Mesh.

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Vytvoření služby

Po kliknutí na **OK**, **nová služba Service Fabric** se zobrazí dialogové okno. Vyberte **ASP.NET Core** typ projektu, ujistěte se, že **Container OS** je nastavena na **Windows** a klikněte na tlačítko **OK** vytvořit projekt ASP.NET Core . 

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

**Nová webová aplikace ASP.NET Core** se zobrazí dialogové okno. Vyberte **webovou aplikaci** a potom klikněte na tlačítko **OK**.

![Nová aplikace ASP.NET Core v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio vytvoří projekt aplikace Service Fabric mřížky a projekt ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Vytvářejte a publikujte do místního clusteru

Image Dockeru je automaticky vytvořená a publikovaná na místní cluster poté, co váš projekt načten. Tento proces může chvíli trvat. Můžete sledovat průběh nástroje Service Fabric v **výstup** okno, pokud chcete, aby tak, že vyberete **Service Fabric Tools** položky v **výstup** rozevírací okno. Můžete pokračovat v práci při nasazení image dockeru.

Po vytvoření projektu klikněte na tlačítko **F5** ladění služby místně. Po dokončení místní nasazení a váš projekt je spuštěná sada Visual Studio, otevře se okno prohlížeče se ukázkové webové stránky.

Až budete mít procházení nasazené služby, zastavení ladění váš projekt stisknutím kombinace kláves **Shift + F5** v sadě Visual Studio.

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Před publikováním projektu Service Fabric sítě do Azure, klikněte pravým tlačítkem na **sítě pro Service Fabric project** v sadě Visual studio a vyberte **publikování...**

![Visual studio klikněte pravým tlačítkem na projekt Service Fabric mřížky](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zobrazí se **publikovat aplikaci Service Fabric** dialogového okna.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Zvolte **Umístění**. Tento článek používá umístění **USA – východ**.

V části **Skupina prostředků** vyberte **\<Vytvořit novou skupinu prostředků...>**. **Vytvořit skupinu prostředků** se zobrazí dialogové okno. Nastavte **název skupiny prostředků** a **umístění**.  Tento rychlý start využívá **USA – východ** umístění a název skupiny **sfmeshTutorial1RG** (Pokud má vaše organizace více uživatelů pomocí stejného předplatného, zvolte název skupiny prostředků jedinečný).  Klikněte na tlačítko **vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

V dialogovém okně **Publikovat aplikaci Service Fabricu** vyberte v části **Azure Container Registry** možnost **\<Vytvořit nový registr kontejneru...>**. V dialogovém okně **Vytvořit registr kontejneru** použijte pro **registr kontejneru** jedinečný název. Zadejte **umístění** (v tomto rychlém startu používá **USA – východ**). V rozevírací nabídce vyberte **skupinu prostředků**, kterou jste vytvořili v předchozím kroku, například **sfmeshTutorial1RG**. Nastavte **SKU** k **základní** a potom klikněte na tlačítko **vytvořit** se vraťte do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

V dialogovém okně Publikovat, klikněte na tlačítko **publikovat** tlačítko pro nasazení aplikace Service Fabric sítě do Azure.

Při prvním publikování do Azure se do služby ACR (Azure Container Registry) nahraje image dockeru, což v závislosti na velikosti image může nějakou chvíli trvat. Následná publikování stejného projektu budou rychlejší. Můžete monitorovat průběh nasazení tak, že vyberete **Service Fabric Tools** v sadě Visual Studio **výstup** rozevírací okno. Po dokončení nasazení se ve výstupu **Nástroje Service Fabric** zobrazí IP adresa a port aplikace v podobě adresy URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Otevřete webový prohlížeč a přejděte na adresu URL zobrazíte webu běžícího v Azure:

![Spuštění aplikace Service Fabric mřížky Web](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte všechny prostředky, které jste vytvořili pro účely tohoto rychlého startu. Protože jste vytvořili novou skupinu prostředků pro hostování prostředky služby ACR i sítě pro Service Fabric, můžete bezpečně odstranit tuto skupinu prostředků, která je snadný způsob, jak odstranit všechny prostředky s ním spojená.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternativně můžete odstranit skupinu prostředků [z portálu Azure portal](https://portal.azure.com).

## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o vytváření a nasazování aplikací Service Fabric Mesh, pokračujte v tomto kurzu.
> [!div class="nextstepaction"]
> [Vytvoření a ladění webové aplikace s více službami a její nasazení do služby Azure Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)