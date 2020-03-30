---
title: Řešení potíží se sadou Visual Studio
description: Zjistěte, jak řešit potíže s aplikací App Service pomocí nástrojů pro vzdálené ladění, trasování a protokolování, které jsou integrované do Visual Studia 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280050"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Poradce při potížích s aplikací ve službě Azure App Service pomocí Sady Visual Studio
## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak pomocí nástrojů sady Visual Studio pomoci ladit aplikaci ve [službě App Service](https://go.microsoft.com/fwlink/?LinkId=529714), spuštěním v [režimu ladění](https://docs.microsoft.com/visualstudio/debugger/) vzdáleně nebo zobrazením protokolů aplikací a protokolů webového serveru.

Co se dozvíte:

* Které funkce správy aplikací jsou k dispozici v sadě Visual Studio.
* Jak pomocí vzdáleného zobrazení sady Visual Studio provádět rychlé změny ve vzdálené aplikaci.
* Jak spustit režim ladění vzdáleně, když projekt běží v Azure, a to jak pro aplikaci, tak pro webovou úlohu.
* Jak vytvořit protokoly trasování aplikace a zobrazit je při jejich vytváření aplikace.
* Jak zobrazit protokoly webového serveru, včetně podrobných chybových zpráv a trasování neúspěšných požadavků.
* Jak odeslat diagnostické protokoly do účtu Azure Storage a zobrazit je tam.

Pokud máte Visual Studio Ultimate, můžete také použít [IntelliTrace](/visualstudio/debugger/intellitrace) pro ladění. IntelliTrace není zahrnuta v tomto kurzu.

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky
Tento kurz funguje s vývojovým prostředím, webovým projektem a aplikací App Service, které jste nastavili v části [Vytvoření ASP.NET aplikace ve službě Azure App Service](app-service-web-get-started-dotnet-framework.md). Pro části WebJobs budete potřebovat aplikaci, kterou vytvoříte v [začínáme s sadou Azure WebJobs SDK][GetStartedWJ].

Ukázky kódu uvedené v tomto kurzu jsou pro webové aplikace C# MVC, ale postupy řešení potíží jsou stejné pro aplikace jazyka Visual Basic a webových formulářů.

Kurz předpokládá, že používáte Visual Studio 2019. 

Funkce protokolů datových proudů funguje pouze pro aplikace, které cílí na rozhraní .NET Framework 4 nebo novější.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Konfigurace a správa aplikací
Visual Studio poskytuje přístup k podmnožině funkcí správy aplikací a nastavení konfigurace, které jsou k dispozici na [webu Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715). V této části uvidíte, co je k dispozici pomocí **Průzkumníka serveru**. Pokud chcete zobrazit nejnovější funkce integrace Azure, vyzkoušejte taky **Cloud Explorer.** Obě okna můžete otevřít z nabídky **Zobrazení.**

