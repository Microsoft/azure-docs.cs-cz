---
title: Kurz – upgrade aplikace Azure Service Fabric Mesh
description: Tento kurz je čtvrtá část řady a ukazuje, jak upgradovat aplikaci Azure Service Fabric Mesh přímo z Visual Studia.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351718"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Kurz: Naučte se upgradovat aplikaci Service Fabric pomocí sady Visual Studio

Tento kurz je čtvrtá část řady a ukazuje, jak upgradovat aplikaci Azure Service Fabric Mesh přímo z Visual Studia. Upgrade bude zahrnovat aktualizaci kódu i aktualizaci konfigurace. Uvidíte, že kroky pro upgrade a publikování z v rámci sady Visual Studio jsou stejné.

Co se v tomto kurzu naučíte:
> [!div class="checklist"]
> * Upgrade služby Service Fabric Mesh pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Vytvoření aplikace Service Fabric Mesh v sadě Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Ladit aplikaci Service Fabric Mesh běžící v místním clusteru pro vývoj](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Nasadit aplikaci Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Upgrade aplikace Service Fabric Mesh
> * [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud jste nenasadili aplikaci seznamu úkolů, postupujte podle pokynů v článku o [publikování webové aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Upgrade služby Service Fabric Mesh pomocí sady Visual Studio

Tento článek ukazuje, jak upgradovat mikroslužbu v rámci aplikace. V tomto příkladu upravíme službu `WebFrontEnd` tak, aby zobrazovala kategorii úkolů a zvýšila množství procesoru, které je k dispozici. Pak inovujeme nasazenou službu.

## <a name="modify-the-config"></a>Změna konfigurace

Když vytvoříte aplikaci Service Fabric Mesh, Visual Studio přidá soubor **parameters.yaml** pro každé prostředí nasazení (cloud a místní). V těchto souborech můžete definovat parametry a jejich hodnoty, na které pak lze odkazovat ze souborů Mesh *.yaml, například service.yaml nebo network.yaml.  Visual Studio poskytuje některé proměnné pro vás, například kolik procesoru služba může používat.

Aktualizujeme `WebFrontEnd_cpu` parametr pro aktualizaci prostředků `1.5` procesoru v očekávání, že služba **WebFrontEnd** bude více využívána.

1. V projektu **todolistapp** otevřete v části **Environments** > **Cloud**soubor **parameters.yaml.** Upravte `WebFrontEnd_cpu`hodnotu `1.5`, na . Název parametru je označen názvem `WebFrontEnd_` služby jako osvědčený postup k jeho odlišení od parametrů se stejným názvem, které platí pro různé služby.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otevřete soubor **service.yaml** projektu **WebFrontEnd** v části**Zdroje služeb** **WebFrontEnd** > .

    Všimněte si, `cpu:` že v `"[parameters('WebFrontEnd_cpu')]"` `resources:` části, je nastavena na . Pokud je projekt sektován pro `'WebFrontEnd_cpu` cloud, hodnota pro bude převzata ze souboru **Environments** > **Cloud** > **parameters.yaml** a bude `1.5`. Pokud je projekt sektován tak, aby byl spuštěn místně, bude hodnota převzata ze souboru **Environments** > **Local** > **parameters.yaml** a bude "0.5".

> [!Tip]
> Ve výchozím nastavení bude soubor parametrů, který je rovnocenným partnerem souboru profile.yaml, použit k zadání hodnot pro tento soubor profile.yaml.
> Například Environments > Cloud > parameters.yaml poskytuje hodnoty parametrů pro prostředí > Cloud > profile.yaml.
>
> Tuto možnost můžete přepsat přidáním následujícího příkazu do`parametersFilePath=”relative or full path to the parameters file”` souboru `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` profile.yaml: Například nebo`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Úprava modelu

Chcete-li zavést změnu kódu, přidejte `Category` vlastnost do `ToDoItem` třídy v souboru. `ToDoItem.cs`

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Potom aktualizujte metodu `Load()` ve stejném souboru a nastavte kategorii na výchozí řetězec:

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

## <a name="modify-the-service"></a>Úprava služby

Projekt `WebFrontEnd` je ASP.NET základní aplikace s webovou stránkou, která zobrazuje položky seznamu úkolů. V `WebFrontEnd` projektu otevřete `Index.cshtml` a přidejte následující dva řádky, které jsou uvedeny níže, pro zobrazení kategorie úkolu:

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

Vytvořte a spusťte aplikaci a ověřte, zda se na webové stránce se seznamem úkolů zobrazuje nový sloupec kategorie.

## <a name="upgrade-the-app-from-visual-studio"></a>Upgrade aplikace z Visual Studia

Ať už provádíte upgrade kódu nebo upgrade konfigurace (v tomto případě děláme obojí), upgradujte aplikaci Service Fabric Mesh v Azure kliknutím pravým tlačítkem myši na **todolistapp** v sadě Visual Studio a pak vyberte **Publikovat...**

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

Pomocí rozevíracího nabídky **Cílový profil** vyberte soubor profile.yaml, který chcete použít pro toto nasazení. Upgradujeme aplikaci v cloudu, takže v rozbalovací části vybereme **cloud.yaml,** který použije `WebFrontEnd_cpu` hodnotu 1.0 definovanou v tomto souboru.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Nastavte **umístění** na umístění, které jste použili při původním publikování aplikace pro práci do Azure. Tento článek používá **východní USA**.

Nastavte **skupinu prostředků** na skupinu prostředků, kterou jste použili při původním publikování aplikace pro práci do Azure.

Nastavte **Azure Container Registry** na název registru kontejneru Azure, který jste vytvořili při původním publikování aplikace pro práci do Azure.

V dialogovém okně publikovat stiskněte tlačítko **Publikovat** a upgradujte aplikaci pro práci v Azure.

Sledujte průběh upgradu výběrem podokna **Nástroje prostředků infrastruktury služeb** v okně **Výstup** sady Visual Studio. 

Po vytvoření bitové kopie a jeho zasunutí do registru kontejnerů Azure se ve výstupu, na který můžete kliknout a sledovat nasazení na webu Azure Portal, zobrazí odkaz **Pro.**

Po dokončení upgradu se ve výstupu **Nástroje prostředků pro služby** zobrazí adresa IP a port aplikace ve formě adresy URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure. Nyní byste měli vidět webovou stránku, která obsahuje sloupec kategorie.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se dozvěděli:
> [!div class="checklist"]
> * Jak upgradovat aplikaci Service Fabric Mesh pomocí Sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)