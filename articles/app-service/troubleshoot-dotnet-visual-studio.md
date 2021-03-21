---
title: Řešení potíží se sadou Visual Studio
description: Naučte se řešit potíže s App Service aplikací pomocí nástrojů pro vzdálené ladění, trasování a protokolování, které jsou integrované do Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 65eb4e8fefb74219c927fe507baceca7c55462be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101095720"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Řešení potíží s aplikací v Azure App Service pomocí sady Visual Studio
## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak používat nástroje sady Visual Studio k ladění aplikace v [App Service](./overview.md), spuštění v [režimu ladění](/visualstudio/debugger/) vzdáleně nebo zobrazení protokolů aplikací a protokolů webového serveru.

Naučíte se:

* Které funkce správy aplikací jsou k dispozici v sadě Visual Studio.
* Jak používat vzdálené zobrazení sady Visual Studio k provádění rychlých změn ve vzdálené aplikaci.
* Jak spustit režim ladění vzdáleně, když projekt běží v Azure, a to jak pro aplikaci, tak pro webovou úlohu.
* Jak vytvořit protokoly trasování aplikace a zobrazit je v době, kdy je aplikace vytváří.
* Jak zobrazit protokoly webového serveru, včetně podrobných chybových zpráv a trasování chybných požadavků.
* Postup odesílání diagnostických protokolů na účet Azure Storage a jejich zobrazení.

Pokud máte Visual Studio Ultimate, můžete použít také [IntelliTrace](/visualstudio/debugger/intellitrace) pro ladění. IntelliTrace se v tomto kurzu nezabývá.

## <a name="prerequisites"></a><a name="prerequisites"></a>Předpoklady
Tento kurz spolupracuje s vývojovým prostředím, webovým projektem a App Service aplikaci, kterou jste nastavili v části [Vytvoření aplikace v ASP.NET v Azure App Service](quickstart-dotnet-framework.md). V sekcích WebJobs budete potřebovat aplikaci, kterou vytvoříte v části [Začínáme s Azure WEBJOBS SDK][GetStartedWJ].

Ukázky kódu zobrazené v tomto kurzu jsou pro webovou aplikaci v jazyce C# MVC, ale postupy řešení potíží jsou stejné pro aplikace Visual Basic a webových formulářů.

V tomto kurzu se předpokládá, že používáte Visual Studio 2019. 

Funkce protokolů streamování funguje jenom pro aplikace, které cílí na .NET Framework 4 nebo novější.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Konfigurace a Správa aplikací
Sada Visual Studio poskytuje přístup k podmnožině funkcí správy aplikací a nastavení konfigurace, která jsou k dispozici v [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715). V této části uvidíte, co je k dispozici, pomocí **Průzkumník serveru**. Pokud chcete zobrazit nejnovější funkce Integrace Azure, vyzkoušejte také **Průzkumníka cloudu** . V nabídce **zobrazení** můžete otevřít obě okna.

1. Pokud jste se ještě přihlásili k Azure v aplikaci Visual Studio, klikněte pravým tlačítkem na **Azure** a vyberte připojit k **Microsoft Azure předplatnému** v **Průzkumník serveru**.

    Alternativou je instalace certifikátu pro správu, který umožňuje přístup k vašemu účtu. Pokud se rozhodnete nainstalovat certifikát, klikněte pravým tlačítkem myši na uzel **Azure** v **Průzkumník serveru** a potom v místní nabídce vyberte **Spravovat a filtrovat předplatná** . V dialogovém okně **spravovat předplatná Microsoft Azure** klikněte na kartu **certifikáty** a pak klikněte na **importovat**. Podle pokynů stáhněte a pak importujte soubor předplatného (označovaný také jako soubor *. publishsettings* ) pro váš účet Azure.

   > [!NOTE]
   > Pokud stahujete soubor předplatného, uložte ho do složky mimo adresáře zdrojového kódu (například ve složce stažené soubory) a po dokončení importu ho odstraňte. Uživatel se zlými úmysly, který získá přístup k souboru předplatného, může upravit, vytvořit a odstranit vaše služby Azure.
   >
   >

    Další informace o připojení k prostředkům Azure ze sady Visual Studio najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. V **Průzkumník serveru** rozbalte položku **Azure** a rozbalte položku **App Service**.
