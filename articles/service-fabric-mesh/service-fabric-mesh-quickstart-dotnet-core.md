---
title: Rychlý start – nasazení webové aplikace do Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Tento rychlý start ukazuje, jak vytvořit web ASP.NET Core a její publikování do Azure Service Fabric sítě pomocí sady Visual Studio.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 35ce19d796a9889d734bd5dde20a7c56485656f1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888532"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Rychlý start: Vytvoření webové aplikace a její nasazení do služby Azure Service Fabric Mesh

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě.

V tomto rychlém startu vytvoříte novou aplikaci Service Fabric Mesh sestávající z webové aplikace ASP.NET Core, spustíte ji v místním clusteru pro vývoj a publikujete ji tak, aby běžela v Azure.

Budete potřebovat předplatné Azure. Pokud ho ještě nemáte, můžete si snadno vytvořit [bezplatný účet Azure](https://azure.microsoft.com/free/), ještě než začnete. Budete také muset [nastavit vývojářské prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Vytvoření projektu Service Fabric Mesh

Spusťte Visual Studio a vyberte **Soubor** > **Nový** > **Projekt**.

Do pole **Hledat** v dialogovém okně **Nový projekt** v horní části zadejte `mesh`. Vyberte šablonu **Service Fabric Mesh Application** (Aplikace Service Fabric Mesh). (Pokud tuto šablonu nevidíte, ujistěte se, že jste nainstalovali sadu SDK Mesh a verzi Preview sady VS Tools podle popisu v části [Nastavení vývojového prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

Do pole **Název** zadejte **ServiceFabricMesh1** a v poli **Umístění** nastavte cestu ke složce, do které se budou ukládat soubory projektu.

Ujistěte se, že je zaškrtnuté políčko **Vytvořit adresář pro řešení**, a kliknutím na **OK** vytvořte projekt Service Fabric Mesh.

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Vytvoření služby

Po kliknutí na **OK** se zobrazí dialogové okno **Nová služba Service Fabricu**. Vyberte typ projektu **ASP.NET Core** a ujistěte se, že je v poli **Operační systém kontejneru** nastavená možnost **Windows**. Pak kliknutím na tlačítko **OK** vytvořte projekt ASP.NET Core. 

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Zobrazí se dialogové okno **Nová webová aplikace ASP.NET Core**. Vyberte **Webová aplikace** a potom klikněte na **OK**.

![Nová aplikace ASP.NET Core v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio vytvoří projekt aplikace Service Fabric Mesh a projekt ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Sestavení a publikování v místním clusteru

Po načtení projektu se image Dockeru automaticky sestaví a publikuje do místního clusteru. Tento proces může chvíli trvat. Pokud chcete, můžete průběh u nástrojů Service Fabric monitorovat v podokně **Výstup** – stačí vybrat položku **Nástroje Service Fabric** v rozevíracím seznamu okna **Výstup**. Při nasazování image Dockeru můžete pokračovat v práci.

Po vytvoření projektu klikněte na **F5**, abyste mohli službu vyladit místně. Až se místní nasazení dokončí a Visual Studio spustí váš projekt, otevře se okno prohlížeče s ukázkovou webovou stránkou.

Až procházení nasazené služby dokončíte, zastavte ladění projektu stisknutím kláves **Shift + F5** v sadě Visual Studio.

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Pokud chcete projekt Service Fabric Mesh publikovat v Azure, klikněte v sadě Visual Studio na daný **projekt Service Fabric Mesh** pravým tlačítkem myši a vyberte **Publikovat**.

![Kliknutí pravým tlačítkem myši na projekt Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Zvolte **Umístění**. Tento článek používá umístění **USA – východ**.

V části **Skupina prostředků** vyberte **\<Vytvořit novou skupinu prostředků...>**. Zobrazí se dialogové okno **Vytvořit skupinu prostředků**. Nastavte **název skupiny prostředků** a **umístění**.  Tento rychlý start pracuje s umístěním **USA – východ** a názvem skupiny **sfmeshTutorial1RG** (pokud vaše organizace má více lidí, kteří používají stejné předplatné, zvolte jedinečný název skupiny prostředků).  Kliknutím na **Vytvořit** vytvořte skupinu prostředků a vraťte se do dialogového okna pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

V dialogovém okně **Publikovat aplikaci Service Fabricu** vyberte v části **Azure Container Registry** možnost **\<Vytvořit nový registr kontejneru...>**. V dialogovém okně **Vytvořit registr kontejneru** použijte pro **registr kontejneru** jedinečný název. Zadejte **umístění** (tento rychlý start používá **USA – východ**). V rozevírací nabídce vyberte **skupinu prostředků**, kterou jste vytvořili v předchozím kroku, například **sfmeshTutorial1RG**. **Skladovou položku** nastavte na **Basic** a kliknutím na **Vytvořit** se vraťte do dialogu pro publikování.

![Dialogové okno nové skupiny prostředků Service Fabric Mesh v sadě Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

V dialogu pro publikování klikněte na tlačítko **Publikovat** a nasaďte aplikaci Service Fabric Mesh do Azure.

Při prvním publikování do Azure se do služby ACR (Azure Container Registry) nahraje image dockeru, což v závislosti na velikosti image může nějakou chvíli trvat. Následná publikování stejného projektu budou rychlejší. Průběh nasazení můžete monitorovat výběrem položky **Nástroje Service Fabric** v rozevíracím seznamu okna **Výstup** v sadě Visual Studio. Po dokončení nasazení se ve výstupu **Nástroje Service Fabric** zobrazí IP adresa a port aplikace v podobě adresy URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure:

![Spuštění webové aplikace Service Fabric Mesh](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené pro tento rychlý start nepotřebujete, všechny je odstraňte. Protože jste vytvořili novou skupinu prostředků za účelem hostování prostředků služby ACR a Service Fabric Mesh, můžete ji bezpečně odstranit, což je jednoduchý způsob, jak odstranit také všechny k ní přidružené prostředky.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Skupinu prostředků můžete také odstranit přes [Azure Portal](https://portal.azure.com).

## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o vytváření a nasazování aplikací Service Fabric Mesh, pokračujte v tomto kurzu.
> [!div class="nextstepaction"]
> [Vytvoření a ladění webové aplikace s více službami a její nasazení do služby Azure Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
