---
title: Kurz upgradu aplikace Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Informace o upgradu aplikace Service Fabric pomocí sady Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: c23646bca6109d27e57b2f928363e65c83c634eb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031148"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Kurz: Přečtěte si pokyny k upgradu aplikace Service Fabric pomocí sady Visual Studio

Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat aplikaci Azure Service Fabric mřížky přímo ze sady Visual Studio. Upgrade bude obsahovat aktualizace kódu a aktualizaci konfigurace. Uvidíte, že kroky pro upgrade a publikování ze sady Visual Studio jsou stejné.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Upgrade služby sítě pro Service Fabric pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření prostředků infrastruktury sítě služby app v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladění aplikace Service Fabric sítě ve vašem místním vývojovém clusteru spuštěná](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Nasazení prostředků infrastruktury sítě služby app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Upgradu prostředků infrastruktury sítě služby app
> * [Vyčištění prostředků sítě pro Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud jste nenasadili aplikace to-do, postupujte podle pokynů v [publikovat webovou aplikaci Service Fabric mřížky](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Upgrade služby sítě pro Service Fabric pomocí sady Visual Studio

Tento článek ukazuje, jak upgradovat nezávisle na sobě mikroslužeb v rámci aplikace.  V tomto příkladu upravíme `WebFrontEnd` služby zobrazit kategorii úkolů. Potom provedeme váš upgrade v nasazované službě.

## <a name="modify-the-config"></a>Upravit konfigurace

Upgrade může být z důvodu změn v kódu a změny konfigurace.  Zavést změní konfiguraci, otevřete `WebFrontEnd` projektu `service.yaml` souboru (která se nachází v **prostředky služby** uzlu).

V `resources:` oddíl, změna `cpu:` z 0,5 1.0 očekávaly, že webový front-end se nejčastěji používá. To by teď měl vypadat takto:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Upravit model

Zavést změny kódu, přidejte `Category` vlastnost `ToDoItem` třídy v `ToDoItem.cs` souboru.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Pak aktualizujte `Load()` metoda ve stejném souboru, pro kategorii nastavíme na výchozí řetězec:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Upravit službu

`WebFrontEnd` Projekt je aplikace ASP.NET Core s webovou stránkou, která zobrazuje seznam položek úkolů. V `WebFrontEnd` projekt, otevřete `Index.cshtml` a přidejte následující dva řádky uvedené níž, chcete-li zobrazit kategorie úkolu:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Sestavení a spuštění aplikace a zkontrolujte, jestli se nový sloupec kategorie na webové stránce, která jsou uvedeny úlohy.

## <a name="upgrade-the-app-from-visual-studio"></a>Upgrade aplikace ze sady Visual Studio

Bez ohledu na to, jestli provádíte upgrade kódu nebo konfigurace upgradu (v tomto případě se nám daří obojí), k upgradu aplikace Service Fabric mřížky na Azure klikněte pravým tlačítkem na **todolistapp** v sadě Visual Studio a vyberte **publikovat ...**

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Ujistěte se, že **umístění** nastavená na umístění, které jste použili při původně publikované aplikace to-do do Azure. Tento článek používá **USA – východ**.

Ujistěte se, že **skupiny prostředků** je nastavena na skupinu prostředků, který jste použili při původně publikované aplikace to-do do Azure.

Ujistěte se, že **Azure Container Registry** je nastavena na název registru kontejneru azure, který jste vytvořili při původně publikované aplikace to-do do Azure.

V dialogovém okně Publikovat, stiskněte **publikovat** tlačítko upgradovat aplikaci seznamu úkolů v Azure.

Průběh upgradu můžete monitorovat tak, že vyberete **Service Fabric Tools** podokno v sadě Visual Studio **výstup** okna. Po dokončení upgradu **Service Fabric Tools** výstupu se zobrazí IP adresa a port aplikace ve formuláři adresu URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure. Měli byste vidět webovou stránku, která obsahuje sloupec kategorie.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Postup upgradu aplikace Service Fabric MES pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Vyčištění prostředků sítě pro Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)