---
title: 'Kurz: Vytvoření a ladění webové aplikace s více službami a její nasazení do služby Service Fabric Mesh | Microsoft Docs'
description: V tomto kurzu vytvoříte aplikaci Azure Service Fabric Mesh s více službami složenou z webu ASP.NET Core, který komunikuje s back-endovou webovou službou, provedete její místní ladění a publikujete ji do Azure.
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
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 59ff3434e7b984f4530ad4f8b03b27991d3a9c1c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919520"
---
# <a name="tutorial-create-debug-and-deploy-a-multi-service-web-application-to-service-fabric-mesh"></a>Kurz: Vytvoření a ladění webové aplikace s více službami a její nasazení do služby Azure Service Fabric Mesh

Tento kurz je první částí série. Dozvíte se, jak vytvořit aplikaci Azure Service Fabric Mesh s webovým front-endem ASP.NET Core a back-endovou službou s webovým rozhraním API ASP.NET Core. Potom provedete ladění aplikace v místním clusteru pro vývoj a publikujete do Azure. Na konci tak získáte jednoduchou aplikaci s úkoly, která předvádí volání typu služba-služba v aplikaci Service Fabric spuštěné ve službě Azure Service Fabric Mesh.

Pokud nechcete aplikaci s úkoly vytvářet ručně, můžete si [stáhnout zdrojový kód](https://github.com/azure-samples/service-fabric-mesh) dokončené aplikace a přeskočit k části [Místní ladění aplikace](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikaci Service Fabric Mesh, která se skládá z webového front-endu ASP.NET
> * Vytvořit model, který reprezentuje položky úkolů
> * Vytvořit back-endovou službu a načíst z ní data
> * Přidat kontroler a třídu DataContext v rámci architektury model-zobrazení-kontroler pro back-endovou službu
> * Vytvořit webovou stránku pro zobrazení položek úkolů
> * Vytvořit proměnné prostředí, které identifikují back-endovou službu

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Sestavit aplikaci Service Fabric Mesh
> * [Ladit aplikaci v místním prostředí](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Publikovat aplikaci do Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Zkontrolujte, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md), což zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Dockeru a sady Visual Studio 2017.

* Aplikaci pro účely tohoto kurzu musíte prozatím sestavit v anglickém národním prostředí.

## <a name="create-a-service-fabric-mesh-project"></a>Vytvoření projektu Service Fabric Mesh

Spusťte Visual Studio a vyberte **Soubor** > **Nový** > **Projekt…**

Do pole **Hledat** v dialogovém okně **Nový projekt** v horní části zadejte `mesh`. Vyberte šablonu **Service Fabric Mesh Application** (Aplikace Service Fabric Mesh). (Pokud tuto šablonu nevidíte, ujistěte se, že jste nainstalovali sadu SDK Mesh a verzi Preview sady VS Tools podle popisu v části [Nastavení vývojového prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

Do pole **Název** zadejte `todolistapp` a v poli **Umístění** nastavte cestu ke složce, do které chcete uložit soubory projektu.

Ujistěte se, že je zaškrtnuté políčko **Vytvořit adresář pro řešení**, a kliknutím na **OK** vytvořte projekt Service Fabric Mesh.

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Potom se zobrazí dialogové okno **Nová služba Service Fabricu**.

### <a name="create-the-web-front-end-service"></a>Vytvoření služby webového front-endu

V dialogovém okně **Nová služba Service Fabricu** vyberte typ projektu **ASP.NET Core** a ujistěte se, že je v poli **Operační systém kontejneru** nastavená možnost **Windows**.

V poli **Název služby** nastavte **WebFrontEnd**. Stisknutím **OK** vytvořte službu ASP.NET Core.

![Dialogové okno nového projektu Service Fabric Mesh v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Potom se zobrazí dialogové okno **Nová webová aplikace ASP.NET Core**. V dialogovém okně **Nová webová aplikace ASP.NET Core** vyberte **Webová aplikace** a potom klikněte na **OK**.

![Nová aplikace ASP.NET Core v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Právě jste vytvořili aplikaci Service Fabric Mesh. Dál vytvoříte model pro informace o úkolech.

## <a name="create-the-to-do-items-model"></a>Vytvoření modelu položek úkolů

Pro větší jednoduchost se položky úkolů ukládají do paměti ve formě seznamu. Vytvořte knihovnu tříd pro položky úkolů a seznam, ve kterém budou uložené. V sadě Visual Studio, kde máte aktuálně načtený projekt **todolistapp**, zvolte **Soubor** > **Přidat** > **Nový projekt**.

Do pole **Hledat** v dialogovém okně **Nový projekt** v horní části zadejte `C# .net core class`. Vyberte šablonu **Knihovna tříd (.NET Core)**.

Do pole **Název** zadejte `Model`. Kliknutím na tlačítko **OK** vytvořte knihovnu tříd.

V Průzkumníku řešení v části **Model** klikněte pravým tlačítkem na **Class1.cs** a zvolte **Přejmenovat**. Přejmenujte třídu na **ToDoItem.cs**. Pokud se zobrazí výzva s dotazem, jestli se mají přejmenovat všechny odkazy, klikněte na **Ano**.

Nahraďte obsah prázdné třídy `class ToDoItem` tímto obsahem:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Tato třída představuje jednotlivé položky úkolů.

V sadě Visual Studio klikněte pravým tlačítkem na knihovnu tříd **Model** a vyberte **Přidat** > **Třída…**, aby se vytvořil seznam, ve do kterého se položky úkolů uloží. Zobrazí se dialogové okno **Přidat novou položku**. V poli **Název** nastavte `ToDoList.cs` a klikněte na **Přidat**.

Ve třídě **ToDoList.cs** nahraďte prázdný obsah `class ToDoList` obsahem:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Potom vytvořte službu Service Fabric, která bude položky úkolů sledovat.

## <a name="create-the-back-end-service"></a>Vytvoření back-endové služby

V okně **Průzkumník řešení** sady Visual Studio klikněte pravým tlačítkem na **todolistapp** a klikněte na **Přidat** > **Nová služba Service Fabricu…**

Zobrazí se dialogové okno **Nová služba Service Fabricu**. Vyberte typ projektu **ASP.NET Core** a ujistěte se, že je v poli **Operační systém kontejneru** nastavená možnost **Windows**.

V poli **Název služby** nastavte **ToDoService**. Kliknutím na **OK** vytvořte službu ASP.NET Core. Zobrazí se dialogové okno **Nová webová aplikace ASP.NET Core**. V tomto dialogovém okně vyberte **Rozhraní API** a klikněte na **OK**. Do řešení se přidá projekt pro tuto službu.

![Nová aplikace ASP.NET Core v sadě Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Back-endová služba neposkytuje žádné uživatelské rozhraní, proto vypněte spouštění prohlížeče při spuštění služby. V **Průzkumníku řešení** klikněte pravým tlačítkem na službu **ToDoService** a vyberte **Vlastnosti**. Zobrazí se okno vlastností. Vlevo vyberte kartu **Ladit** a zrušte zaškrtnutí políčka **Spustit prohlížeč**. Uložte změnu stisknutím kláves **Ctrl+S**.

Jelikož tato služba udržuje informace o úkolech, přidejte odkaz na knihovnu tříd Model. V Průzkumníku řešení klikněte pravým tlačítkem na službu **ToDoService** a zvolte **Přidat** > **Odkaz…**. Zobrazí se dialogové okno **Správce odkazů**.

V okně **Správce odkazů** zaškrtněte políčko **Model** a klikněte na **OK**.

### <a name="add-a-data-context"></a>Přidání kontextu dat

Teď vytvoříte kontext dat, který koordinuje poskytování dat z datového modelu.

Pokud chcete přidat třídu DataContext, v Průzkumníku řešení klikněte pravým tlačítkem na službu **ToDoService** a potom klikněte na **Přidat** > **Třída**.
Zobrazí se dialogové okno **Přidat novou položku**. Zkontrolujte, jestli je vybraná možnost **Třída**, v poli **Název** nastavte `DataContext` a klikněte na **Přidat**.

Ve třídě **DataContext.cs** nahraďte prázdný obsah `class DataContext` tímto obsahem:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        ToDoList = new Model.ToDoList("Main List");

        // Seed to-do list

        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Tento minimální kontext dat poskytne pár ukázkových položek úkolů a zajistí k nim přístup.

### <a name="add-a-controller"></a>Přidání kontroleru

Při vytváření projektu **ToDoService** poskytla šablona výchozí kontroler, který zpracovává požadavky HTTP a vytváří odpovědi HTTP. V okně **Průzkumník řešení** u projektu **ToDoService** otevřete složku **Kontrolery**, ve které uvidíte soubor **ValuesController.cs**. 

Klikněte pravým tlačítkem na **ValuesController.cs** a potom klikněte na **Přejmenovat**. Přejmenujte soubor na `ToDoController.cs`. Pokud se zobrazí výzva s dotazem, jestli se mají přejmenovat všechny odkazy, klikněte na **Ano**.

Otevřete soubor **ToDoController.cs** a nahraďte obsah `class ToDoController` tímto obsahem:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Tento kurz nepopisuje implementaci operací přidání, odstranění atd. a soustředí se výhradně na komunikaci s jinou službou.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Vytvoření webové stránky, která zobrazuje položky úkolů

S implementovanou back-endovou službou vytvořte kód webu pro zobrazení položek úkolů, které služba poskytuje. Následující kroky probíhají v rámci projektu **WebFrontEnd**.

Webová stránka, na které se zobrazují položky úkolů, potřebuje přístup ke třídě a seznamu **ToDoItem**.
V okně **Průzkumník řešení** kliknutím pravým tlačítkem na **WebFrontEnd** a volbou položek **Přidat** > **Odkaz…** přidejte odkaz na projekt Model. Zobrazí se dialogové okno **Správce odkazů**.

V okně **Správce odkazů** zaškrtněte políčko **Model** a klikněte na **OK**.

V okně **Průzkumník řešení** otevřete stránku Index.cshtml tím, že přejdete na **WebFrontEnd** > **Stránky** > **Index.cshtml**. Otevřete stránku **Index.cshtml**.

Nahraďte obsah celého souboru následujícím kódem HTML, který definuje jednoduchou tabulku pro zobrazení položek úkolů:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Otevřete kód indexové stránky v okně **Průzkumník řešení** tím, že otevřete stránku **Index.cshtml** a potom **Index.cshtml.cs**.
Do horní části **Index.cshtml.cs** přidejte `using System.Net.Http;`

Nahraďte obsah `public class IndexModel` tímto obsahem:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Vytvoření proměnných prostředí

Ke komunikaci s back-endovou službou je zapotřebí její adresa URL. Pro účely tohoto kurzu následující úryvek kódu (definovaný vše v rámci třídy IndexModel) čte proměnné prostředí, ze kterých se adresa URL skládá:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Adresa URL se skládá z názvu služby a portu. Všechny tyto informace se nachází v souboru service.yaml v projektu **ToDoService**.

V okně **Průzkumník řešení** přejděte na projekt **ToDoService** a přejděte na **Prostředky služby** > **service.yaml**.

![Obrázek 1 – soubor service.yaml projektu ToDoService](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* Název služby `ToDoService` se nachází v části `services:` za položkou `name:`, jak ukazuje položka (1) na předchozím obrázku.
* Port `20008` se nachází v části `endpoints:` za položkou `port:`, jak ukazuje položka (2) na předchozím obrázku. Číslo portu pro váš projekt se může lišit.

Dál je potřeba v projektu WebFrontEnd definovat proměnné prostředí, které reprezentují název služby a číslo portu, aby bylo možné volat back-endovou službu.

V okně **Průzkumník řešení** přejděte na **WebFrontEnd** > **Prostředky služby** > **service.yaml**, abyste mohli definovat proměnné, které určují adresu back-endové služby.

V souboru service.yaml přidejte do části `environmentVariables` následující proměnné. Určitou roli hrají i mezery, takže zarovnejte přidávané proměnné pod ostatní proměnné v části `environmentVariables:`

> [!IMPORTANT]
> K odsazení proměnných v souboru service.yaml je potřeba použít mezery, ne tabulátory, jinak nebude moct proběhnout kompilace. Visual Studio může při vytváření proměnných prostředí vložit tabulátory. Všechny tabulátory nahraďte mezerami. I když se ve výstupu ladění **build** zobrazí chyby, aplikace se spustí. Nebude ale fungovat, dokud nenahradíte tabulátory mezerami. Pokud chcete mít jistotu, že v souboru service.yaml nezůstaly žádné tabulátory, můžete v editoru sady Visual Studio zobrazit prázdné znaky pomocí voleb **Upravit**  > **Upřesnit**  > **Zobrazit prázdné znaky**.

Soubor **service.yaml** projektu **WebFrontEnd** by se měl podobat následujícímu příkladu, i když hodnota `ApiHostPort` bude pravděpodobně jiná:

![Soubor service.yaml projektu WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)

Teď jste připravení na sestavení a nasazení image aplikace Service Fabric Mesh a back-endové služby do místního clusteru.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit aplikaci Service Fabric Mesh, která se skládá z webového front-endu ASP.NET
> * Vytvořit model, který reprezentuje položky úkolů
> * Vytvořit back-endovou službu a načíst z ní data
> * Přidat kontroler a třídu DataContext v rámci architektury model-zobrazení-kontroler pro back-endovou službu
> * Vytvořit webovou stránku pro zobrazení položek úkolů
> * Vytvořit proměnné prostředí, které identifikují back-endovou službu

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Ladění aplikace Service Fabric Mesh spuštěné v místním prostředí](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