3. Rozbalte skupinu prostředků, která obsahuje aplikaci, kterou jste vytvořili v části [vytvoření Azure App Service aplikace v ASP.NET](quickstart-dotnet-framework.md), a pak klikněte pravým tlačítkem myši na uzel aplikace a pak klikněte na **Zobrazit nastavení**.

    ![Zobrazit nastavení v Průzkumník serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Zobrazí se karta **Webová aplikace Azure** a uvidíte úlohy správy aplikace a konfigurace, které jsou k dispozici v sadě Visual Studio.

    ![Okno webové aplikace Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    V tomto kurzu použijete rozevírací seznam protokolování a trasování. Použijete také vzdálené ladění, ale k jeho povolení použijete jinou metodu.

    Informace o polích nastavení aplikace a připojovací řetězce v tomto okně naleznete v tématu [Azure App Service: jak fungují řetězce aplikace a připojovací řetězce](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Chcete-li provést úlohu správy aplikací, kterou nelze provést v tomto okně, klikněte na tlačítko **otevřít v portál pro správu** . otevře se okno prohlížeče pro Azure Portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Přístup k souborům aplikace v Průzkumník serveru
Obvykle nasadíte webový projekt s `customErrors` příznakem v Web.config souboru nastaveným na `On` nebo `RemoteOnly` , což znamená, že se vám nezobrazuje chybová zpráva, pokud došlo k nějakému problému. V případě mnoha chyb získáte stránku, například jednu z následujících možností:

**Chyba serveru v/aplikaci:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Snímek obrazovky s chybou serveru ve webovém prohlížeči – Chyba aplikace":::

**Došlo k chybě:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Snímek obrazovky znázorňující příklad obecné chyby, ke které došlo ve webovém prohlížeči.":::

**Web nemůže zobrazit stránku**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Snímek obrazovky s webem nemůže zobrazit chybu stránky ve webovém prohlížeči.":::

Často nejjednodušší způsob, jak najít příčinu chyby, je povolit podrobné chybové zprávy, které popisují první z předchozích snímků obrazovky. To vyžaduje změnu v nasazeném souboru Web.config. Můžete upravit soubor *Web.config* v projektu a znovu nasadit projekt, nebo vytvořit [Web.config transformovat](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) a nasadit sestavení pro ladění, ale existuje rychlejší způsob: v **Průzkumník řešení** můžete přímo zobrazit a upravit soubory ve vzdálené aplikaci pomocí funkce *vzdáleného zobrazení* .

1. V **Průzkumník serveru** rozbalte položku **Azure**, rozbalte položku **App Service**, rozbalte skupinu prostředků, ve které je vaše aplikace umístěná, a potom rozbalte uzel pro vaši aplikaci.

    Zobrazí se uzly, které vám umožní přístup k souborům obsahu a souborům protokolu aplikace.
2. Rozbalte uzel **soubory** a dvakrát klikněte na soubor *Web.config* .

    ![Otevřít Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio otevře soubor Web.config ze vzdálené aplikace a v záhlaví zobrazí [Vzdálený] vedle názvu souboru.
3. Přidejte následující řádek do `system.web` prvku:

    `<customErrors mode="Off"></customErrors>`

    ![Upravit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aktualizujte prohlížeč, na kterém je zobrazená chybová zpráva, a teď zobrazíte podrobnou chybovou zprávu, například následující příklad:

    ![Podrobná chybová zpráva](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Zobrazená chyba byla vytvořena přidáním řádku zobrazené červeně do *Views\Home\Index.cshtml*.)

Úprava souboru Web.config je jenom jedním z příkladů scénářů, ve kterých je možnost číst a upravovat soubory ve vaší aplikaci App Service usnadnit odstraňování potíží.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Aplikace pro vzdálené ladění
Pokud podrobná chybová zpráva neposkytne dostatek informací a chyba se nedá znovu vytvořit v místním prostředí, můžete jiný způsob řešení potíží spustit v režimu ladění vzdáleně. Můžete nastavit zarážky, manipulovat přímo s pamětí, krokovat kód a dokonce i změnit cestu kódu.

Vzdálené ladění nefunguje v edicích Express sady Visual Studio.

V této části se dozvíte, jak vzdáleně ladit pomocí projektu, který jste vytvořili v tématu [Vytvoření aplikace ASP.NET v Azure App Service](quickstart-dotnet-framework.md).

1. Otevřete webový projekt, který jste vytvořili v části [Vytvoření aplikace ASP.NET v Azure App Service](quickstart-dotnet-framework.md).

1. Otevřete *souboru controllers\homecontroller.cs*.

1. Odstraňte `About()` metodu a vložte následující kód na místo.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Nastavte zarážku](/visualstudio/debugger/) na `ViewBag.Message` řádku.

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a klikněte na **publikovat**.

1. V rozevíracím seznamu **profil** vyberte stejný profil, který jste použili v části [vytvoření aplikace v ASP.NET v Azure App Service](quickstart-dotnet-framework.md). Pak klikněte na nastavení.

1. V dialogovém okně **publikovat** klikněte na kartu **Nastavení** a pak změňte **konfiguraci** na **ladit** a pak klikněte na **Uložit**.

    ![Publikovat v režimu ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klikněte na **Publikovat**. Až se nasazení dokončí a otevře se Váš prohlížeč na adrese URL Azure vaší aplikace, zavřete prohlížeč.

1. V **Průzkumník serveru** klikněte pravým tlačítkem na aplikaci a pak klikněte na **připojit ladicí program**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Snímek obrazovky okna Průzkumník serveru, ve kterém se zobrazuje vybraná aplikace a pak klikněte na tlačítko připojit ladicí program.":::

    Prohlížeč se automaticky otevře na domovské stránce běžící v Azure. Možná budete muset počkat 20 sekund, nebo tak, aby Azure nastavil Server pro ladění. Toto zpoždění nastane jenom při prvním spuštění v režimu ladění aplikace v období 48 hodin. Při opětovném spuštění ladění ve stejné době nedochází ke zpoždění.

    > [!NOTE] 
    > Pokud máte potíže s spuštěním ladicího programu, zkuste to provést pomocí **Průzkumníka cloudu** místo **Průzkumník serveru**.
    >

1. V nabídce klikněte na **o** .

    Visual Studio zastaví na zarážce a kód je spuštěný v Azure, ne na vašem místním počítači.

1. Pokud `currentTime` chcete zobrazit hodnotu času, najeďte myší na proměnnou.

    ![Zobrazit proměnnou v režimu ladění spuštěném v Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Čas, který vidíte, je čas serveru Azure, který může být v jiném časovém pásmu než váš místní počítač.

1. Zadejte novou hodnotu pro `currentTime` proměnnou, například "teď běží v Azure".

1. Pokračujte v běhu stisknutím klávesy F5.

     Stránka o aplikaci běžící v Azure zobrazuje novou hodnotu, kterou jste zadali do proměnné currentTime.

     ![O stránce s novou hodnotou](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> Vzdálené ladění WebJobs
V této části se dozvíte, jak vzdáleně ladit pomocí projektu a aplikace, kterou vytvoříte v tématu [Začínáme s Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkce uvedené v této části jsou k dispozici pouze v Visual Studio 2013 s aktualizací Update 4 nebo novější.

Vzdálené ladění funguje jenom se souvislými WebJobs. Naplánované a na vyžádání WebJobs nepodporují ladění.

1. Otevřete webový projekt, který jste vytvořili v [části Začínáme s Azure WEBJOBS SDK][GetStartedWJ].

2. V projektu ContosoAdsWebJob otevřete *Functions. cs*.

3. [Nastavte zarážku](/visualstudio/debugger/) na prvním příkazu v `GnerateThumbnail` metodě.

    ![Nastavit zarážku](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. V **Průzkumník řešení** klikněte pravým tlačítkem myši na webový projekt (ne na projekt webové úlohy WebJob) a klikněte na **publikovat**.

5. V rozevíracím seznamu **profil** vyberte stejný profil, který jste použili v [části Začínáme s Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klikněte na kartu **Nastavení** , změňte **konfiguraci** na **ladit** a pak klikněte na **publikovat**.

    Visual Studio nasadí webové a webové projekty a otevře se Váš prohlížeč na adrese URL Azure vaší aplikace.

7. V **Průzkumník serveru** rozbalte položku **Azure > App Service > skupinu prostředků > vaše aplikace > webové úlohy > průběžně** a klikněte pravým tlačítkem na **ContosoAdsWebJob**.

8. Klikněte na **připojit ladicí program**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Snímek obrazovky Průzkumník serveru s vybraným ContosoAdsWebJob v rozevírací nabídce a připojením vybraného ladicího programu":::

    Prohlížeč se automaticky otevře na domovské stránce běžící v Azure. Možná budete muset počkat 20 sekund, nebo tak, aby Azure nastavil Server pro ladění. Toto zpoždění nastane jenom při prvním spuštění v režimu ladění aplikace v období 48 hodin. Při opětovném spuštění ladění ve stejné době nedochází ke zpoždění.

9. Ve webovém prohlížeči, který je otevřen na domovské stránce společnosti Contoso ADS, vytvořte novou reklamu.

    Vytvořením služby AD dojde k vytvoření zprávy fronty, kterou webová úloha vybrala a zpracuje. Když Sada WebJobs SDK volá funkci pro zpracování zprávy fronty, kód narazí na zarážku.

10. Když je ladicí program v zarážce zarážka, můžete kontrolovat a měnit hodnoty proměnných, když je program spuštěný v cloudu. Na následujícím obrázku ladicí program zobrazuje obsah objektu blobInfo, který byl předán `GenerateThumbnail` metodě.

     ![objekt blobInfo v ladicím programu](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Pokračujte v běhu stisknutím klávesy F5.

     `GenerateThumbnail`Metoda dokončí vytvoření miniatury.

12. V prohlížeči aktualizujte stránku index a uvidíte miniaturu.

13. V aplikaci Visual Studio stiskněte SHIFT + F5 a zastavte ladění.

14. V **Průzkumník serveru** klikněte pravým tlačítkem na uzel ContosoAdsWebJob a klikněte na **zobrazit řídicí panel**.

15. Přihlaste se pomocí přihlašovacích údajů Azure a potom kliknutím na název webové úlohy přejděte na stránku vaší webové úlohy.

     ![Klikněte na ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Na řídicím panelu se zobrazí, že se `GenerateThumbnail` funkce spustila v poslední době.

     (Při příštím kliknutí na **zobrazit řídicí panel** se nemusíte přihlašovat a prohlížeč přejde přímo na stránku vaší webové úlohy.)

16. Kliknutím na název funkce zobrazíte podrobnosti o spuštění funkce.

     ![Podrobnosti funkce](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Pokud funkce [zapsala protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki), můžete je zobrazit kliknutím na **ToggleOutput** .

## <a name="notes-about-remote-debugging"></a>Poznámky ke vzdálenému ladění

* Spuštění v režimu ladění v produkčním prostředí se nedoporučuje. Pokud se vaše produkční aplikace neškáluje na více instancí serveru, ladění brání webovému serveru v reakci na jiné požadavky. Pokud máte více instancí webového serveru, získáte při připojení k ladicímu programu náhodnou instanci a neexistuje žádný způsob, jak zajistit, aby následné požadavky prohlížeče přešly na stejnou instanci. Také obvykle nebudete nasazovat sestavení ladění do produkčního prostředí a optimalizace kompilátoru pro sestavení vydaných verzí mohou ukázat, co se děje řádekem v kódu ve zdrojovém kódu. Pro řešení potíží s produkčním prostředím je nejlepším prostředkem trasování aplikací a protokoly webového serveru.
* Vyhněte se dlouhému zastavení při zarážce při vzdáleném ladění. Azure považuje proces, který se zastaví po delší dobu než několik minut, a ukončí ho.
* Při ladění Server odesílá data do sady Visual Studio, což by mohlo mít vliv na poplatky za šířku pásma. Informace o sazbách šířky pásma najdete v tématu [ceny Azure](https://azure.microsoft.com/pricing/calculator/).
* Ujistěte se, že `debug` atribut `compilation` prvku v souboru *Web.config* je nastaven na hodnotu true. Je nastavena na hodnotu true ve výchozím nastavení při publikování konfigurace sestavení ladění.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Pokud zjistíte, že ladicí program nekrokuje do kódu, který chcete ladit, bude pravděpodobně nutné změnit nastavení Pouze můj kód.  Další informace naleznete v tématu [určení, zda se má ladit pouze uživatelský kód pomocí pouze můj kód v aplikaci Visual Studio](/visualstudio/debugger/just-my-code).
* Časovač se spustí na serveru, když povolíte funkci vzdáleného ladění a po 48 hodinách se funkce automaticky vypne. Tento limit 48 hodin se provádí z důvodů zabezpečení a výkonu. Tuto funkci můžete snadno znovu zapnout, kolikrát chcete. Doporučujeme ponechat ho zakázaný, když nebudete aktivně ladit.
* Ladicí program můžete připojit ručně k libovolnému procesu, nejen k procesu aplikace (w3wp.exe). Další informace o použití režimu ladění v aplikaci Visual Studio naleznete v tématu [ladění v aplikaci Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Přehled diagnostických protokolů
Aplikace ASP.NET, která běží v aplikaci App Service, může vytvořit následující typy protokolů:

* **Protokoly trasování aplikace**<br/>
  Aplikace vytváří tyto protokoly voláním metod třídy [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) .
* **Protokoly webového serveru**<br/>
  Webový server vytvoří záznam protokolu pro každý požadavek HTTP do aplikace.
* **Podrobné protokoly chybových zpráv**<br/>
  Webový server vytvoří stránku HTML s některými dalšími informacemi pro neúspěšné požadavky HTTP (požadavky, jejichž výsledkem je stav kód 400 nebo vyšší).
* **Protokoly pro trasování chybných požadavků**<br/>
  Webový server vytvoří soubor XML s podrobnými informacemi o trasování pro neúspěšné požadavky HTTP. Webový server také poskytuje soubor XSL pro formátování XML v prohlížeči.

Protokolování má vliv na výkon aplikace, takže Azure vám nabídne možnost povolit nebo zakázat každý typ protokolu podle potřeby. Pro protokoly aplikací můžete určit, že se mají zapisovat jenom protokoly nad určitou úrovní závažnosti. Když vytváříte novou aplikaci, je ve výchozím nastavení zakázané všechna protokolování.

Protokoly se zapisují do souborů ve složce *Logfiles* v systému souborů vaší aplikace a jsou dostupné přes FTP. Protokoly webového serveru a protokoly aplikací je také možné zapsat na účet Azure Storage. V účtu úložiště můžete uchovávat větší objem protokolů, než je možné v systému souborů. Pokud používáte systém souborů, omezíte maximálně 100 megabajtů protokolů. (Protokoly systému souborů jsou pouze pro krátkodobé uchovávání. Azure po dosažení tohoto limitu odstraní staré soubory protokolů, aby se uvolnily na nové.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Vytvoření a zobrazení protokolů trasování aplikace
V této části provedete následující úlohy:

* Přidejte příkazy trasování do webového projektu, který jste vytvořili v části Začínáme [s Azure a ASP.NET](quickstart-dotnet-framework.md).
* Zobrazit protokoly při místním spuštění projektu.
* Zobrazit protokoly, které jsou generovány aplikací běžícími v Azure.

Informace o tom, jak vytvořit protokoly aplikací ve službě WebJobs, najdete v tématu [jak pracovat s úložištěm Azure Queue pomocí sady WebJobs SDK – jak zapisovat protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki). Následující pokyny pro zobrazení protokolů a řízení způsobu jejich uložení v Azure platí také pro protokoly aplikací vytvořené pomocí WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Přidání příkazů trasování do aplikace
1. Otevřete *souboru controllers\homecontroller.cs* a nahraďte `Index` metody, a `About` `Contact` s následujícím kódem, aby bylo možné přidat `Trace` příkazy a `using` příkaz pro `System.Diagnostics` :

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Přidejte `using System.Diagnostics;` příkaz na začátek souboru.

### <a name="view-the-tracing-output-locally"></a>Zobrazit výstup trasování místně
1. Stisknutím klávesy F5 spusťte aplikaci v režimu ladění.

    Výchozí naslouchací proces trasování zapisuje všechny výstupy trasování do okna **výstup** spolu s dalšími výstupy ladění. Následující obrázek ukazuje výstup z příkazů trasování, které jste přidali do `Index` metody.

    ![Trasování v okně ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Následující postup ukazuje, jak zobrazit výstup trasování na webové stránce bez kompilace v režimu ladění.
1. Otevřete soubor aplikace Web.config (ten umístěný ve složce projektu) a přidejte `<system.diagnostics>` element na konec souboru těsně před uzavírací `</configuration>` element:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

`WebPageTraceListener`Umožňuje zobrazit výstup trasování procházením na `/trace.axd` .
1. Do souboru Web.config přidejte <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">prvek trasování</a> `<system.web>` , například následující příklad:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Stiskněte klávesy CTRL+F5 a spusťte aplikaci.
1. Na panelu Adresa v okně prohlížeče přidejte do adresy URL *Trace. axd* a potom stiskněte klávesu ENTER (adresa URL je podobná `http://localhost:53370/trace.axd` ).
1. Na stránce **trasování aplikací** klikněte na tlačítko **Zobrazit podrobnosti** na prvním řádku (ne na řádku BrowserLink).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Snímek stránky trasování aplikace ve webovém prohlížeči zobrazující podrobnosti o zobrazení vybraných na prvním řádku.":::

    Zobrazí se stránka **Podrobnosti žádosti** a v části **trasovací informace** se zobrazí výstup z příkazů trasování, které jste přidali do `Index` metody.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Snímek obrazovky stránky s podrobnostmi o žádosti ve webovém prohlížeči, kde se zobrazí zpráva zvýrazněná v části informace o trasování":::

    Ve výchozím nastavení `trace.axd` je k dispozici pouze místně. Pokud jste chtěli, aby byl k dispozici ze vzdálené aplikace, mohli byste přidat `localOnly="false"` do `trace` prvku v souboru *Web.config* , jak je znázorněno v následujícím příkladu:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    Povolení `trace.axd` v produkční aplikaci se ale z bezpečnostních důvodů nedoporučuje. V následujících částech uvidíte snazší způsob, jak číst protokoly trasování v aplikaci App Service.

### <a name="view-the-tracing-output-in-azure"></a>Zobrazit výstup trasování v Azure
1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na webový projekt a klikněte na **publikovat**.
2. V dialogovém okně **Publikovat web** klikněte na **publikovat**.

    Po publikování aktualizace v aplikaci Visual Studio se otevře okno prohlížeče na domovské stránce (za předpokladu, že se na kartě **připojení** nevymaže **cílová adresa URL** ).
3. V **Průzkumník serveru** klikněte pravým tlačítkem myši na svou aplikaci a vyberte **Zobrazit protokoly streamování**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Snímek obrazovky Průzkumník serveru po kliknutí pravým tlačítkem myši na aplikaci a zobrazení protokolů streamování, které jsou vybrány v novém okně.":::

    V okně **výstup** se zobrazí zpráva, že jste připojeni ke službě streamování protokolů a do každé minuty přidáte řádek oznámení bez protokolu, který se má zobrazit.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Snímek obrazovky okna výstup zobrazující příklad připojení ke službě streamování protokolů s řádky oznámení":::

4. V okně prohlížeče, které zobrazuje vaši domovskou stránku aplikace klikněte na tlačítko **kontakt**.

    Během několika sekund se zobrazí výstup trasování na úrovni chyby, které jste přidali do metody, `Contact` v okně **výstup** .

    ![Trasování chyb v okně výstup](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio zobrazuje pouze trasování na úrovni chyb, protože toto je výchozí nastavení, když povolíte službu sledování protokolů. Když vytvoříte novou aplikaci App Service, všechna protokolování budou ve výchozím nastavení zakázaná, protože jste viděli, když jste otevřeli stránku nastavení dříve:

    ![Odhlášení aplikace](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Pokud však vyberete možnost **Zobrazit protokoly streamování**, aplikace Visual Studio automaticky změnila **protokolování aplikace (systém souborů)** na **Chyba**, což znamená, že protokoly na úrovni chyby jsou hlášeny. Aby se zobrazily všechny protokoly trasování, můžete toto nastavení změnit na **verbose**. Když vyberete nižší úroveň závažnosti než chyba, nahlásí se taky všechny protokoly pro vyšší úrovně závažnosti. Takže když vyberete podrobné informace, zobrazí se také protokoly informací, upozornění a chyb.  

5. V **Průzkumník serveru** klikněte pravým tlačítkem myši na aplikaci a potom klikněte na tlačítko **Zobrazit nastavení** jako dříve.
6. Změňte **protokolování aplikace (systém souborů)** na **podrobné** a pak klikněte na **Uložit**.

    ![Nastavení úrovně trasování na Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. V okně prohlížeče, ve kterém se teď zobrazuje vaše **kontaktní** stránka, klikněte na **Domů**, klikněte na **o** a pak klikněte na **kontakt**.

    Během několika sekund se v okně **výstup** zobrazí celý výstup trasování.

    ![Podrobný výstup trasování](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    V této části jste povolili a zakázali protokolování pomocí nastavení aplikace. Můžete také povolit a zakázat naslouchací procesy trasování úpravou souboru Web.config. Úprava souboru Web.config ale způsobí, že se doména aplikace recykluje, zatímco při povolování protokolování přes konfiguraci aplikace to neudělá. Pokud dojde k opakovanému reprodukování nebo občasnýmu zpracování problému, recyklování domény aplikace může "opravit" a vynutit vám, abyste počkali, až bude znovu provedeno. Povolením diagnostiky v Azure umožníte okamžité zachycení informací o chybách bez recyklace domény aplikace.

### <a name="output-window-features"></a>Funkce okna výstup
Karta **protokoly Microsoft Azure** v okně **výstup** obsahuje několik tlačítek a textové pole:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Snímek obrazovky se zobrazenými tlačítky a textovým polem karty protokoly Microsoft Azure v okně výstup.":::

Provádí následující funkce:

* Vymažte okno **výstup** .
* Povolí nebo zakáže zalamování řádků.
* Spustit nebo zastavit protokoly monitorování.
* Zadejte protokoly, které chcete monitorovat.
* Stáhněte si protokoly.
* Filtrovat protokoly na základě hledaného řetězce nebo regulárního výrazu.
* Zavřete okno **výstup** .

Pokud zadáte hledaný řetězec nebo regulární výraz, aplikace Visual Studio filtruje informace o protokolování v klientovi. To znamená, že můžete zadat kritéria po zobrazení protokolů v okně **výstup** a kritéria filtrování můžete změnit, aniž byste museli znovu vygenerovat protokoly.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Zobrazit protokoly webového serveru
Protokoly webového serveru zaznamenávají všechny aktivity HTTP pro aplikaci. Chcete-li je zobrazit v okně **výstup** , je nutné je povolit pro aplikaci a sdělit aplikaci Visual Studio, kterou chcete monitorovat.

1. Na kartě **Konfigurace webové aplikace Azure** , kterou jste otevřeli z **Průzkumník serveru**, změňte protokolování webového serveru na **zapnuto** a pak klikněte na **Uložit**.

    ![Povolit protokolování webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. V okně **výstup** klikněte na tlačítko **Určete, které protokoly Microsoft Azure se mají monitorovat** .

    ![Zadejte, které protokoly Azure se mají monitorovat.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. V dialogovém okně **Možnosti protokolování Microsoft Azure** vyberte **protokoly webového serveru** a pak klikněte na **OK**.

    ![Monitorování protokolů webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. V okně prohlížeče, ve kterém se aplikace zobrazuje, klikněte na **Domů**, klikněte na **o** a pak klikněte na **kontakt**.

    Obecně se zobrazí protokoly aplikací, za kterými následuje protokol webového serveru. Je možné, že budete muset chvíli počkat, než se protokoly objeví.

    ![Protokoly webového serveru v okně výstup](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Když ve výchozím nastavení poprvé povolíte protokoly webového serveru pomocí sady Visual Studio, Azure zapisuje protokoly do systému souborů. Jako alternativu můžete použít Azure Portal k určení, jestli se protokoly webového serveru mají zapsat do kontejneru objektů BLOB v účtu úložiště.

Pokud použijete portál k povolení protokolování webového serveru do účtu služby Azure Storage a potom zakážete protokolování v aplikaci Visual Studio, když znovu povolíte protokolování v aplikaci Visual Studio, obnoví se nastavení vašeho účtu úložiště.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Zobrazit podrobné protokoly chybových zpráv
Podrobné protokoly chyb obsahují další informace o požadavcích HTTP, které mají za následek kódy chybových odpovědí (400 nebo vyšší). Aby je bylo možné zobrazit v okně **výstup** , je nutné je povolit pro aplikaci a sdělit aplikaci Visual Studio, kterou chcete monitorovat.

1. Na kartě **Konfigurace webové aplikace Azure** , kterou jste otevřeli z **Průzkumník serveru**, změňte **podrobné chybové zprávy** na **zapnuto** a pak klikněte na **Uložit**.

    ![Povolit podrobné chybové zprávy](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. V okně **výstup** klikněte na tlačítko **Určete, které protokoly Microsoft Azure se mají monitorovat** .

3. V dialogovém okně **Možnosti protokolování Microsoft Azure** klikněte na **všechny protokoly** a pak klikněte na **OK**.

    ![Monitorovat všechny protokoly](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na panelu Adresa v okně prohlížeče přidejte k adrese URL znak navíc, který způsobí chybu 404 (například `http://localhost:53370/Home/Contactx` ), a stiskněte klávesu ENTER.

    Po několika sekundách se zobrazí podrobný protokol chyb v okně **výstup** sady Visual Studio.

    ![Podrobný protokol chyb – okno výstup](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ovládací prvek a kliknutím na odkaz zobrazíte výstup protokolu formátovaný v prohlížeči:

    ![Podrobný protokol chyb – okno prohlížeče](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Stáhnout protokoly systému souborů
Všechny protokoly, které lze monitorovat v okně **výstup** , lze také stáhnout jako soubor *. zip* .

1. V okně **výstup** klikněte na **Stáhnout protokoly streamování**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Snímek obrazovky okna výstup zobrazující, že se zvýrazní tlačítko Stáhnout protokoly streamování":::

    Průzkumník souborů se otevře ve složce *stažené soubory* se zvoleným staženým souborem.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Snímek obrazovky složky Ke_stažení v Průzkumníkovi souborů se zvoleným staženým souborem":::

2. Rozbalte soubor *. zip* a uvidíte následující strukturu složek:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Snímek obrazovky struktury složky souboru. zip po extrakci souboru":::

   * Protokoly trasování aplikace jsou v souborech *. txt* ve složce *LogFiles\Application* .
   * Protokoly webového serveru jsou v souborech *. log* ve složce *LogFiles\http\RawLogs* . Pomocí nástroje, jako je [analyzátor protokolů](https://www.iis.net/downloads/community/2010/04/log-parser-22) , můžete tyto soubory zobrazit a manipulovat s nimi.
   * Podrobné protokoly chybových zpráv jsou v souborech *. html* ve složce *LogFiles\DetailedErrors* .

     (Složka *nasazení* je určena pro soubory vytvořené publikováním správy zdrojového kódu. nemá žádnou souvislost s publikováním sady Visual Studio. Složka *Git* je určena pro trasování týkající se publikování správy zdrojového kódu a služby streamování souborů protokolu.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Zobrazit protokoly trasování neúspěšných žádostí
Protokoly pro trasování chybných požadavků jsou užitečné, pokud potřebujete pochopit podrobnosti o tom, jak služba IIS zpracovává požadavek HTTP, ve scénářích, jako je například přepisování adresy URL nebo problémy s ověřováním.

Aplikace App Service používají stejné funkce pro trasování chybných požadavků, které jsou dostupné se službou IIS 7,0 a novější. Nemáte přístup k nastavení služby IIS, která konfigurují, které chyby se ale protokolují. Pokud povolíte trasování chybných požadavků, budou zachyceny všechny chyby.

Trasování chybných požadavků můžete povolit pomocí sady Visual Studio, ale nemůžete je zobrazit v aplikaci Visual Studio. Tyto protokoly jsou soubory XML. Služba protokol streamování monitoruje pouze soubory, které jsou považovány za čitelné v režimu prostého textu:  *. txt*, *. html* a *. log* .

Protokoly pro trasování chybných požadavků můžete zobrazit v prohlížeči přímo prostřednictvím FTP nebo místně po použití nástroje FTP pro jejich stažení do místního počítače. V této části je budete zobrazovat přímo v prohlížeči.

1. Na kartě **Konfigurace** okna **webové aplikace Azure** , které jste otevřeli z **Průzkumník serveru**, změňte **trasování chybných požadavků** na **zapnuto** a pak klikněte na **Uložit**.

    ![Povolit trasování neúspěšných žádostí](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Do adresního řádku okna prohlížeče, které zobrazuje aplikaci, přidejte do adresy URL znak navíc a kliknutím na tlačítko ENTER způsobíte chybu 404.

    To způsobí, že se vytvoří protokol pro trasování chybných požadavků a následující kroky ukazují, jak tento protokol zobrazit nebo stáhnout.

3. V aplikaci Visual Studio klikněte na kartě **Konfigurace** v okně **webové aplikace Azure** na **otevřít v portál pro správu**.

4. Na stránce [](https://portal.azure.com) **Nastavení** Azure Portal vaší aplikace klikněte na **přihlašovací údaje pro nasazení** a pak zadejte nové uživatelské jméno a heslo.

    ![Nové uživatelské jméno a heslo pro FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Když se přihlásíte, musíte použít úplné uživatelské jméno s názvem aplikace, který je pro něj předem stanoven. Pokud například zadáte "MyID" jako uživatelské jméno a web bude "myExample", přihlásíte se jako "myexample\myid".
    >

5. V novém okně prohlížeče přejít na adresu URL, která se zobrazuje v části **FTP hostname** nebo **FTPS hostname** na stránce **Přehled** pro vaši aplikaci.

6. Přihlaste se pomocí přihlašovacích údajů FTP, které jste vytvořili dříve (včetně předpony názvu aplikace pro uživatelské jméno).

    V prohlížeči se zobrazí kořenová složka aplikace.

7. Otevřete složku *soubory protokolů* .

    ![Otevřít složku soubory protokolů](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otevřete složku s názvem W3SVC plus číselná hodnota.

    ![Otevření složky W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Složka obsahuje soubory XML pro všechny chyby, které byly zaprotokolovány po povolení trasování chybných požadavků, a soubor XSL, který může prohlížeč použít k formátování XML.

    ![Složka W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klikněte na soubor XML pro neúspěšný požadavek, pro který chcete zobrazit informace o trasování pro.

    Následující ilustrace ukazuje část informací o trasování pro ukázkovou chybu.

    ![Trasování chybných požadavků v prohlížeči](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Další kroky
Viděli jste, jak Visual Studio usnadňuje zobrazení protokolů vytvořených App Service aplikací. Následující části obsahují odkazy na další zdroje informací o souvisejících tématech:

* Řešení potíží s App Service
* Ladění v sadě Visual Studio
* Vzdálené ladění v Azure
* Trasování v aplikacích ASP.NET
* Analýza protokolů webového serveru
* Analýza protokolů pro trasování neúspěšných požadavků
* Cloud Services ladění

### <a name="app-service-troubleshooting"></a>Řešení potíží s App Service
Další informace o řešení potíží s aplikacemi v Azure App Service najdete v následujících zdrojích informací:

* [Jak monitorovat aplikace](web-sites-monitor.md)
* [Zkoumání nevracení paměti v Azure App Service pomocí Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Příspěvek na blogu k Microsoft ALM o funkcích sady Visual Studio pro analýzu problémů se správou paměti
* [Azure App Service online nástroje, které byste měli znát](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Příspěvek na blogu od Amit Apple.

Chcete-li získat nápovědu k určité otázce týkající se řešení potíží, spusťte vlákno v jednom z následujících fór:

* [Fórum Azure na webu ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Fórum Azure v Microsoft Q&A](/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Ladění v sadě Visual Studio
Další informace o použití režimu ladění v aplikaci Visual Studio naleznete v tématu [ladění v aplikaci Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) a [tipy pro ladění pomocí sady Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Vzdálené ladění v Azure
Další informace o vzdáleném ladění pro aplikace App Service a WebJobs najdete v následujících zdrojích informací:

* [Seznámení se vzdáleným laděním Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Úvod ke vzdálenému ladění Azure App Service část 2 – uvnitř vzdáleného ladění](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Úvod do vzdáleného ladění v Azure App Service část 3 – prostředí s více instancemi a GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Ladění webových úloh (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Pokud vaše aplikace používá webové rozhraní API Azure nebo Mobile Services back-endu a potřebujete je ladit, přečtěte si téma [ladění .NET back-endu v aplikaci Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio).

### <a name="tracing-in-aspnet-applications"></a>Trasování v aplikacích ASP.NET
K dispozici nejsou žádná důkladná a aktuální Úvod k ASP.NET trasování na internetu. Nejlepší z nich je začít se starými úvodními materiály vytvořenými pro webové formuláře, protože MVC zatím neexistovalo a doplňují je s novějšími blogových příspěvků, které se zaměřují na konkrétní problémy. V některých dobrých místech ke spuštění jsou následující zdroje:

* [Monitorování a telemetrie (vytváření Real-World cloudových aplikací s Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Kapitola E-knihy s doporučeními pro trasování v cloudových aplikacích Azure.
* [ASP.NET trasování](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Old, ale ještě dobrý prostředek pro základní Úvod k předmětu.
* [Naslouchací procesy trasování](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informace o posluchačích trasování, ale nezmiňuje [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Návod: Integrace trasování ASP.NET pomocí trasování System. Diagnostics](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Tento článek je také starý, ale obsahuje několik dalších informací, které úvodní článek nezabývá.
* [Trasování v zobrazeních ASP.NET MVC Razor](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Kromě trasování v zobrazeních Razor vystavení také vysvětluje, jak vytvořit filtr chyb, aby bylo možné protokolovat všechny neošetřené výjimky v aplikaci MVC. Informace o tom, jak protokolovat všechny neošetřené výjimky v aplikaci webových formulářů, naleznete v příkladu Global. asax v části [kompletní příklad pro obslužné rutiny chyb](/previous-versions/bb397417(v=vs.140)) na webu MSDN. V MVC nebo webových formulářích, pokud chcete zaznamenat určité výjimky, ale nechat výchozí nastavení architektury platit pro ně, můžete zachytit a znovu vyvolat jako v následujícím příkladu:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Protokolování trasování diagnostiky pro streamování z příkazového řádku Azure (plus nakoukněte)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Jak použít příkazový řádek k tomu, jak tento kurz ukazuje, jak dělat v aplikaci Visual Studio. [Nakoukněte](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) je nástroj pro ladění aplikací ASP.NET.
* [Použití Web Apps protokolování a diagnostiky – s využitím David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) a [streamování protokolů z Web Apps-with David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videa od Scottu Hanselman a David Ebbo.

Pro protokolování chyb je alternativou k psaní vlastního trasovacího kódu použití Open Source protokolovacího rozhraní, jako je [knihovny elmah](https://nuget.org/packages/elmah/). Další informace najdete v [blogovém příspěvku Scott Hanselman o knihovny elmah](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

`System.Diagnostics`K získání protokolů streamování z Azure nemusíte také používat ASP.NET ani trasování. Služba protokol streamování aplikací App Service streamuje libovolný soubor *. txt*, *. html* nebo *. log* , který najde ve složce *Logfiles* . Proto byste mohli vytvořit vlastní systém protokolování, který zapisuje do systému souborů aplikace, a váš soubor se automaticky streamuje a stáhne. Vše, co musíte udělat, je napsat kód aplikace, který vytvoří soubory ve složce *d:\home\logfiles* .

### <a name="analyzing-web-server-logs"></a>Analýza protokolů webového serveru
Další informace o analýze protokolů webového serveru najdete v následujících zdrojích informací:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Nástroj pro zobrazení dat v protokolech webového serveru (soubory *protokolu* ).
* [Řešení potíží s výkonem služby IIS nebo chyb aplikací pomocí LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Úvod do nástroje analyzátoru protokolů, který můžete použít k analýze protokolů webového serveru.
* [Blogové příspěvky od Robert blog o použití LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [Stavový kód HTTP ve službě IIS 7,0, IIS 7,5 a IIS 8,0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analýza protokolů pro trasování neúspěšných požadavků
Web Microsoft TechNet obsahuje část použití funkce pro [trasování chybných požadavků](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) , která může být užitečná pro porozumění způsobu použití těchto protokolů. Tato dokumentace se ale zaměřuje hlavně na konfiguraci trasování chybných požadavků ve službě IIS, které nemůžete dělat v Azure App Service.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki