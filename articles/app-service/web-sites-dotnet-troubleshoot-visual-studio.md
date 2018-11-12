---
title: Řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio
description: Informace o řešení potíží s webovou aplikaci Azure s použitím vzdálené ladění, trasování a protokolování nástroje, které jsou integrované do sady Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.openlocfilehash: 2028de68b3c50f24c46bfafd804b00e84301a9d7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256591"
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio
## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak používat nástroje Visual Studia k ladění webové aplikace v [služby App Service](https://go.microsoft.com/fwlink/?LinkId=529714), běží v [režimu ladění](https://docs.microsoft.com/visualstudio/debugger/) vzdáleně nebo zobrazením aplikačních protokolů a protokolů webového serveru.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Naučíte se:

* Funkcích správy, které Azure webové aplikace jsou k dispozici v sadě Visual Studio.
* Jak pomocí sady Visual Studio vzdálené zobrazení provádění rychlé změn ve vzdálené webové aplikaci.
* Spuštění režimu ladění, vzdálené při projekt běží v Azure, webové aplikace i pro webové úlohy.
* Jak vytvořit protokoly trasování aplikací a zobrazit při použití je jejich vytváření.
* Postup zobrazení protokolů webového serveru, včetně podrobných chybových zpráv a trasování neúspěšných žádostí.
* Postup odesílání diagnostických protokolů do služby Azure Storage account a zobrazit je.

Pokud máte Visual Studio Ultimate, můžete také použít [IntelliTrace](https://msdn.microsoft.com/library/vstudio/dd264915.aspx) pro ladění. IntelliTrace nejsou uvedeny v tomto kurzu.

## <a name="prerequisites"></a>Požadavky
Tento kurz pracuje s vývojovým prostředím, webový projekt a webovou aplikaci Azure, které jste nastavili v [Začínáme s Azure a ASP.NET](app-service-web-get-started-dotnet-framework.md). U oddílů, WebJobs, budete potřebovat aplikaci, kterou vytvoříte v [Začínáme se sadou Azure WebJobs SDK][GetStartedWJ].

Ukázky kódu, které jsou uvedené v tomto kurzu jsou pro webovou aplikaci C# MVC, ale řešení problémů s postupy jsou stejné pro aplikace Visual Basic a webových formulářů.

Kurz předpokládá, že používáte Visual Studio 2017. 

Datový proud protokolů funkce funguje pouze u aplikací určených pro rozhraní .NET Framework 4 nebo novější.

## <a name="sitemanagement"></a>Konfigurace webové aplikace a Správa
Poskytuje přístup k podmnožinu funkcí správy webové aplikace a nastavení konfigurace, které jsou k dispozici v sadě Visual Studio [webu Azure portal](https://go.microsoft.com/fwlink/?LinkId=529715). V této části se zobrazí, co je k dispozici prostřednictvím **Průzkumníka serveru**. Pokud chcete zobrazit nejnovější funkce integrace služby Azure, vyzkoušejte si **Průzkumníka cloudu** také. Můžete otevřít z windows **zobrazení** nabídky.

1. Pokud nejste ještě přihlášení do Azure v sadě Visual Studio, klikněte pravým tlačítkem na **Azure** a vyberte připojit k **předplatné Microsoft Azure** v **Průzkumníka serveru**.

    Další možností je nainstalovat certifikát pro správu, který umožňuje přístup k vašemu účtu. Pokud se rozhodnete k instalaci certifikátu, klikněte pravým tlačítkem myši **Azure** uzel v **Průzkumníka serveru**a pak vyberte **spravovat a filtrovat předplatná** v místní nabídce. V **spravovat předplatná Microsoft Azure** dialogové okno, klikněte na tlačítko **certifikáty** kartu a potom klikněte na tlačítko **Import**. Postupujte podle pokynů ke stažení a importujte soubor předplatného (také nazývané *.publishsettings* souboru) pro váš účet Azure.

   > [!NOTE]
   > Pokud chcete stáhnout soubor předplatného, uloží ho do složky mimo váš kód adresáře zdrojových souborů (například ve složce stažené soubory) a odstraňte ho po dokončení importu. Uživatel se zlými úmysly, který získá přístup k souboru předplatného můžete upravit, vytvoření a odstranění služby Azure.
   >
   >

    Další informace o připojení k prostředkům Azure ze sady Visual Studio najdete v tématu [Správa účtů, předplatných a správních rolí](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. V **Průzkumníka serveru**, rozbalte **Azure** a rozbalte **služby App Service**.
3. Rozbalte skupinu prostředků obsahující webovou aplikaci, kterou jste vytvořili v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet-framework.md)a pak klikněte pravým tlačítkem na uzel webové aplikace a klikněte na tlačítko **nastavení zobrazení**.

    ![Zobrazit nastavení v Průzkumníku serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Webové aplikace Azure** kartě se zobrazí a můžete tam uvidíme webové aplikace úkolů správy a konfigurace, které jsou k dispozici v sadě Visual Studio.

    ![Okno Azure webové aplikace](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    V tomto kurzu budete používat protokolování a trasování rozevírací seznamy. Můžete také využít vzdálené ladění, ale budete používat jinou metodu, aby je.

    Informace o polích nastavení aplikace a připojovacích řetězců v tomto okně najdete v tématu [Azure Web Apps: fungování řetězců aplikace a připojovacích řetězců](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Pokud chcete provést úlohu správy webové aplikace, které není možné provést toto okno, klikněte na tlačítko **otevření portálu pro správu** otevřete okno prohlížeče a webu Azure portal.

## <a name="remoteview"></a>Přístup k webové aplikaci soubory v Průzkumníku serveru
Obvykle nasazení webového projektu s `customErrors` příznak v souboru Web.config nastavte na `On` nebo `RemoteOnly`, což znamená, že nebudete mít užitečné chybová zpráva, když se něco nepovede. Pro mnoho chyb je vše, co se zobrazí stránka jako jednu z následujících nástrojů:

**Chyba serveru v aplikaci '/':**

![Neužitečné chybovou stránku](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Došlo k chybě:**

![Neužitečné chybovou stránku](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web nelze zobrazit stránku**

![Neužitečné chybovou stránku](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Nejjednodušší způsob, jak najít příčinu chyby je často povolit podrobné chybové zprávy, které první na předchozích snímcích obrazovky vysvětluje, jak provést. Který vyžaduje změny v nasazeném souboru Web.config. Může upravit *Web.config* souboru v projektu a znovu nasaďte projekt nebo vytvořte [transformaci Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) a nasadit sestavení pro ladění, ale není tak rychlejší: v **Průzkumníka řešení** , můžete přímo zobrazit a upravovat soubory ve vzdálené webové aplikace s použitím *vzdálené zobrazení* funkce.

1. V **Průzkumníka serveru**, rozbalte **Azure**, rozbalte **služby App Service**, rozbalte skupinu prostředků, webové aplikace se nachází v a potom rozbalte uzel pro vaši webovou aplikaci.

    Zobrazí uzly, které poskytují přístup k obsahu souborů a souborů protokolů webové aplikace.
2. Rozbalte **soubory** uzlu a dvakrát klikněte na *Web.config* souboru.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio otevře soubor Web.config ze vzdálené webové aplikace a zobrazuje [vzdálené] vedle názvu souboru v záhlaví programu.
3. Přidejte následující řádek, který `system.web` element:

    `<customErrors mode="Off"></customErrors>`

    ![Upravit soubor Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aktualizujte prohlížeč, který se zobrazuje zpráva čitelná a nyní získáte podrobné chybové zprávy, jako v následujícím příkladu:

    ![Podrobná chybová zpráva](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Zobrazit tato chyba byla vytvořena přidáním řádku zobrazí červeně na *Views\Home\Index.cshtml*.)

Úpravy souboru Web.config je pouze jeden příklad scénáře, ve kterých možnost číst a upravovat soubory na svou webovou aplikaci Azure provést odstraňování problémů.

## <a name="remotedebug"></a>Vzdálené ladění web apps
Pokud podrobnou chybovou zprávu neposkytuje dostatek informací, a nelze znovu vytvořit chybu místně, je další způsob, jak řešit vzdálené spouštění v režimu ladění. Můžete nastavit zarážky, pracovat přímo s paměti, krokovat kód a dokonce i změnit cestu kódu.

Vzdálené ladění nebude fungovat v edicích Express sady Visual Studio.

Tato část ukazuje, jak ladit vzdáleně pomocí aplikace project můžete vytvořit v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet-framework.md).

1. Otevřete webový projekt, který jste vytvořili v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet-framework.md).

2. Otevřít *Controllers\HomeController.cs*.

3. Odstranit `About()` metoda a vložte následující kód na příslušné místo.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```

4. [Nastavit zarážku](https://docs.microsoft.com/visualstudio/debugger/) na `ViewBag.Message` řádku.

5. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a klikněte na tlačítko **publikovat**.

6. V **profilu** rozevíracího seznamu vyberte stejné, jako jste použili v profilu [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet-framework.md). Potom klikněte na nastavení.

7. V **publikovat** dialogového okna, klikněte na tlačítko **nastavení** kartu a potom změňte **konfigurace** k **ladění**a potom klikněte na tlačítko  **Uložit**.

    ![Publikování v režimu ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Klikněte na **Publikovat**. Po nasazení dokončí a prohlížeči se otevře na adresu URL webové aplikace Azure, ukončete prohlížeč.

9. V **Průzkumníka serveru**, klikněte pravým tlačítkem na svou webovou aplikaci a pak klikněte na tlačítko **připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Na domovskou stránku běžící v Azure se automaticky otevře v prohlížeči. Budete muset počkejte 20 sekund a tak Azure nastaví server pro ladění. Toto zpoždění situace nastane pouze při prvním spuštění v režimu ladění ve webové aplikaci 48 hodin. Při zahájení ladění znovu ve stejném období není k dispozici ke zpoždění.

    > [!NOTE] 
    > Pokud máte jakékoli potíže, spouští se ladicí program, zkuste provést pomocí **Průzkumníka cloudu** místo **Průzkumníka serveru**.
    >

10. Klikněte na tlačítko **o** v nabídce.

     Visual Studio zastaví na zarážce a kód běží v Azure, ne na místním počítači.

11. Najeďte myší `currentTime` proměnné a zobrazit tak hodnotu času.

     ![Zobrazení proměnné v režimu ladění běžící v Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Který se zobrazí čas je čas serveru Azure, který může být v jiném časovém pásmu než místního počítače.

12. Zadejte novou hodnotu `currentTime` proměnných, například "Nyní běží v Azure".

13. Stisknutím klávesy F5 pokračovat v běhu.

     Na stránce o běžící v Azure se zobrazí nová hodnota, kterou jste zadali do proměnné aktualnicas.

     ![Stránka s novou hodnotou](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Vzdálené ladění WebJobs
Tato část ukazuje, jak ladit vzdáleně pomocí projektu a webové aplikace, které vytvoříte v [Začínáme se sadou Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkce uvedené v této části jsou k dispozici pouze v sadě Visual Studio 2013 s aktualizací Update 4 nebo novější.

Vzdálené ladění pracuje pouze s průběžné WebJobs. Na vyžádání a plánované webové úlohy bez podpory ladění.

1. Otevřete webový projekt, který jste vytvořili v [Začínáme se sadou Azure WebJobs SDK][GetStartedWJ].

2. Otevřete v projektu ContosoAdsWebJob *Functions.cs*.

3. [Nastavit zarážku](https://docs.microsoft.com/visualstudio/debugger/) na první příkaz v `GnerateThumbnail` metody.

    ![nastavit zarážku](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. V **Průzkumníka řešení**, klikněte pravým tlačítkem na webový projekt (ne projektu úlohy WebJob) a klikněte na tlačítko **publikovat**.

5. V **profilu** rozevíracího seznamu vyberte stejné, jako jste použili v profilu [Začínáme se sadou Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klikněte na tlačítko **nastavení** kartě a změňte **konfigurace** k **ladění**a potom klikněte na tlačítko **publikovat**.

    Visual Studio nasadí web a projekty webovou úlohu a otevře prohlížeč na adresu URL webové aplikace Azure.

7. V **Průzkumníka serveru**, rozbalte **Azure > App Service > vaší skupiny prostředků > webové aplikace > WebJobs > průběžné**a potom klikněte pravým tlačítkem na **ContosoAdsWebJob**.

8. Klikněte na tlačítko **připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Na domovskou stránku běžící v Azure se automaticky otevře v prohlížeči. Budete muset počkejte 20 sekund a tak Azure nastaví server pro ladění. Toto zpoždění situace nastane pouze při prvním spuštění v režimu ladění ve webové aplikaci 48 hodin. Při zahájení ladění znovu ve stejném období není k dispozici ke zpoždění.

9. Ve webovém prohlížeči, který se otevírá na domovskou stránku Contoso Ads vytvořte nové reklamy.

    Vytvoření ad způsobí, že zprávy fronty má být vytvořen, který se prodlouží webové úlohy a zpracovat. Když sada WebJobs SDK volá funkci zpracovat zprávu fronty, přístupy do kódu zarážku.

10. Pokud ladicí program přeruší na zarážku, můžete zkontrolovat a změnit hodnoty proměnných, zatímco program běží v cloudu. Na následujícím obrázku, ladicí program zobrazí obsah, který byl předán objekt blobInfo `GenerateThumbnail` metody.

     ![blobInfo objektů v ladicím programu](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Stisknutím klávesy F5 pokračovat v běhu.

     `GenerateThumbnail` Metoda dokončí vytváření miniaturu.

12. V prohlížeči indexovou stránku aktualizujte a zobrazit miniaturu.

13. V sadě Visual Studio stiskněte SHIFT + F5 ukončete ladění.

14. V **Průzkumníka serveru**, klikněte pravým tlačítkem na uzel ContosoAdsWebJob a klikněte na tlačítko **zobrazit řídicí panel**.

15. Přihlaste se pomocí přihlašovacích údajů Azure a pak klikněte na název úlohy WebJob přejdete na stránku pro webové úlohy.

     ![Klikněte na tlačítko ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Řídicí panel, který ukazuje `GenerateThumbnail` nedávno funkce.

     (Při příštím kliknutí na **zobrazit řídicí panel**, není nutné se přihlásit a prohlížeči přejde přímo na stránky pro webové úlohy.)

16. Klikněte na název funkce, které chcete zobrazit podrobnosti o spuštění funkce.

     ![Podrobnosti funkce](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Pokud vaše funkce [napsal protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki), mohli byste kliknout na **ToggleOutput** k jejich zobrazení.

## <a name="notes-about-remote-debugging"></a>Poznámky ke vzdálenému ladění

* Nedoporučuje se používat v režimu ladění v produkčním prostředí. Pokud není produkční webovou aplikaci škálovat do několika instancí serverů, ladění bránit ve webovém serveru reagovat na další požadavky. Pokud máte více instancí webového serveru, a po připojení k ladicímu programu, získáte náhodné instance a nemůžete nijak zajistit, že požadavky následné prohlížeče přejít na stejnou instanci. Navíc je obvykle není nasazení ladicího buildu do produkčního prostředí a optimalizace kompilátoru pro buildy vydaných verzí může znemožnit zobrazit, co se děje ve zdrojovém kódu řádek po řádku. Pro řešení potíží s problémy v produkčním prostředí, je nejlepší prostředek aplikace protokoly trasování a webového serveru.
* Vyhněte se dlouho zastaví na zarážkách při vzdáleném ladění. Azure považuje proces, který se zastaví po dobu delší než několik minut, než jako reagovat proces a se vypne.
* Při ladění, server, který odesílá data do sady Visual Studio, které by mohly ovlivnit poplatky za šířku pásma. Informace o sazby za šířku pásma, naleznete v tématu [ceny za Azure](https://azure.microsoft.com/pricing/calculator/).
* Ujistěte se, že `debug` atribut `compilation` prvek *Web.config* souboru má nastavenou na hodnotu true. Je nastavena na hodnotu true ve výchozím nastavení při publikování konfiguraci ladění buildů.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Pokud zjistíte, že ladicí program nebude Krokovat s vnořením kód, který chcete ladit, budete možná muset změnit nastavení pouze můj kód.  Další informace najdete v tématu [určit, jestli se má ladit jenom uživatelský kód, pomocí funkce pouze můj kód v sadě Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Časovač spustí na serveru, když povolíte funkci vzdáleného ladění, a po 48 hodinách se tato funkce automaticky vypne. Tento limit 48 hodin se provádí z důvodů zabezpečení a výkonu. Můžete snadno zapnout funkci zpět jako tolikrát, kolikrát chcete. Doporučujeme, abyste v něm zakázán, když se aktivně ladění.
* Můžete ručně připojit ladicí program k libovolnému procesu, ne jenom webové aplikace procesu (w3wp.exe). Další informace o tom, jak použít režim ladění v sadě Visual Studio najdete v tématu [ladění v sadě Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Přehled diagnostických protokolů
Aplikace ASP.NET, na kterém běží ve webové aplikaci Azure můžete vytvořit následující typy protokolů:

* **Protokoly trasování aplikací**<br/>
  Aplikace vytvoří těchto protokolů voláním metod [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) třídy.
* **Protokoly webového serveru**<br/>
  Webový server vytvoří položku protokolu pro každý požadavek HTTP do webové aplikace.
* **Podrobné chybové zprávy protokoly**<br/>
  Webový server vytvoří stránku HTML s některé další informace o neúspěšných požadavků HTTP (počet požadavků, jejichž výsledkem je stavový kód 400 nebo vyšší).
* **Protokoly neúspěšných požadavků trasování**<br/>
  Webový server vytvoří soubor XML s informacemi o podrobné trasování pro chybné žádosti protokolu HTTP. Webový server také poskytuje soubor XSL do formátu XML v prohlížeči.

Protokolování ovlivňuje výkon webové aplikace, takže Azure vám dává možnost k povolení nebo zakázání každého typu protokolu podle potřeby. Pro protokoly aplikací můžete určit, že by měly být zapsány pouze protokoly nad určitou úroveň závažnosti. Když vytvoříte novou webovou aplikaci, ve výchozím nastavení všechny protokolování je zakázaná.

Protokoly se zapisují do souborů v *LogFiles* složku v systému souborů webové aplikace a je přístupná přes protokol FTP. Protokoly webového serveru a protokoly aplikace lze také zapsat do účtu služby Azure Storage. Můžete zachovat větší objem protokolů v účtu úložiště, než je možné v systému souborů. Jste omezeni na maximálně 100 MB. protokolů při použití systému souborů. (Soubor systémové protokoly jsou jen pro krátkodobé ukládání. Azure odstraní staré soubory protokolů, aby uvolnil prostor pro nové značky po dosažení limitu).  

## <a name="apptracelogs"></a>Vytvářet a zobrazovat protokoly trasování aplikace
V této části provedete následující úlohy:

* Přidání příkazů trasování do webového projektu, který jste vytvořili v [Začínáme s Azure a ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Zobrazte protokoly po spusťte projekt lokálně.
* Zobrazení protokolů vygenerovaný aplikací spuštěných v Azure.

Informace o tom, jak vytvořit aplikaci přihlásí WebJobs, najdete v části [jak pracovat s úložišti front Azure pomocí sady WebJobs SDK – jak zapisovat protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki). Následující pokyny pro prohlížení protokolů a řízení, jak jsou uloženy v Azure platí také pro aplikace protokoly vytvořené metodami WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Přidání příkazů trasování do aplikace
1. Otevřít *Controllers\HomeController.cs*a nahraďte `Index`, `About`, a `Contact` metody následujícím kódem, chcete-li přidat `Trace` příkazy a `using` příkaz pro `System.Diagnostics`:

```c#
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

2. Přidat `using System.Diagnostics;` příkaz do horní části souboru.

### <a name="view-the-tracing-output-locally"></a>Zobrazit výstup trasování místně
1. Stisknutím klávesy F5 spusťte aplikaci v režimu ladění.

    Naslouchací proces trasování výchozí zapíše veškerý výstup trasování do **výstup** okna, společně s další výstup ladění. Následující obrázek znázorňuje výstup z příkazů trasování, které jste přidali do `Index` metody.

    ![V okně ladění trasování](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Následující kroky ukazují, jak zobrazit výstup trasování na webové stránce, bez kompilace v režimu ladění.
2. Otevřete soubor Web.config aplikace (je umístěný ve složce projektu) a přidejte `<system.diagnostics>` prvek na konec souboru, těsně před uzavírací `</configuration>` element:

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

`WebPageTraceListener` Zobrazují tak, že přejdete do výstupu trasování `/trace.axd`.
3. Přidat <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trasování – element</a> pod `<system.web>` v souboru Web.config, jako v následujícím příkladu:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Stiskněte klávesy CTRL+F5 a spusťte aplikaci.
5. V panelu Adresa v okně prohlížeče přidejte *trace.axd* adresy URL a potom stiskněte klávesu Enter (adresa URL je podobný http://localhost:53370/trace.axd).
6. Na **trasování aplikace** klikněte na **zobrazit podrobnosti o** na prvním řádku (ne BrowserLink řádek).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **Podrobnosti o žádosti** se zobrazí stránka a v **informace o trasování** části uvidíte výstup trasování příkazy, které jste přidali do `Index` metoda.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Ve výchozím nastavení `trace.axd` je k dispozici pouze místně. Pokud byste chtěli ji dejte k dispozici ze vzdálené webové aplikace, můžete přidat `localOnly="false"` k `trace` prvek *Web.config* souboru, jak je znázorněno v následujícím příkladu:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Povolení však `trace.axd` v produkční webové aplikace se nedoporučuje z bezpečnostních důvodů. V následujících částech zobrazí se vám snadný způsob, jak číst protokoly trasování ve službě Azure web app.

### <a name="view-the-tracing-output-in-azure"></a>Zobrazit výstup trasování v Azure
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na webový projekt a klikněte na tlačítko **publikovat**.
2. V **Publikovat Web** dialogové okno, klikněte na tlačítko **publikovat**.

    Po aktualizaci sady Visual Studio, otevře se okno prohlížeče na domovskou stránku (za předpokladu, že nepovedlo vymazat **cílovou adresu URL** na **připojení** kartu).
3. V **Průzkumníka serveru**, klikněte pravým tlačítkem na svou webovou aplikaci a vyberte **zobrazit datový proud protokolů**.

    ![Zobrazit datový proud protokolů v kontextové nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Výstup** okno zobrazuje, že jsou připojené ke službě streamování protokolů a přidá nový řádek oznámení každou minutu, které přejde bez protokolu zobrazíte.

    ![Zobrazit datový proud protokolů v kontextové nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. V okně prohlížeče, který zobrazuje domovskou stránku aplikace, klikněte na tlačítko **kontakt**.

    Během několika sekund, jste přidali do trasování výstupu z úroveň chyb `Contact` metoda se zobrazí v **výstup** okna.

    ![Trasování chyb v okně výstupu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio se zobrazují se jenom úroveň chyby trasování vzhledem k tomu, který je ve výchozím nastavení při povolení protokolu sledování služby. Když vytvoříte novou webovou aplikaci, veškeré protokolování je zakázána ve výchozím nastavení, jako když jste otevřeli stránku nastavení dříve jste:

    ![Odhlášení aplikace](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Nicméně, když vyberete **zobrazit datový proud protokolů**, Visual Studio automaticky změnila **Logging(File System) aplikace** k **chyba**, což znamená, že protokoly úroveň chyb hlášeny. Chcete-li zobrazit všechny vaše protokoly trasování, můžete změnit toto nastavení **Verbose**. Když vyberete úroveň závažnosti, která je nižší než chyba, jsou rovněž uvedeny všechny protokoly pro vyšší úrovně závažnosti. Takže když vyberete verbose, zobrazí také informace, upozornění a protokoly chyb.  

1. V **Průzkumníka serveru**, klikněte pravým tlačítkem na webovou aplikaci a pak klikněte na tlačítko **nastavení zobrazení** jako jste to udělali dříve.
2. Změna **protokolování aplikace (systém souborů)** k **Verbose**a potom klikněte na tlačítko **Uložit**.

    ![Nastavení úrovně trasování na podrobné](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. V okně prohlížeče, který se teď zobrazuje vaše **kontakt** klikněte na **Domů**, klikněte na **o**a potom klikněte na tlačítko **kontakt**.

    Během několika sekund **výstup** okno zobrazuje všechny výstupu trasování.

    ![Výstup podrobné trasování](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    V této části povolená a zakázat protokolování pomocí nastavení Azure webové aplikace. Můžete také povolit nebo zakázat naslouchacích procesů trasování úpravou souboru Web.config. Ale upravte soubor Web.config způsobí, že doména aplikace recyklovat, zatímco povolení protokolování prostřednictvím konfigurace webové aplikace neprovádí, který. Pokud problém trvá dlouhou dobu reprodukovat, nebo je přerušované, recyklace domény aplikace může být "Automatická oprava" a vynutit můžete počkat, až se stane znovu. Povolení diagnostiky v Azure umožňuje spustit zachytávání informací o chybách okamžitě bez recyklace domény aplikace.

### <a name="output-window-features"></a>Funkce okna výstup
**Protokolů Microsoft Azure** karty **výstup** okno má několik tlačítek a textové pole:

![Protokoly kartu tlačítka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Tyto provádět následující funkce:

* Zrušte **výstup** okna.
* Povolí nebo zakáže zalamování řádků.
* Spuštění nebo zastavení sledování protokolů.
* Určete, které protokoly se mají monitorovat.
* Stažení protokolů.
* Filtrovat protokoly na základě vyhledávacího řetězce nebo regulárního výrazu.
* Zavřít **výstup** okna.

Pokud zadáte hledaný řetězec nebo regulární výraz, Visual Studio filtruje informace o protokolování u klienta. To znamená, že zadáte kritéria po protokoly jsou zobrazeny v **výstup** okno a můžete změnit kritéria filtrování bez nutnosti znovu vygenerovat protokolech.

## <a name="webserverlogs"></a>Zobrazení protokolů webového serveru
Protokoly webového serveru zaznamenejte všechny aktivitu protokolu HTTP pro webovou aplikaci. Chcete-li je zobrazit **výstup** okna, musíte povolit pro webovou aplikaci a říct sadě Visual Studio, že chcete je sledovat.

1. V **konfigurace webové aplikace Azure** kartu, kterou jste otevřeli z **Průzkumníka serveru**, změňte protokolování webového serveru na **na**a potom klikněte na tlačítko **Uložit**.

    ![Povolení protokolování webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. V **výstup** okna, klikněte na tlačítko **zadejte, které protokoly Microsoft Azure k monitorování** tlačítko.

    ![Určení pro monitorování protokolů Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. V **možnosti protokolování Microsoft Azure** dialogu **webovou protokolech serveru**a potom klikněte na tlačítko **OK**.

    ![Monitorování protokolů webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. V okně prohlížeče, který zobrazuje webovou aplikaci, klikněte na tlačítko **Domů**, pak klikněte na tlačítko **o**a potom klikněte na tlačítko **kontakt**.

    Protokoly aplikací obvykle zobrazí první, za nímž následuje protokolů webového serveru. Budete muset chvíli počkejte pro protokoly objeví.

    ![V okně výstupu protokolů webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Ve výchozím nastavení když poprvé povolíte protokolů webového serveru pomocí sady Visual Studio, Azure zapisuje protokoly do systému souborů. Jako alternativu můžete zadat webový server, které protokoly by měly být zapsány do kontejneru objektů blob v účtu úložiště na webu Azure portal.

Pokud jste pomocí portálu pro povolení webového serveru k účtu služby Azure storage a potom zakázat protokolování v sadě Visual Studio, když je znovu povolit protokolování v sadě Visual Studio nastavení svého účtu úložiště obnoveny.

## <a name="detailederrorlogs"></a>Zobrazit protokoly Podrobná chybová zpráva
Protokoly podrobné informace o chybě poskytují další informace o požadavcích HTTP, jejichž výsledkem kódy odpovědí chyba (400 nebo novější). Chcete-li je zobrazit **výstup** okna, je nutné povolit pro webovou aplikaci a že Visual Studio se, že chcete monitorovat.

1. V **konfigurace webové aplikace Azure** kartu, kterou jste otevřeli z **Průzkumníka serveru**, změňte **podrobné chybové zprávy** k **na**a pak Klikněte na tlačítko **Uložit**.

    ![Povolit podrobné chybové zprávy](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. V **výstup** okna, klikněte na tlačítko **zadejte, které protokoly Microsoft Azure k monitorování** tlačítko.

3. V **možnosti protokolování Microsoft Azure** dialogové okno, klikněte na tlačítko **všechny protokoly**a potom klikněte na tlačítko **OK**.

    ![Monitorování všech protokolů](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. V panelu Adresa v okně prohlížeče přidejte znak navíc k adrese URL můžete způsobit chybu 404 (například `http://localhost:53370/Home/Contactx`), a stiskněte klávesu Enter.

    Po několika sekundách se zobrazí podrobné informace o chybě protokolu v sadě Visual Studio **výstup** okna.

    ![Podrobné informace o chybě protokolu – okno výstup](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    CTRL + kliknutím na odkaz zobrazíte výstup protokolu ve formátu v prohlížeči:

    ![Podrobné informace o chybě protokolu – okno prohlížeče](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Stáhněte si soubor protokolu systému
Všechny protokoly, které můžete monitorovat v **výstup** okno lze také stáhnout jako *ZIP* souboru.

1. V **výstup** okna, klikněte na tlačítko **stáhnout datový proud protokolů**.

    ![Protokoly kartu tlačítka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Průzkumník otevře vaše *stáhne* složka s stažený soubor vybrali.

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahovat *ZIP* soubor a uvidíte následující strukturu složek:

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Protokoly trasování aplikace jsou v *.txt* soubory *LogFiles\Application* složky.
   * Protokoly webového serveru jsou v *.log* soubory *LogFiles\http\RawLogs* složky. Můžete použít nástroj, jako [analyzátoru protokolů](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) k zobrazení a manipulaci s těchto souborů.
   * Podrobné chybové zprávy protokolů jsou v *.html* soubory *LogFiles\DetailedErrors* složky.

    ( *Nasazení* složka je pro soubory vytvořené pomocí správy zdrojového kódu publikování; není třeba nic, co souvisí s publikováním sady Visual Studio. Tím *Git* složka je pro trasování týkající se správy zdrojových kódů publikování a protokol služby streamování souborů.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

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
## <a name="failedrequestlogs"></a>Zobrazit protokoly trasování chybných požadavků
Protokoly trasování neúspěšných požadavků jsou užitečné, když je potřeba pochopit, jak služba IIS zpracovává požadavek HTTP ve scénářích, jako je například adresa URL přepsání nebo ověřování problémy podrobnosti.

Webové aplikace Azure pomocí stejné funkce trasování chybných požadavků, které bylo dostupné se službou IIS 7.0 a novější. Nemáte přístup k nastavení služby IIS, které se budou protokolovat které chyby, ale konfigurace. Když povolíte trasování chybných požadavků, jsou zachyceny všechny chyby.

Trasování chybných požadavků můžete povolit pomocí sady Visual Studio, ale nelze je zobrazit v sadě Visual Studio. Tyto protokoly jsou soubory formátu XML. Streamování protokolů služby sleduje pouze soubory, které se považují za čitelný v režimu prostého textu: *.txt*, *.html*, a *.log* soubory.

Protokoly trasování chybných požadavků můžete zobrazit v prohlížeči přímo přes protokol FTP nebo místně po stažení do místního počítače pomocí nástroje serveru FTP. V této části budete zobrazíte je v prohlížeči přímo.

1. V **konfigurace** karty **webové aplikace Azure** okno, které jste otevřeli z **Průzkumníka serveru**, změnit **trasování neúspěšných žádostí** do **Na**a potom klikněte na tlačítko **Uložit**.

    ![Povolit trasování chybných požadavků](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. V panelu Adresa v okně prohlížeče, který ukazuje webové aplikace přidat další znak na adresu URL a klikněte na Enter způsobí chybu 404.

    To způsobí, že protokol trasování chybných požadavků mají být vytvořeny, a následující kroky ukazují, jak zobrazit nebo stáhnout.

3. V sadě Visual Studio v **konfigurace** karty **webové aplikace Azure** okna, klikněte na tlačítko **otevřít na portálu správy**.

4. V [webu Azure portal](https://portal.azure.com) **nastavení** stránce pro vaši webovou aplikaci, klikněte na tlačítko **přihlašovací údaje pro nasazení**a poté zadejte nové uživatelské jméno a heslo.

    ![Nové uživatelské jméno pro FTP a heslo](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Když se přihlásíte, budete muset použít úplné uživatelské jméno s předponou je název webové aplikace. Například pokud zadejte "myid" jako uživatelské jméno a webu je název "myexample", přihlášení jako "myexample\myid".
    >

5. V novém okně prohlížeče, přejděte na adresu URL, která se zobrazí v části **název hostitele FTP** nebo **název hostitele FTPS** v **přehled** stránky pro webové aplikace.

6. Přihlaste se pomocí přihlašovacích údajů FTP, které jste vytvořili dříve (včetně webových předponu názvu aplikace pro uživatelské jméno).

    Prohlížeč zobrazí kořenové složce webové aplikace.

7. Otevřít *LogFiles* složky.

    ![Otevřete složku LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otevřete složku s názvem W3SVC plus číselná hodnota.

    ![Otevřete složku W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Složka obsahuje soubory XML pro všechny chyby, které byly zaprotokolovány po povolení trasování chybných požadavků a soubor XSL, který prohlížeč můžete použít k formátování kódu XML.

    ![Složka W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klikněte na soubor XML pro neúspěšné žádosti, které chcete zobrazit informace o trasování pro.

    Následující obrázek ukazuje část trasovací informace pro ukázkové chybu.

    ![Trasování neúspěšných žádostí v prohlížeči](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Další kroky
Už víte, jak Visual Studio umožňuje snadno zobrazit protokoly vytvořené webové aplikace Azure. Následující části obsahují odkazy na další zdroje informací související témata:

* Řešení potíží s Azure web app
* Ladění v sadě Visual Studio
* Vzdálené ladění v Azure
* Trasování v aplikacích ASP.NET
* Analýza protokolů webového serveru
* Analýza protokolů trasování chybných požadavků
* Ladění cloudové služby

### <a name="azure-web-app-troubleshooting"></a>Řešení potíží s Azure web app
Další informace o řešení potíží s webovými aplikacemi ve službě Azure App Service naleznete na následujících odkazech:

* [Monitorování webových aplikací](web-sites-monitor.md)
* [Zkoumání nevracení paměti ve službě Azure Web Apps pomocí sady Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Příspěvek na blogu Microsoft ALM funkcemi sady Visual Studio pro analýzu spravovaných problémy s pamětí.
* [Online nástroje pro Azure web apps byste měli vědět o](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Příspěvek na blogu od Amitu Apple.

Nápovědu k řešení problémů s konkrétní dotaz otevřete vlákno ve jednu z následujících fór služby:

* [Fórum Azure na webu ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Fórum Azure na webu MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Ladění v sadě Visual Studio
Další informace o tom, jak použít režim ladění v sadě Visual Studio najdete v tématu [ladění v sadě Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) a [tipy k ladění pomocí Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Vzdálené ladění v Azure
Další informace o vzdáleném ladění pro Azure web apps a WebJobs najdete v následujících zdrojích:

* [Úvod do vzdálené ladění webových aplikací Azure App Service Web Apps](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Úvod do vzdáleného ladění v Azure App Service Web Apps část 2 – uvnitř vzdálené ladění](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Úvod do vzdáleného ladění na Azure App Service Web Apps – část 3 – prostředí s více instancemi a GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs ladění (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Pokud vaše webová aplikace používá back endové webové rozhraní API Azure nebo Mobile Services a je třeba ladit, který najdete v tématu [ladění back-end .NET v sadě Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Trasování v aplikacích ASP.NET
Na Internetu nejsou k dispozici žádné důkladné a aktuální přehled trasování rozhraní ASP.NET. Nejlepší, co můžete dělat je začít s původní úvodní materiály, které jsou napsané pro webové formuláře protože MVC nebyla ještě neexistuje a doplněk, který se novější blogu příspěvky, které se zaměřují na konkrétní problémy. Některé dobré místa, kde začít jsou následující prostředky:

* [Monitorování a Telemetrie (vytváření skutečných cloudových aplikací s Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Elektronická kniha kapitoly s doporučeními pro trasování v Azure cloudové aplikace.
* [Trasování rozhraní ASP.NET](https://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Starý, ale stále vhodným místem k zadání základní informace o subjektu.
* [Naslouchací procesy trasování](https://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informace o naslouchacích procesů trasování, ale nebude zmiňovat [WebPageTraceListener](https://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Návod: Integrování trasování System.Diagnostics trasování rozhraní ASP.NET](https://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Tento článek je také starý, ale zahrnuje některé další informace, které úvodní článek nepopisuje.
* [Trasování v zobrazení syntaxe Razor rozhraní ASP.NET MVC](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Kromě trasování v zobrazení syntaxe Razor příspěvek také vysvětluje, jak vytvořit filtr chyba protokolovat všechny neošetřené výjimky v aplikaci MVC. Informace o tom, jak protokolovat všechny neošetřené výjimky v aplikaci webových formulářů, podívejte se na příklad Global.asax v [kompletní příklad pro obslužné rutiny chyb](https://msdn.microsoft.com/library/bb397417.aspx) na webové stránce MSDN. V MVC nebo webového formuláře Pokud chcete protokolovat určité výjimky, ale nechat výchozí rozhraní zpracování se projeví, můžete zachytit a znovu vyvolat jako v následujícím příkladu:

``` c#
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

* [Protokolování z rozhraní příkazového řádku Azure (plus balíčku Glimpse!) streamování trasování diagnostiky](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Použití příkazového řádku provést co tento kurz ukazuje, jak provést v sadě Visual Studio. [Balíčku glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) je nástroj pro ladění aplikací ASP.NET.
* [Použití webových aplikací, protokolování a Diagnostika – s David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) a [protokoly z webové aplikace – David Ebbo streamování](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videa Scott Hanselman a David Ebbo.

Pro protokolování chyb, je použití rozšiřovatelnou platformu pro protokolování open source, jako alternativu k psaní kódu trasování [ELMAH](http://nuget.org/packages/elmah/). Další informace najdete v tématu [Scott Hanselman příspěvky o ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Navíc není nutné používat technologie ASP.NET nebo `System.Diagnostics` trasování streamování získat protokoly z Azure. Streamování protokolů služby Azure webové aplikace streamuje žádné *.txt*, *.html*, nebo *.log* soubor, který najde v *LogFiles* složky. Proto můžete vytvořit vlastní protokolování systému, která zapisuje do systému souborů webové aplikace a soubor automaticky streamování a stáhnout. Vše, co musíte udělat je napsat kód aplikace, která vytvoří soubory v *d:\home\logfiles* složky.

### <a name="analyzing-web-server-logs"></a>Analýza protokolů webového serveru
Další informace o analýze protokolů webového serveru najdete v následujících zdrojích:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Nástroj pro zobrazení dat v protokolů webového serveru (*.log* soubory).
* [Řešení potíží s problémy s výkonem služby IIS nebo chyby aplikace pomocí LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Úvod do nástroje analyzátoru protokolů, který můžete použít k analýze protokolů webového serveru.
* [Příspěvky do blogu podle Robert McMurray pomocí LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Stavový kód HTTP ve službě IIS 7.0, IIS 7.5 a IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analýza protokolů trasování chybných požadavků
Zahrnuje webu Microsoft TechNet [pomocí trasování neúspěšných žádostí](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) oddíl, což může být užitečné pro pochopení způsobu, jak používat tyto protokoly. Nicméně tato dokumentace je zaměřen především na Konfigurace trasování chybných požadavků ve službě IIS, které nelze použít ve službě Azure Web Apps.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
