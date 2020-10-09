---
title: Kurz – upgrade aplikace sítě pro Azure Service Fabric
description: Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat aplikaci Service Fabric sítě Azure přímo ze sady Visual Studio.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 2d74841e1873e5bd84d839122e0e662ef65ccd11
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841998"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Kurz: informace o tom, jak upgradovat Service Fabric aplikaci pomocí sady Visual Studio

Tento kurz je čtvrtou částí série a ukazuje, jak upgradovat aplikaci Service Fabric sítě Azure přímo ze sady Visual Studio. Upgrade bude zahrnovat aktualizaci kódu i aktualizaci konfigurace. Uvidíte, že kroky pro upgrade a publikování v rámci sady Visual Studio jsou stejné.

Co se v tomto kurzu naučíte:
> [!div class="checklist"]
> * Upgrade služby Service Fabric sítě pomocí sady Visual Studio

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

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Upgrade služby Service Fabric sítě pomocí sady Visual Studio

V tomto článku se dozvíte, jak upgradovat mikroslužbu v rámci aplikace. V tomto příkladu změníme `WebFrontEnd` službu tak, aby se zobrazila kategorie úlohy, a zvýšilo se množství procesoru, které je dané. Pak upgraduje nasazenou službu.

## <a name="modify-the-config"></a>Úprava konfigurace

Když vytvoříte aplikaci Service Fabric mřížka, Visual Studio přidá soubor **Parameters. yaml** pro každé prostředí nasazení (Cloud a místní). V těchto souborech můžete definovat parametry a jejich hodnoty, na které se pak můžete odkazovat z vaší sítě * soubory YAML, jako je Service. YAML nebo Network. yaml.  Visual Studio poskytuje některé proměnné za vás, například kolik CPU může služba využívat.

Aktualizujeme `WebFrontEnd_cpu` parametr a aktualizujeme prostředky procesoru na, aby se `1.5` předpokládalo, že se služba **webendu** bude intenzivně používat.

1. V projektu **todolistapp** v části **prostředí**  >  **cloudu**otevřete soubor **Parameters. yaml** . Změňte `WebFrontEnd_cpu` hodnotu na `1.5` . Název parametru je před názvem služby `WebFrontEnd_` doporučený postup pro odlišení od parametrů stejného názvu, které se vztahují k různým službám.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Otevřete soubor **Service. yaml** projektu **webendu** v části **WebFrontEnd**  >  **prostředky služby**webendu.

    Všimněte si, že v `resources:` části v `cpu:` je nastavena na `"[parameters('WebFrontEnd_cpu')]"` . Pokud projekt sestavíte pro Cloud, hodnota pro `'WebFrontEnd_cpu` bude provedena z **Environments**  >  **cloudových**  >  **parametrů prostředí. yaml** soubor a bude `1.5` . Pokud je projekt sestaven tak, aby běžel místně, hodnota bude provedena z **prostředí**  >  **místních**  >  **parametrů. yaml** a bude "0,5".

> [!Tip]
> Ve výchozím nastavení se soubor parametrů, který je partnerským souborem profilu. yaml, použije k zadání hodnot pro tento soubor Profile. yaml.
> Například prostředí > cloudových > Parameters. yaml poskytuje hodnoty parametrů pro prostředí > cloudového > Profile. yaml.
>
> To můžete přepsat přidáním následujícího do souboru Profile. yaml `parametersFilePath=”relative or full path to the parameters file”` , například `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` nebo. `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Úprava modelu

Chcete-li zavést změnu kódu, přidejte do `Category` `ToDoItem` třídy v `ToDoItem.cs` souboru vlastnost.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Pak aktualizujte `Load()` metodu do stejného souboru, chcete-li nastavit kategorii na výchozí řetězec:

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

`WebFrontEnd`Projekt je ASP.NET Core aplikace s webovou stránkou, která zobrazuje položky seznamu úkolů. V `WebFrontEnd` projektu otevřete `Index.cshtml` a přidejte následující dva řádky, které jsou uvedeny níže, aby se zobrazila Kategorie úkolu:

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

Sestavte a spusťte aplikaci, abyste ověřili, že se na webové stránce zobrazí nový sloupec kategorie, který obsahuje seznam úkolů.

## <a name="upgrade-the-app-from-visual-studio"></a>Upgrade aplikace ze sady Visual Studio

Bez ohledu na to, jestli provádíte upgrade kódu nebo upgrade konfigurace (v tomto případě provádíme obojí), upgradujte svou Service Fabricovou aplikaci v Azure tak, že kliknete pravým tlačítkem na **todolistapp** v aplikaci Visual Studio a pak vyberete **publikovat...**

Zobrazí se dialogové okno **Publikovat aplikaci Service Fabricu**.

Pomocí rozevíracího seznamu **cílový profil** vyberte soubor Profile. yaml, který chcete použít pro toto nasazení. Upgradujete aplikaci v cloudu, takže v rozevíracím seznamu vybereme soubor **Cloud. yaml** , který bude používat `WebFrontEnd_cpu` hodnotu 1,0 definovanou v tomto souboru.

![Dialogové okno pro publikování aplikace Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Vyberte účet a předplatné Azure. Nastavte **umístění** na místo, které jste použili při původním publikování aplikace v aplikaci do Azure. Tento článek se používá **východní USA**.

Nastavte **skupinu prostředků** na skupinu prostředků, kterou jste použili při původním publikování aplikace v aplikaci do Azure.

Nastavte **Azure Container Registry** na název služby Azure Container Registry, který jste vytvořili při původním publikování aplikace v aplikaci do Azure.

Kliknutím na tlačítko **publikovat** v dialogovém okně Publikovat provedete upgrade aplikace na aplikaci v Azure.

Sledujte průběh upgradu tak, že v okně **výstup** sady Visual Studio vyberete podokno **nástroje Service Fabric** . 

Po sestavení obrázku a vložení do Azure Container Registry se ve výstupu zobrazí odkaz **pro stav** , ve kterém můžete kliknout a monitorovat nasazení v Azure Portal.

Po dokončení upgradu zobrazí **nástroje Service Fabric** výstup IP adresu a port vaší aplikace ve formě adresy URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Spusťte webový prohlížeč a přejděte na tuto adresu URL, abyste se mohli podívat na web spuštěný v Azure. Nyní by se měla zobrazit webová stránka, která obsahuje sloupec kategorie.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se dozvěděli:
> [!div class="checklist"]
> * Postup upgradu aplikace Service Fabric sítě pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Vyčištění prostředků Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)