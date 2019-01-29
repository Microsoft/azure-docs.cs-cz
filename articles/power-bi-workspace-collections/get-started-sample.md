---
title: Začínáme s pomocí ukázky
description: V tomto článku vám ukážeme k ukázce začnete get kolekce pracovních prostorů Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 630413d15df04d27599389f647c57876fff9d295
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094423"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Začínáme s ukázkou kolekce pracovních prostorů Power BI

S **kolekce pracovních prostorů Power BI Microsoft**, můžete integrovat sestavy Power BI přímo do vašich webových nebo mobilních aplikací. V tomto článku jsme vám představí **kolekce pracovních prostorů Power BI** ukázka Začínáme get.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Než půjdeme dál, budete chtít uložit na následujících odkazech: Mohou vám pomoci při příliš integrace sestav Power BI do ukázkové aplikace a vlastních aplikací.

* [Ukázkový pracovní prostor webové aplikace](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Referenční materiály k Power BI pracovního prostoru kolekce API](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK ](https://go.microsoft.com/fwlink/?LinkId=746472) (k dispozici prostřednictvím NuGet)
* [Ukázku vložení sestavy jazyka JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Předtím, než můžete nakonfigurovat a spustit kolekce pracovních prostorů Power BI získáte ukázka Začínáme, musíte vytvořit aspoň jeden **kolekce pracovních prostorů** ve vašem předplatném Azure. Další informace o vytvoření **kolekce pracovních prostorů** na webu Azure Portal, najdete v článku [Začínáme s kolekcemi pracovních prostorů Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurace ukázkové aplikace

Projděme si nastavení vývojového prostředí sady Visual Studio pro přístup k součástem potřebných ke spuštění ukázkové aplikace.

1. Stáhněte a rozbalte [kolekce pracovních prostorů Power BI – integrace sestavy do webové aplikace](https://go.microsoft.com/fwlink/?LinkId=761493) ukázka na Githubu.
2. Otevřít **PowerBI embedded.sln** v sadě Visual Studio. Možná budete muset provést **Update-Package** příkazu v konzole Správce balíčků NuGet, abyste mohli aktualizovat balíčky používané v tomto řešení.
3. Sestavte řešení.
4. Spustit **ProvisionSample** konzolovou aplikaci. V ukázkové aplikaci konzoly zřízení pracovního prostoru a naimportovat soubor PBIX.
5. Ke zřízení nového **pracovní prostor**, vyberte možnost 1, **Správa kolekce**a poté vyberte možnost 6, **zřiďte nový pracovní prostor**
6. Chcete-li importovat nový **sestavy**, vyberte možnost 2, **sestav správy**a poté vyberte možnost 3, **souboru Desktopu Import PBIX do pracovního prostoru**.

7. Zadejte vaše **kolekce pracovních prostorů** název, a **přístupový klíč**. Si můžete stáhnout tyto **webu Azure portal**. Další informace o tom, jak získat vaše **přístupový klíč**, naleznete v tématu [zobrazení Power BI rozhraní API přístupové klíče](get-started.md#view-power-bi-api-access-keys) v Začínáme s Microsoft Power BI Embedded.

    ![Přístupové klíče v rámci webu Azure portal](media/get-started-sample/azure-portal.png)
8. Zkopírujte a uložte nově vytvořený **ID pracovního prostoru** pro pozdější použití v tomto článku. Po **ID pracovního prostoru** je vytvořen, najdete ho **webu Azure portal**.

    ![ID pracovního prostoru v rámci webu Azure portal](media/get-started-sample/workspace-id.png)
9. Import souboru PBIX do vaší **pracovní prostor**, vyberte možnost **6. Import souboru PBIX Desktopu do existujícího pracovního prostoru**. Pokud nemáte k dispozici PBIX souboru po ruce, můžete stáhnout [ukázkový soubor PBIX prodejní analýzy](https://go.microsoft.com/fwlink/?LinkID=780547).
10. Pokud se zobrazí výzva, zadejte popisný název pro váš **datovou sadu**.

Měli byste vidět odpovědi podobně jako:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Pokud váš soubor PBIX obsahuje všechna připojení directquery, spusťte možnost 7 aktualizovat připojovací řetězce.

V tomto okamžiku máte sestavu Power BI PBIX importovat do vaší **pracovní prostor**. Teď se podívejme se na tom, jak spustit **kolekce pracovních prostorů Power BI** získat Začínáme ukázkovou webovou aplikaci.

## <a name="run-the-sample-web-app"></a>Spuštění ukázkové webové aplikace

Ukázkové webové aplikace je ukázková aplikace, který vykreslí sestav se importovat do vaší **pracovní prostor**. Tady je postup konfigurace ukázkové webové aplikace.

1. V **Power BI embedded** řešení sady Visual Studio, klikněte pravým tlačítkem na **EmbedSample** webovou aplikaci a zvolte **nastavit jako spouštěný projekt**.
2. V **web.config**v **EmbedSample** webovou aplikaci, upravit **appSettings**: **AccessKey**, **WorkspaceCollection** název, a **ID pracovního prostoru**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Spustit **EmbedSample** webovou aplikaci.

Po spuštění **EmbedSample** webovou aplikaci, by měl obsahovat levém navigačním panelu **sestavy** nabídky. Chcete-li zobrazit sestavu jste naimportovali, rozbalte **sestavy**a klikněte na sestavu. Pokud jste naimportovali [ukázkový soubor PBIX prodejní analýzy](https://go.microsoft.com/fwlink/?LinkID=780547), ukázková webová aplikace bude vypadat takto:

![Ukázka levé navigaci v rámci ukázkové aplikace](media/get-started-sample/sample-left-nav.png)

Po kliknutí na zprávu, **EmbedSample** webovou aplikaci by měl vypadat přibližně toto:

![Ukázkové sestavě pro zobrazení v rámci aplikace](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Prozkoumejte vzorový kód

**Kolekce pracovních prostorů Power BI Microsoft** ukázka je ukázkové webové aplikace, který ukazuje, jak integrovat **Power BI** sestavy do vaší aplikace. Návrhový vzor Model-View-Controller (MVC) používá k ukazují osvědčené postupy. Tato část ukazuje části ukázek kódu, který si můžete projít v rámci **Power BI embedded** webové aplikace řešení. Vzor Model-View-Controller (MVC) odděluje modelování domény, prezentace a akce na základě uživatelského zadání na tři samostatné třídy: Model, zobrazení a ovládací prvek. Další informace o MVC najdete v tématu [Další informace o ASP.NET](http://www.asp.net/mvc).

**Kolekce pracovních prostorů Power BI Microsoft** ukázkový kód je oddělená následujícím způsobem. Každý oddíl obsahuje název souboru v řešení Power BI embedded.sln, takže můžete snadno vyhledat kód v ukázce.

> [!NOTE]
> V této části je uveden seznam ukázkový kód, který ukazuje, jak byla zapsána kód. Chcete-li zobrazit úplnou ukázku, načtěte PowerBI embedded.sln řešení v sadě Visual Studio.

### <a name="model"></a>Model

Ukázka obsahuje **ReportsViewModel** a **ReportViewModel**.

**ReportsViewModel.cs**: Představuje sestavy Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Představuje sestavy Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec musí být v následujícím formátu:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Pomocí společných server a databáze atributy selhání. Příklad: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Zobrazení

**Zobrazení** spravuje zobrazení Power BI **sestavy** a Power BI **sestavy**.

**Reports.cshtml**: Iterovat přes **Model.Reports** k vytvoření **ActionLink**. **ActionLink** se skládá následujícím způsobem:

| Část | Popis |
| --- | --- |
| Název |Název sestavy. |
| Řetězec dotazu |Odkaz na ID sestavy. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Nastavte **Model.AccessToken**a výraz Lambda pro **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Kontrolér

**DashboardController.cs**: Vytvoří PowerBIClient předávání **tokenu aplikace**. JSON Web Token (token JWT) se generuje z **podpisový klíč** zobrazíte **přihlašovací údaje**. **Pověření** slouží k vytvoření instance **PowerBIClient**. Jakmile budete mít instanci **PowerBIClient**, můžete volat GetReports() a GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Úloha<ActionResult> sestavy (reportId řetězec)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integrace sestavy do vaší aplikace

Jakmile budete mít **sestavy**, použijete **IFrame** pro vložení Power BI **sestavy**. Tady je fragment kódu z powerbi.js v **kolekce pracovních prostorů Power BI Microsoft** vzorku.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtr sestavy vloží do vaší aplikace

Vloženou sestavu můžete filtrovat pomocí syntaxe adresy URL. K tomuto účelu můžete přidat **$filter** parametr řetězce pomocí dotazu **eq** operátor pro vaše zdrojové adresy url elementu iFrame se specifikovaným filtrem. Tady je syntaxe dotazů filtru:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> V řetězci {tableName/fieldName} nesmí být mezery ani speciální znaky. Na místo položky {FieldValue} je možné vložit jednu hodnotu představující kategorii.  

## <a name="see-also"></a>Další informace najdete v tématech

[Běžné scénáře kolekce pracovních prostorů Microsoft Power BI](scenarios.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Vložení sestavy](embed-report.md)  
[Vytvoření nové sestavy z datové sady](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
