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
ms.date: 11/29/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 0f6ede488ae118f8df00febda3c53eabb73f2030
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890224"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Kurz: Přečtěte si pokyny k upgradu aplikace Service Fabric pomocí sady Visual Studio

Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat aplikaci Azure Service Fabric mřížky přímo ze sady Visual Studio. Upgrade bude obsahovat aktualizace kódu a aktualizaci konfigurace. Uvidíte, že kroky pro upgrade a publikování ze sady Visual Studio jsou stejné.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Upgrade služby sítě pro Service Fabric pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvořit aplikaci Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladit aplikaci Service Fabric Mesh běžící v místním clusteru pro vývoj](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Nasadit aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Upgradu prostředků infrastruktury sítě služby app
> * [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud jste nenasadili aplikaci seznamu úkolů, postupujte podle pokynů v článku o [publikování webové aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Upgrade služby sítě pro Service Fabric pomocí sady Visual Studio

Tento článek ukazuje, jak upgradovat mikroslužbách v rámci aplikace. V tomto příkladu upravíme `WebFrontEnd` služby zobrazit kategorii úkolů a zvýšit procento využití procesoru je uveden. Potom provedeme váš upgrade v nasazované službě.

## <a name="modify-the-config"></a>Upravit konfigurace

Když vytvoříte aplikaci Service Fabric mřížky, Visual studio přidá **parameters.yaml** soubor pro každé prostředí nasazení (cloudové a místní). V těchto souborů můžete definovat parametry a jejich hodnoty, které lze odkazovat ze svých souborů *.yaml sítě například service.yaml nebo network.yaml.  Visual Studio poskytuje několik proměnných, například kolik procesorů může služba použít.

Aktualizujeme `WebFrontEnd_cpu` parametr aktualizovat prostředky procesoru, které `1.5` čekat na případná, který **WebFrontEnd** nevyčerpáte použijete službu.

1. V **todolistapp** projektu v části **prostředí** > **cloudu**, otevřete **parameters.yaml** souboru. Upravit `WebFrontEnd_cpu`, hodnota, která se `1.5`. Název parametru je uvedena s názvem služby `WebFrontEnd_` jako osvědčený postup, aby se odlišil od parametry se stejným názvem, které platí pro různé služby.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otevřít **WebFrontEnd** projektu **service.yaml** soubor **WebFrontEnd** > **prostředky služby**.

    Všimněte si, že v `resources:` části `cpu:` je nastavena na `"[parameters('WebFrontEnd_cpu')]"`. Pokud se sestavení projektu pro cloud, hodnota `'WebFrontEnd_cpu` vytvářena z **prostředí** > **cloudu** > **parameters.yaml** souboru a bude `1.5`. Pokud se sestavení projektu spouštět místně, hodnota vytvářena z **prostředí** > **místní** > **parameters.yaml** souboru, a bude "0.5".

> [!Tip]
> Ve výchozím nastavení se použije k zadání hodnot pro tento soubor profile.yaml soubor parametrů, která je sdílená profile.yaml souboru.
> Například prostředí > Cloud > parameters.yaml obsahuje hodnoty parametrů pro prostředí > Cloud > profile.yaml.
>
> Toto můžete přepsat přidáním následujícího kódu do souboru profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` například `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` nebo `parametersFilePath=”..\CommonParameters.yaml”`

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

Jestli provádíte upgrade kódu nebo konfigurace upgradu (v tomto případě provádíme obojí), upgradujte aplikace Service Fabric sítě v Azure kliknutím pravým tlačítkem na **todolistapp** v sadě Visual Studio a pak vyberte **publikování...**

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

Použít **cílový profil** rozevírací seznam a vyberte soubor profile.yaml určený pro toto nasazení. Jsme provádíte upgrade aplikace v cloudu, takže vybereme **cloud.yaml** v rozevírací nabídce, která bude používat `WebFrontEnd_cpu` hodnota 1,0 definované v tomto souboru.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Nastavte **umístění** do umístění, které jste použili při původně publikované aplikace to-do do Azure. Tento článek používá **USA – východ**.

Nastavte **skupiny prostředků** do skupiny prostředků, který jste použili při původně publikované aplikace to-do do Azure.

Nastavte **Azure Container Registry** pro název registru kontejneru azure, který jste vytvořili při původně publikované aplikace to-do do Azure.

V dialogovém okně Publikovat, stiskněte **publikovat** tlačítko upgradovat aplikaci seznamu úkolů v Azure.

Průběh upgradu sledovat tak, že vyberete **Service Fabric Tools** podokno v sadě Visual Studio **výstup** okna. 

Po vytvoření image a nahráli do Azure Container Registry **stav** odkazu se zobrazí ve výstupu, který můžete kliknout a monitorujte nasazení na webu Azure Portal.

Po dokončení upgradu **Service Fabric Tools** výstupu se zobrazí IP adresa a port aplikace ve formuláři adresu URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure. Měli byste vidět webovou stránku, která obsahuje sloupec kategorie.

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Postup upgradu na aplikaci Service Fabric sítě s použitím sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)