1. Pokud ještě nejste přihlášení k Azure ve Visual Studiu, klikněte pravým tlačítkem na **Azure** a v **Průzkumníkovi serveru**vyberte Připojit k **předplatnému Microsoft Azure** .

    Alternativou je instalace certifikátu správy, který umožňuje přístup k vašemu účtu. Pokud se rozhodnete nainstalovat certifikát, klikněte pravým tlačítkem myši na uzel **Azure** v **Průzkumníkovi serveru**a v místní nabídce vyberte spravovat **a filtrovat předplatná.** V dialogovém okně **Spravovat předplatná Microsoft Azure** klikněte na kartu **Certifikáty** a potom klikněte na **Importovat**. Postupujte podle pokynů ke stažení a importujte soubor předplatného (nazývaný také soubor *.publishsettings)* pro váš účet Azure.

   > [!NOTE]
   > Pokud stáhnete soubor odběru, uložte jej do složky mimo adresáře zdrojového kódu (například ve složce Soubory ke stažení) a po dokončení importu jej odstraňte. Uživatel se zlými úmysly, který získá přístup k souboru předplatného, může upravovat, vytvářet a odstraňovat vaše služby Azure.
   >
   >

    Další informace o připojení k prostředkům Azure z Visual Studia najdete v [tématu Správa účtů, předplatných a rolí pro správu](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. V **Průzkumníkovi serveru**rozbalte **Azure** a **rozbalte App Service**.
3. Rozbalte skupinu prostředků, která obsahuje aplikaci, kterou jste [vytvořili](app-service-web-get-started-dotnet-framework.md)v části Vytvoření ASP.NET aplikace ve službě Azure App Service , klikněte pravým tlačítkem myši na uzel aplikace a klikněte na Nastavení **zobrazení**.

    ![Zobrazení nastavení v Průzkumníkovi serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Zobrazí se karta **Azure Web App** a uvidíte zde úlohy správy aplikací a konfigurace, které jsou dostupné ve Visual Studiu.

    ![Okno Azure Web Appu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    V tomto kurzu budete používat rozevírací rozevírací jsou v protokolování a trasování. Budete také používat vzdálené ladění, ale budete používat jinou metodu k jeho povolení.

    Informace o polích Nastavení aplikací a připojovacích řetězců v tomto okně najdete v tématu [Azure App Service: Jak fungují řetězce aplikací a připojovací řetězce](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Pokud chcete provést úlohu správy aplikací, kterou v tomto okně nejde, kliknutím na **Otevřít na portálu pro správu** otevřete okno prohlížeče na portálazure.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Přístup k souborům aplikací v Průzkumníkovi serveru
Obvykle nasadíte webový `customErrors` projekt s příznakem v souboru Web.config nastaveným na `On` nebo `RemoteOnly`, což znamená, že se nezobrazí užitečná chybová zpráva, když se něco pokazí. Pro mnoho chyb, vše, co dostanete, je stránka jako jeden z následujících:

**Chyba serveru v aplikaci '/' :**

![Nepotřebná chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Došlo k chybě:**

![Nepotřebná chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web nemůže zobrazit stránku**

![Nepotřebná chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Často nejjednodušší způsob, jak najít příčinu chyby, je povolit podrobné chybové zprávy, které první z předchozích snímků obrazovky vysvětluje, jak to udělat. To vyžaduje změnu v nasazeném souboru Web.config. Soubor *Web.config* můžete v projektu upravit a znovu nasadit, nebo vytvořit [transformaci Web.config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) a nasadit sestavení ladění, ale existuje rychlejší způsob: v **Průzkumníku řešení**můžete přímo zobrazit a upravit soubory ve vzdálené aplikaci pomocí funkce *vzdáleného zobrazení.*

1. V **Průzkumníkovi serveru**rozbalte **Azure**, rozbalte **App Service**, rozbalte skupinu prostředků, ve které se vaše aplikace nachází, a pak rozbalte uzel pro vaši aplikaci.

    Zobrazí se uzly, které vám poskytují přístup k souborům obsahu aplikace a souborůprotokolu.
2. Rozbalte uzel **Soubory** a poklepejte na soubor *Web.config.*

    ![Otevřít soubor Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio otevře soubor Web.config ze vzdálené aplikace a zobrazí [Remote] vedle názvu souboru v záhlaví.
3. Do `system.web` prvku přidejte následující řádek:

    `<customErrors mode="Off"></customErrors>`

    ![Upravit web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aktualizujte prohlížeč, který zobrazuje neužitečnou chybovou zprávu, a nyní se zobrazí podrobná chybová zpráva, například následující příklad:

    ![Podrobná chybová zpráva](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Zobrazená chyba byla vytvořena přidáním řádku zobrazeného červeně do *položky Views\Home\Index.cshtml*.)

Úpravy souboru Web.config jsou pouze jedním z příkladů scénářů, ve kterých možnost číst a upravovat soubory v aplikaci App Service usnadňuje řešení potíží.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Aplikace pro vzdálené ladění
Pokud podrobná chybová zpráva neposkytuje dostatek informací a nelze znovu vytvořit chybu místně, dalším způsobem řešení potíží je spustit v režimu ladění vzdáleně. Můžete nastavit zarážky, manipulovat s pamětí přímo, krokovat kód a dokonce změnit cestu kódu.

Vzdálené ladění nefunguje v expresních edicích sady Visual Studio.

Tato část ukazuje, jak ladit vzdáleně pomocí projektu, který vytvoříte v [vytvořit ASP.NET aplikace ve službě Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Otevřete webový projekt, který jste vytvořili v [části Vytvoření ASP.NET aplikace ve službě Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Otevřete *řadiče\HomeController.cs*.

1. Odstraňte `About()` metodu a na její místo vložte následující kód.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Nastavte zarážku](https://docs.microsoft.com/visualstudio/debugger/) na `ViewBag.Message` řádku.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a klepněte na příkaz **Publikovat**.

1. V rozevíracím seznamu **Profil** vyberte stejný profil, který jste použili v [aplikaci Create a ASP.NET ve službě Azure App Service](app-service-web-get-started-dotnet-framework.md). Potom klikněte na Nastavení.

1. V dialogovém okně **Publikovat** klikněte na kartu **Nastavení,** potom **změňte možnost Konfigurace** na **Ladění**a potom klepněte na tlačítko **Uložit**.

    ![Publikovat v režimu ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klikněte na **Publikovat**. Po dokončení nasazení a otevření prohlížeče na adresu URL Azure vaší aplikace zavřete prohlížeč.

1. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na aplikaci a potom klikněte na **Připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Prohlížeč se automaticky otevře na domovskou stránku spuštěnou v Azure. Možná budete muset počkat 20 sekund nebo tak, zatímco Azure nastaví server pro ladění. K tomuto zpoždění dochází pouze při prvním spuštění v režimu ladění v aplikaci v období 48 hodin. Když začnete ladění znovu ve stejném období, není zpoždění.

    > [!NOTE] 
    > Pokud máte potíže se spuštěním ladicího programu, zkuste to provést pomocí **Průzkumníka Cloud** namísto **Průzkumníka serveru**.
    >

1. V nabídce klikněte na **O.**

    Visual Studio se zastaví na zarážky a kód běží v Azure, ne v místním počítači.

1. Najeďte `currentTime` nad proměnnou, abyste viděli hodnotu času.

    ![Zobrazit proměnnou v režimu ladění spuštěném v Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Čas, který se zobrazí, je čas serveru Azure, který může být v jiném časovém pásmu než místní počítač.

1. Zadejte novou hodnotu pro proměnnou, `currentTime` například "Právě běží v Azure".

1. Chcete-li pokračovat v běhu, stiskněte klávesu F5.

     Stránka O spuštění v Azure zobrazí novou hodnotu, kterou jste zadali do proměnné currentTime.

     ![O stránce s novou hodnotou](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a>Vzdálené ladění webových úloh
Tato část ukazuje, jak vzdáleně ladit pomocí projektu a aplikace, kterou vytvoříte v [sadě Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkce uvedené v této části jsou k dispozici pouze v Sadě Visual Studio 2013 s aktualizací 4 nebo novější.

Vzdálené ladění funguje pouze s průběžnými webovými úlohami. Plánované a webové úlohy na vyžádání nepodporují ladění.

1. Otevřete webový projekt, který jste vytvořili v [aplikaci Začínáme s sadou Azure WebJobs SDK][GetStartedWJ].

2. V projektu ContosoAdsWebJob otevřete *Functions.cs*.

3. [Nastavte zarážku](https://docs.microsoft.com/visualstudio/debugger/) na první `GnerateThumbnail` příkaz v metodě.

    ![Nastavení zarážky](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na webový projekt (nikoli na projekt WebJob) a klepněte na příkaz **Publikovat**.

5. V rozevíracím seznamu **Profil** vyberte stejný profil, který jste použili v [aplikaci Začínáme s sadou Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klepněte na kartu **Nastavení,** změňte **možnost Konfigurace** na **Ladit**a potom klepněte na **tlačítko Publikovat**.

    Visual Studio nasazuje webové projekty a projekty WebJob a váš prohlížeč se otevře na adresu URL Azure vaší aplikace.

7. V **Průzkumníkovi serveru** **rozbalte službu Azure > App Service > skupinu prostředků > aplikace > webjobs > průběžné**a potom klikněte pravým tlačítkem myši na **ContosoAdsWebJob**.

8. Klepněte na **tlačítko Připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Prohlížeč se automaticky otevře na domovskou stránku spuštěnou v Azure. Možná budete muset počkat 20 sekund nebo tak, zatímco Azure nastaví server pro ladění. K tomuto zpoždění dochází pouze při prvním spuštění v režimu ladění v aplikaci v období 48 hodin. Když začnete ladění znovu ve stejném období, není zpoždění.

9. Ve webovém prohlížeči, který se otevře na domovské stránce reklamy společnosti Contoso, vytvořte novou reklamu.

    Vytvoření reklamy způsobí vytvoření zprávy fronty, která je vyzvednuta webovou úlohou a zpracována. Když sada WebJobs SDK zavolá funkci ke zpracování zprávy fronty, kód narazí na zarážku.

10. Když ladicí program rozdělí na zarážku, můžete zkoumat a měnit hodnoty proměnných, zatímco program běží v cloudu. Na následujícím obrázku ladicí program zobrazuje obsah objektu blobInfo, který byl předán metodě. `GenerateThumbnail`

     ![Objekt blobInfo v ladicím programu](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Chcete-li pokračovat v běhu, stiskněte klávesu F5.

     Metoda `GenerateThumbnail` dokončí vytvoření miniatury.

12. V prohlížeči aktualizujte stránku Rejstřík a uvidíte miniaturu.

13. V sadě Visual Studio stisknutím kláves SHIFT+F5 ukončit ladění.

14. V **Průzkumníkovi serveru**klepněte pravým tlačítkem myši na uzel ContosoAdsWebJob a klepněte na příkaz **Zobrazit řídicí panel**.

15. Přihlaste se pomocí přihlašovacích údajů Azure a kliknutím na název webové úlohy přejděte na stránku pro webovou úlohu.

     ![Klikněte na ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Řídicí panel ukazuje, že `GenerateThumbnail` funkce byla spuštěna nedávno.

     (Při příštím klepnutí na tlačítko **Zobrazit řídicí panel**se nemusíte přihlašovat a prohlížeč přejde přímo na stránku vaší webové úlohy.)

16. Kliknutím na název funkce zobrazíte podrobnosti o spuštění funkce.

     ![Podrobnosti o funkci](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Pokud vaše funkce [zapisovala protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki), můžete je zobrazit klepnutím na příkaz **ToggleOutput.**

## <a name="notes-about-remote-debugging"></a>Poznámky ke vzdálenému ladění

* Spuštění v režimu ladění v produkčním prostředí se nedoporučuje. Pokud vaše produkční aplikace není škálována na více instancí serveru, ladění zabrání webovému serveru reagovat na jiné požadavky. Pokud máte více instancí webového serveru, při připojení k ladicímu programu získáte náhodnou instanci a nemáte žádný způsob, jak zajistit, aby následné požadavky prohlížeče přecčovalo do stejné instance. Také obvykle nenasadíte sestavení ladění do produkčního prostředí a optimalizace kompilátoru pro sestavení verzí může znemožnit zobrazit, co se děje řádek po řádku ve zdrojovém kódu. Při řešení problémů s produkčními problémy je nejlepším zdrojem trasování aplikací a protokolů webového serveru.
* Při vzdáleném ladění se vyhněte dlouhým zarážekm v zarážky. Azure zpracovává proces, který je zastaven déle než několik minut jako proces neodpovídá a vypne jej.
* Při ladění server odesílá data do sady Visual Studio, což může ovlivnit poplatky za šířku pásma. Informace o sazbách za šířku pásma najdete v [tématu Azure Pricing](https://azure.microsoft.com/pricing/calculator/).
* Ujistěte se, `debug` že `compilation` atribut prvku v souboru *Web.config* je nastaven na hodnotu true. Je nastavena na hodnotu true ve výchozím nastavení při publikování konfigurace sestavení ladění.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Pokud zjistíte, že ladicí program není krok do kódu, který chcete ladit, bude pravděpodobně muset změnit pouze můj kód nastavení.  Další informace naleznete [v tématu Určení, zda se má ladit pouze uživatelský kód pomocí pouze můj kód v sadě Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Časovač se spustí na serveru, když povolíte funkci vzdáleného ladění a po 48 hodinách je funkce automaticky vypnuta. Tento 48hodinový limit se provádí z důvodů zabezpečení a výkonu. Funkci můžete snadno zapnout tolikrát, kolikrát chcete. Doporučujeme ponechat zakázáno, pokud nejste aktivně ladění.
* Ladicí program můžete ručně připojit k libovolnému procesu, nejen k procesu aplikace (w3wp.exe). Další informace o použití režimu ladění v sadě Visual Studio naleznete [v tématu Ladění v sadě Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Přehled diagnostických protokolů
ASP.NET aplikace, která běží v aplikaci App Service můžete vytvořit následující druhy protokolů:

* **Protokoly trasování aplikací**<br/>
  Aplikace vytvoří tyto protokoly voláním metody [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) třídy.
* **Protokoly webového serveru**<br/>
  Webový server vytvoří položku protokolu pro každý požadavek HTTP do aplikace.
* **Podrobné protokoly chybových zpráv**<br/>
  Webový server vytvoří stránku HTML s některými dalšími informacemi o neúspěšných požadavcích HTTP (požadavky, které mají za následek stavový kód 400 nebo vyšší).
* **Protokoly trasování neúspěšných požadavků**<br/>
  Webový server vytvoří soubor XML s podrobnými informacemi o trasování pro neúspěšné požadavky HTTP. Webový server také poskytuje soubor XSL pro formátování XML v prohlížeči.

Protokolování ovlivňuje výkon aplikace, takže Azure vám dává možnost povolit nebo zakázat každý typ protokolu podle potřeby. Pro protokoly aplikací můžete určit, že by měly být zapsány pouze protokoly nad určitou úroveň závažnosti. Při vytváření nové aplikace je ve výchozím nastavení zakázáno veškeré protokolování.

Protokoly jsou zapsány do souborů ve složce *LogFiles* v systému souborů vaší aplikace a jsou přístupné prostřednictvím FTP. Protokoly webového serveru a protokoly aplikací lze také zapsat do účtu úložiště Azure. V účtu úložiště můžete zachovat větší objem protokolů, než je možné v systému souborů. Při použití systému souborů jste omezeni na maximálně 100 megabajtů protokolů. (Protokoly systému souborů jsou určeny pouze pro krátkodobé uchovávání. Azure odstraní staré soubory protokolu, aby po dosažení limitu uvolnil místo pro nové.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Vytvoření a zobrazení protokolů trasování aplikací
V této části projdete následujícími úkoly:

* Přidejte příkazy trasování do webového projektu, který jste vytvořili v [začínáme s Azure a ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Zobrazení protokolů při spuštění projektu místně.
* Zobrazení protokolů tak, jak jsou generovány aplikací spuštěnou v Azure.

Informace o vytváření protokolů aplikací v webových úlohách najdete v tématu [Jak pracovat s úložištěm front Azure pomocí sady WebJobs SDK – Jak psát protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki). Následující pokyny pro zobrazení protokolů a řízení, jak jsou uloženy v Azure platí také pro protokoly aplikací vytvořené WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Přidání příkazů trasování do aplikace
1. Otevřete *Controllers\HomeController.cs*a `Index` `About`nahraďte metody , `Contact` a metody `Trace` a `using` metody `System.Diagnostics`následujícím kódem, abyste mohli přidat příkazy a příkaz pro :

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

1. Přidejte `using System.Diagnostics;` příkaz do horní části souboru.

### <a name="view-the-tracing-output-locally"></a>Zobrazení výstupu trasování místně
1. Stisknutím klávesy F5 spusťte aplikaci v režimu ladění.

    Výchozí naslouchací proces trasování zapíše veškerý výstup trasování do okna **Výstup** spolu s dalším ladicím výstupem. Následující obrázek znázorňuje výstup z příkazů `Index` trasování, které jste přidali k metodě.

    ![Trasování v okně Ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Následující kroky ukazují, jak zobrazit výstup trasování na webové stránce bez kompilace v režimu ladění.
1. Otevřete soubor Web.config aplikace (ten, který se `<system.diagnostics>` nachází ve složce projektu) a `</configuration>` přidejte prvek na konec souboru těsně před uzavírací prvek:

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

Umožňuje `WebPageTraceListener` zobrazit výstup trasování procházením na . `/trace.axd`
1. Přidejte do `<system.web>` souboru Web.config <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">prvek trasování,</a> například v následujícím příkladu:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Stiskněte klávesy CTRL+F5 a spusťte aplikaci.
1. V adresním řádku okna prohlížeče přidejte do adresy URL soubor *trace.axd* a `http://localhost:53370/trace.axd`stiskněte Enter (adresa URL je podobná ).
1. Na stránce **Trasování aplikace** klikněte na **zobrazit podrobnosti** na prvním řádku (ne na řádku BrowserLink).

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Zobrazí se stránka **Podrobnosti požadavku** a v části **Informace o trasování** se `Index` zobrazí výstup z příkazů trasování, které jste přidali do metody.

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Ve výchozím `trace.axd` nastavení je k dispozici pouze místně. Pokud jste chtěli zpřístupnit z aplikace vzdálené, `localOnly="false"` můžete `trace` přidat do prvku v souboru *Web.config,* jak je znázorněno v následujícím příkladu:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Povolení `trace.axd` v produkční aplikaci se však z bezpečnostních důvodů nedoporučuje. V následujících částech uvidíte jednodušší způsob čtení protokolů trasování v aplikaci App Service.

### <a name="view-the-tracing-output-in-azure"></a>Zobrazení výstupu trasování v Azure
1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na webový projekt a klepněte na příkaz **Publikovat**.
2. V dialogovém okně **Publikovat web** klepněte na tlačítko **Publikovat**.

    Po visual studio publikuje aktualizaci, otevře okno prohlížeče na domovskou stránku (za předpokladu, že jste nevymazat **cílovou adresu URL** na kartě **Připojení).**
3. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na aplikaci a vyberte **zobrazit protokoly datových proudů**.

    ![Zobrazit protokoly datových proudů v místní nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Okno Výstup** ukazuje, že jste připojeni ke službě streamování protokolu a přidá řádek oznámení každou minutu, která prochází bez protokolu k zobrazení.

    ![Zobrazit protokoly datových proudů v místní nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. V okně prohlížeče, které zobrazuje domovskou stránku aplikace, klikněte na **Kontakt**.

    Během několika sekund se v okně **Výstup** zobrazí výstup `Contact` z trasování na úrovni chyby, které jste přidali k metodě.

    ![Trasování chyb ve výstupním okně](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio zobrazuje pouze trasování na úrovni chyb, protože to je výchozí nastavení při povolení služby sledování protokolu. Když vytvoříte novou aplikaci App Service, všechna protokolování se ve výchozím nastavení deaktivují, jak jste viděli při dřívějším otevření stránky nastavení:

    ![Odhlašování aplikací](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Pokud jste však vybrali **možnost Zobrazit protokoly datových proudů**, aplikace Visual Studio automaticky změnila **protokolování aplikací (systém souborů)** na **chybu**, což znamená, že budou hlášeny protokoly na úrovni chyb. Chcete-li zobrazit všechny protokoly trasování, můžete toto nastavení změnit na **Verbose**. Vyberete-li úroveň závažnosti nižší než chyba, jsou hlášeny také všechny protokoly pro vyšší úrovně závažnosti. Takže když vyberete podrobné, zobrazí se také informace, upozornění a protokoly chyb.  

5. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na aplikaci a potom klikněte na **Zobrazit nastavení** stejně jako dříve.
6. Změňte **protokolování aplikací (systém souborů)** na **podrobné a**klepněte na tlačítko **Uložit**.

    ![Nastavení úrovně trasování na Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. V okně prohlížeče, které nyní zobrazuje vaši **stránku Kontakt,** klikněte na **Domů**, potom klikněte na **položku O**aplikaci a potom klikněte na **položku Kontakt**.

    Během několika sekund zobrazí okno **Výstup** veškerý výstup trasování.

    ![Podrobný výstup trasování](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    V této části jste povolili a zakázali protokolování pomocí nastavení aplikace. Můžete také povolit a zakázat posluchače trasování úpravou souboru Web.config. Úprava souboru Web.config však způsobí, že doména aplikace bude recyklovat, zatímco povolení protokolování prostřednictvím konfigurace aplikace to neuděláte. Pokud problém trvá dlouhou dobu reprodukovat, nebo je přerušovaný, recyklace domény aplikace může "opravit" a přinutit, abyste počkat, až se to stane znovu. Povolení diagnostiky v Azure vám umožní okamžitě začít zachycovat informace o chybách bez recyklace domény aplikace.

### <a name="output-window-features"></a>Funkce výstupního okna
Karta **Microsoft Azure Logs** v okně **Výstup** obsahuje několik tlačítek a textové pole:

![Tlačítka karty Protokoly](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Tyto provádět následující funkce:

* Vymažte okno **Výstup.**
* Povolte nebo zakažte zalamování řádků.
* Spusťte nebo zastavte protokoly monitorování.
* Určete, které protokoly mají být sledovány.
* Stáhněte si protokoly.
* Protokoly filtrů založené na vyhledávacím řetězci nebo regulárním výrazu.
* Zavřete okno **Výstup.**

Pokud zadáte vyhledávací řetězec nebo regulární výraz, Visual Studio filtruje informace o protokolování na straně klienta. To znamená, že můžete zadat kritéria po protokoly jsou zobrazeny v okně **Výstup** a můžete změnit kritéria filtrování bez nutnosti regenerovat protokoly.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Zobrazit protokoly webového serveru
Protokoly webového serveru zaznamenávají všechny aktivity protokolu HTTP pro aplikaci. Chcete-li je zobrazit v okně **Výstup,** musíte je povolit pro aplikaci a sdělit visual studiu, že je chcete sledovat.

1. Na kartě **Konfigurace webové aplikace Azure,** kterou jste otevřeli v **Průzkumníkovi serveru**, změňte protokolování webového serveru na **Zapnuto**a klikněte na **Uložit**.

    ![Povolení protokolování webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. V okně **Výstup** klikněte na **tlačítko Určit, které protokoly Microsoft Azure monitorují.**

    ![Určení protokolů Azure, které mají být monitorována](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. V dialogovém okně **Možnosti protokolování Microsoft Azure** vyberte **protokoly webového serveru**a klepněte na tlačítko **OK**.

    ![Sledování protokolů webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. V okně prohlížeče, které aplikaci zobrazuje, klikněte na **Domů**, potom klikněte na **O aplikaci**a potom klikněte na **Kontakt**.

    Protokoly aplikací se obvykle zobrazují jako první, následované protokoly webového serveru. Možná budete muset chvíli počkat, než se protokoly zobrazí.

    ![Protokoly webového serveru v okně Výstup](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Ve výchozím nastavení při prvním povolení protokolů webového serveru pomocí sady Visual Studio azure zapíše protokoly do systému souborů. Jako alternativu můžete použít portál Azure k určení, že protokoly webového serveru by měly být zapsány do kontejneru objektů blob v účtu úložiště.

Pokud pomocí portálu povolíte protokolování webového serveru do účtu úložiště Azure a potom zakážete protokolování v sadě Visual Studio, při opětovném povolení protokolování v sadě Visual Studio se obnoví nastavení účtu úložiště.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Zobrazit podrobné protokoly chybových zpráv
Podrobné protokoly chyb poskytují některé další informace o požadavcích HTTP, které mají za následek kódy chybové odpovědi (400 nebo vyšší). Chcete-li je zobrazit v okně **Výstup,** musíte je povolit pro aplikaci a sdělit visual studiu, že je chcete sledovat.

1. Na kartě **Konfigurace webové aplikace Azure,** kterou jste otevřeli v **Průzkumníkovi serveru**, změňte podrobné chybové zprávy na **Zapnuto** a klikněte na **Uložit**. **On**

    ![Povolení podrobných chybových zpráv](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. V okně **Výstup** klikněte na **tlačítko Určit, které protokoly Microsoft Azure monitorují.**

3. V dialogovém okně **Možnosti protokolování Microsoft Azure** klikněte na **všechny protokoly**a potom klikněte na **OK**.

    ![Sledovat všechny protokoly](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. V adresním řádku okna prohlížeče přidejte k adrese URL další znak, který `http://localhost:53370/Home/Contactx`způsobí chybu 404 (například ) a stiskněte Enter.

    Po několika sekundách se v okně **Visual** Studio Output zobrazí podrobný protokol chyb.

    ![Podrobný protokol chyb - výstupní okno](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl+klikněte na odkaz pro zobrazení výstupu protokolu formátovaného v prohlížeči:

    ![Podrobný protokol chyb - okno prohlížeče](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Stáhnout protokoly systému souborů
Všechny protokoly, které můžete sledovat v okně **Výstup,** lze také stáhnout jako soubor *ZIP.*

1. V okně **Výstup** klepněte na **položku Stáhnout protokoly datových proudů**.

    ![Tlačítka karty Protokoly](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Průzkumník souborů se otevře do složky *Soubory ke stažení* s vybraným staženým souborem.

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahujte soubor *ZIP* a zobrazí se následující struktura složek:

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Protokoly trasování aplikací jsou v souborech *TXT* ve složce *LogFiles\Application.*
   * Protokoly webového serveru jsou v souborech *PROTOKOLU* ve složce *LogFiles\http\RawLogs.* K zobrazení těchto souborů a manipulaci s nimi můžete použít nástroj, například [Analyzátor protokolů.](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659)
   * Podrobné protokoly chybových zpráv jsou v souborech *HTML* ve složce *LogFiles\DetailedErrors.*

     (Složka *nasazení* je pro soubory vytvořené publikováním správy zdrojového kódu; nemá nic, co by souviselo s publikováním v sadě Visual Studio. Složka *Git* je pro trasování související s publikováním správy zdrojového kódu a službou streamování souborů protokolu.)  

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
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
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
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Zobrazit protokoly trasování neúspěšných požadavků
Protokoly trasování neúspěšných požadavků jsou užitečné, když potřebujete porozumět podrobnostem o tom, jak službu IIS zpracovává požadavek HTTP, v situacích, jako je přepis ování adres URL nebo problémy s ověřováním.

Aplikace služby App Service používají stejnou funkci trasování neúspěšných požadavků, která je k dispozici se službou IIS 7.0 a novější. Nemáte však přístup k nastavení služby IIS, která konfigurují, které chyby se zaznamenávají. Pokud povolíte trasování neúspěšných požadavků, budou zachyceny všechny chyby.

Trasování neúspěšných požadavků můžete povolit pomocí sady Visual Studio, ale nelze je zobrazit v sadě Visual Studio. Tyto protokoly jsou soubory XML. Služba protokolu datových proudů sleduje pouze soubory, které jsou považovány za čitelné v režimu prostého textu: *.txt*, *.html*a *.log.*

Protokoly trasování neúspěšných požadavků můžete zobrazit v prohlížeči přímo prostřednictvím protokolu FTP nebo místně po použití nástroje FTP ke stažení do místního počítače. V této části je zobrazíte přímo v prohlížeči.

1. Na **kartě Konfigurace** v okně **Azure Web Appu,** které jste otevřeli z **Průzkumníka serveru**, změňte **trasování neúspěšných požadavků** na **Zapnuto**a klikněte na **Uložit**.

    ![Povolit trasování neúspěšných požadavků](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. V adresním řádku okna prohlížeče, které zobrazuje aplikaci, přidejte další znak do adresy URL a kliknutím na Enter způsobíte chybu 404.

    To způsobí, že nepodařilo protokol trasování požadavku, které mají být vytvořeny a následující kroky ukazují, jak zobrazit nebo stáhnout protokol.

3. Ve Visual Studiu klikněte na kartě **Konfigurace** v okně **Azure Web Appu** na **Otevřít na portálu pro správu**.

4. Na stránce **Nastavení** [portálu Azure](https://portal.azure.com) pro vaši aplikaci klikněte na Přihlašovací údaje **pro nasazení**a zadejte nové uživatelské jméno a heslo.

    ![Nové uživatelské jméno a heslo FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Když se přihlásíte, musíte použít celé uživatelské jméno s předponou názvu aplikace. Například pokud zadáte "myid" jako uživatelské jméno a web je "myexample", přihlaste se jako "myexample\myid".
    >

5. V novém okně prohlížeče přejděte na adresu URL, která se zobrazuje pod **názvem hostitele FTP** nebo **název_hostitele FTPS** na stránce **Přehled** vaší aplikace.

6. Přihlaste se pomocí pověření FTP, které jste vytvořili dříve (včetně předpony názvu aplikace pro uživatelské jméno).

    Prohlížeč zobrazuje kořenovou složku aplikace.

7. Otevřete složku *LogFiles.*

    ![Otevřít složku LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otevřete složku s názvem W3SVC plus číselnou hodnotu.

    ![Otevřít složku W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Složka obsahuje soubory XML pro všechny chyby, které byly zaznamenány po povolení vektorizace neúspěšných požadavků, a soubor XSL, který může prohlížeč použít k formátování xml.

    ![Složka W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klikněte na soubor XML pro neúspěšný požadavek, pro který chcete zobrazit informace o trasování.

    Následující obrázek znázorňuje část informací o trasování pro ukázkovou chybu.

    ![Trasování neúspěšných požadavků v prohlížeči](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Další kroky
Viděli jste, jak Visual Studio usnadňuje zobrazení protokolů vytvořených aplikací služby App Service. Následující části obsahují odkazy na další zdroje informací o souvisejících tématech:

* Řešení potíží se službou App Service
* Ladění v sadě Visual Studio
* Vzdálené ladění v Azure
* Trasování v ASP.NET aplikacích
* Analýza protokolů webového serveru
* Analýza protokolů trasování neúspěšných požadavků
* Ladění cloudových služeb

### <a name="app-service-troubleshooting"></a>Řešení potíží se službou App Service
Další informace o řešení potíží s aplikacemi ve službě Azure App Service najdete v následujících zdrojích informací:

* [Jak sledovat aplikace](web-sites-monitor.md)
* [Zkoumání nevracení paměti ve službě Azure App Service s Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Příspěvek blogu Microsoft ALM o funkcích sady Visual Studio pro analýzu problémů se spravovanou pamětí
* [Online nástroje služby Azure App Service, o kterých byste měli vědět](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blog post od Amit Apple.

Chcete-li pomoci s konkrétní otázkou pro řešení potíží, spusťte vlákno v jednom z následujících fór:

* [Fórum Azure na webu ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Fórum Azure na webu Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Ladění v sadě Visual Studio
Další informace o použití režimu ladění v sadě Visual Studio naleznete [v tématu Ladění v sadě Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) a tipy pro ladění pomocí sady Visual Studio [2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Vzdálené ladění v Azure
Další informace o vzdáleném ladění aplikací služby App Service a webových úloh naleznete v následujících zdrojích:

* [Úvod do vzdálenéladění služby Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Úvod do části 2 vzdáleného ladění služby Azure App Service – uvnitř vzdáleného ladění](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Úvod do vzdáleného ladění ve části 3 služby Azure App Service – prostředí s více instancemi a GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Ladění webových úloh (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Pokud vaše aplikace používá back-end Azure Web API nebo Mobilní služby a potřebujete ho ladit, přečtěte si část [Ladění backendu .NET v sadě Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Trasování v ASP.NET aplikacích
Neexistují žádné důkladné a aktuální představy o ASP.NET trasování k dispozici na Internetu. To nejlepší, co můžete udělat, je začít se starými úvodními materiály napsanými pro webové formuláře, protože MVC ještě neexistovalo, a doplnit to s novějšími blogovými příspěvky, které se zaměřují na konkrétní problémy. Několik dobrých míst, kde začít, jsou následující zdroje:

* [Monitorování a telemetrie (vytváření reálných cloudových aplikací s Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  Kapitola elektronické knihy s doporučeními pro trasování v cloudových aplikacích Azure.
* [ASP.NET trasování](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Starý, ale stále dobrý zdroj pro základní úvod do předmětu.
* [Moduly naslouchání trasování](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informace o naslouchacích procesech trasování, ale nezmiňuje [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Návod: Integrace trasování ASP.NET se sledováním systému.Diagnostika](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Tento článek je také starý, ale obsahuje některé další informace, které úvodní článek nezahrnuje.
* [Trasování v ASP.NET zobrazení holicího strojku MVC](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Kromě trasování v zobrazení Razor, post také vysvětluje, jak vytvořit filtr chyb, aby se přihlásit všechny neošetřené výjimky v aplikaci MVC. Informace o tom, jak protokolovat všechny neošetřené výjimky v aplikaci webových formulářů, naleznete v příkladu Global.asax v [kompletním příkladu obslužných rutin chyb](/previous-versions/bb397417(v=vs.140)) v síti MSDN. V MVC nebo webových formulářů, pokud chcete protokolovat určité výjimky, ale nechat výchozí zpracování rozhraní projevit pro ně, můžete zachytit a znovu vyvolat jako v následujícím příkladu:

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

* [Protokolování trasování diagnostiky datových proudů z příkazového řádku Azure (plus pohled!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Použití příkazového řádku k tomu, co tento kurz ukazuje, jak dělat v sadě Visual Studio. [Pohled](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) je nástroj pro ladění aplikací ASP.NET.
* [Používání protokolování a diagnostiky webových aplikací – s Davidem Ebbem](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) a [protokoly datových proudů z webových aplikací – s Davidem Ebbem](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videa Scott Hanselman a David Ebbo.

Pro protokolování chyb je alternativou k napsání vlastního trasovacího kódu použití open source architektury protokolování, jako je [ELMAH](https://nuget.org/packages/elmah/). Další informace naleznete v [blogových příspěvcích Scotta Hanselmana o elmah](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Taky nemusíte používat ASP.NET nebo `System.Diagnostics` trasování získat protokoly streamování z Azure. Služba protokolu streamování aplikací aplikace streamuje libovolný soubor *TXT*, *HTML*nebo *.log,* který najde ve složce *LogFiles.* Proto můžete vytvořit vlastní systém protokolování, který zapisuje do systému souborů aplikace a váš soubor je automaticky streamován a stažen. Jediné, co musíte udělat, je napsat kód aplikace, který vytváří soubory ve složce *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Analýza protokolů webového serveru
Další informace o analýze protokolů webového serveru naleznete v následujících zdrojích:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Nástroj pro prohlížení dat v protokolech webového serveru (*.log* files).
* [Poradce při potížích s výkonem služby IIS nebo chybám aplikací pomocí logparseru](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Úvod do nástroje analyzátor protokolů, který můžete použít k analýze protokolů webového serveru.
* [Blog příspěvky Robert McMurray na používání LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Stavový kód HTTP ve skutcích 7.0, IIS 7.5 a IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analýza protokolů trasování neúspěšných požadavků
Web Microsoft TechNet obsahuje část [Using Failed Request Tracing,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) která může být užitečná pro pochopení použití těchto protokolů. Tato dokumentace se však zaměřuje především na konfiguraci trasování neúspěšných požadavků ve službě IIS, což nelze provést ve službě Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
