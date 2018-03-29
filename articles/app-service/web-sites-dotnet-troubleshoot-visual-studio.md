---
title: Řešení potíží s webovou aplikaci v Azure App Service pomocí sady Visual Studio
description: Zjistěte, jak řešení webové aplikace Azure pomocí vzdálené ladění, trasování a protokolování nástroje, které jsou součástí sady Visual Studio 2013.
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
ms.openlocfilehash: 7973f4311095b7c87ccd2394b048ec92c50f32a9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Řešení potíží s webovou aplikaci v Azure App Service pomocí sady Visual Studio
## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak pomocí nástrojů Visual Studio pro pomáhají ladit webové aplikace ve [služby App Service](http://go.microsoft.com/fwlink/?LinkId=529714), protože běží na [režimu ladění](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) vzdáleně nebo zobrazením protokoly aplikací a protokoly webového serveru.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Naučíte se:

* Funkcích správy, které Azure webové aplikace jsou k dispozici v sadě Visual Studio.
* Jak provádějte rychlé změny ve vzdálené webové aplikaci pomocí sady Visual Studio vzdálené zobrazení.
* Jak spustit režim ladění vzdáleně při projektu běží v Azure, pro webovou aplikaci a pro webovou úlohu.
* Postup vytvoření aplikace protokoly trasování a zobrazit při aplikace je jejich vytváření.
* Postup zobrazení protokolů webového serveru, včetně podrobné chybové zprávy a trasování neúspěšných žádostí.
* Postup odesílání diagnostických protokolů do služby Azure Storage účtu a zobrazit je.

Pokud máte Visual Studio Ultimate, můžete také použít [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) pro ladění. V tomto kurzu nepopisuje IntelliTrace.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu funguje s vývojového prostředí, webový projekt a Azure webové aplikace, které jste nastavili v [Začínáme s Azure a ASP.NET][GetStarted]. Pro webové části, musíte aplikaci, která vytvoříte v [Začínáme s Azure WebJobs SDK][GetStartedWJ].

Ukázky kódu, které jsou uvedené v tomto kurzu jsou pro MVC C# webovou aplikaci, ale řešení problémů s postupy jsou stejné pro aplikace Visual Basic a webových formulářů.

Kurz předpokládá, že používáte Visual Studio 2017. 

Protokoly streamování funkce funguje pouze pro aplikace, které cílí na rozhraní .NET Framework 4 nebo novější.

## <a name="sitemanagement"></a>Správa a konfigurace webové aplikace
Poskytuje přístup k podmnožině funkce správy webové aplikace a nastavení konfigurace, které jsou k dispozici v sadě Visual Studio [portál Azure](http://go.microsoft.com/fwlink/?LinkId=529715). V této části se zobrazí, co je dostupné pomocí **Průzkumníka serveru**. Vyzkoušet nejnovější funkce integrace se službou Azure najdete **Průzkumník cloudu** také. Můžete otevřít ze systému windows **zobrazení** nabídky.

1. Pokud už nejste přihlášení k Azure v sadě Visual Studio, klikněte pravým tlačítkem na **Azure** a vyberte možnost připojit k **předplatnému Microsoft Azure** v **Průzkumníka serveru**.

    Alternativou je nainstalovat certifikát správy, který umožňuje přístup k vašemu účtu. Pokud se rozhodnete nainstalovat certifikát, klikněte pravým tlačítkem **Azure** uzlu v **Průzkumníka serveru**a potom vyberte **spravovat a odběry filtru** v místní nabídce. V **spravovat předplatná Microsoft Azure** dialogové okno, klikněte **certifikáty** a pak klikněte **Import**. Postupujte podle pokynů ke stažení a následným importem souboru předplatné (také nazývané *.publishsettings* souboru) pro účet Azure.

   > [!NOTE]
   > Pokud chcete stáhnout soubor předplatné, uložit do složky mimo adresáře zdrojový kód (například ve složce soubory ke stažení) a pak odstraňte po dokončení importu. Uživatel se zlými úmysly, který získá přístup k souboru předplatného můžete upravit, vytvořit a odstranit služeb Azure.
   >
   >

    Další informace o připojení k prostředkům Azure ze sady Visual Studio najdete v tématu [Správa účtů, odběry a správu role](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. V **Průzkumníka serveru**, rozbalte položku **Azure** a rozbalte **služby App Service**.
3. Rozbalte skupinu prostředků, která zahrnuje webovou aplikaci, kterou jste vytvořili v [vytvořit webové aplikace ASP.NET v Azure][app-service-web-get-started-dotnet.md] a pak klikněte pravým tlačítkem na uzel webového aplikace a klikněte na **nastavení zobrazení**.

    ![Nastavení zobrazení v Průzkumníku serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Webové aplikace Azure** se zobrazí karta a uvidíte existuje webové aplikace správu a konfiguraci úlohy, které jsou k dispozici v sadě Visual Studio.

    ![Azure okna webové aplikace](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    V tomto kurzu budete používat k protokolování a trasování rozevírací seznamy. Budete používat vzdálené ladění ale budete používat jinou metodu povolit.

    Informace o nastavení aplikace a připojovacích řetězců polí v tomto okně najdete v tématu [Azure Web Apps: fungování řetězců aplikace a připojovacích řetězců](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Pokud chcete provést úlohu správy webové aplikace, která není možné v tomto okně, klikněte na tlačítko **otevřít na portálu pro správu** otevřete okno prohlížeče na portál Azure.

## <a name="remoteview"></a>Přístup k webové aplikaci souborům v Průzkumníku serveru
Obvykle nasazení webového projektu s `customErrors` příznak v souboru Web.config nastaven `On` nebo `RemoteOnly`, tzn., neobdržíte užitečné chybová zpráva, pokud něco pokazí. Pro mnoho chyb můžete získat je jako jeden z následujících těm, které jsou na stránce:

**Chyba serveru v aplikaci '/':**

![Neužitečné chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Došlo k chybě:**

![Neužitečné chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Stránku nelze zobrazit stránku**

![Neužitečné chybová stránka](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Často je nejjednodušší způsob, jak najít příčinu chyby povolit podrobné chybové zprávy, které první předchozí snímky obrazovky vysvětluje, jak to provést. Který vyžaduje změnu v nasazeném souboru Web.config. Může upravit *Web.config* v projektu soubor a znovu nasaďte projekt nebo vytvořte [transformaci Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) a nasadit sestavení ladicí verze, ale existuje rychlejší způsob: v **Průzkumníku řešení** , můžete přímo zobrazit a upravit soubory ve vzdálené webové aplikaci pomocí *vzdálené zobrazení* funkce.

1. V **Průzkumníka serveru**, rozbalte položku **Azure**, rozbalte položku **služby App Service**, rozbalte skupinu prostředků, umístěné ve vaší webové aplikace a pak rozbalte uzel vaší webové aplikace.

    Zobrazí uzly, které umožňují přístup k obsahu souborů a souborů protokolů webové aplikace.
2. Rozbalte **soubory** uzel a klikněte dvakrát *Web.config* souboru.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio otevře soubor Web.config ze vzdálené webové aplikace a ukazuje [vzdálené] vedle názvu souboru v záhlaví.
3. Přidejte následující řádek na `system.web` element:

    `<customErrors mode="Off"></customErrors>`

    ![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aktualizujte stránku prohlížeče, který se zobrazuje neužitečné chybovou zprávu, a teď se podrobná chybová zpráva, jako je například v následujícím příkladu:

    ![Podrobná chybová zpráva](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Chyba zobrazí byl vytvořen tak, že přidáte řádku zobrazené červeně k *Views\Home\Index.cshtml*.)

Úprava souboru Web.config je pouze jeden příklad scénáře, ve kterých zkontrolujte možnost číst a upravovat soubory na vaše webové aplikace Azure při odstraňování problémů.

## <a name="remotedebug"></a>Vzdálené ladění webových aplikací
Pokud Podrobná chybová zpráva neposkytuje dostatek informací, a nelze znovu vytvořit chyba místně, jiný způsob řešení je spustit v režimu ladění vzdáleně. Můžete nastavit zarážky, zpracovávají paměti přímo, krok prostřednictvím kódu a i změňte cestu k kódu.

Vzdálené ladění nefunguje, pokud je v edice Express sady Visual Studio.

V této části ukazuje, jak ladit vzdáleně pomocí projektu vytvoříte v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet.md).

1. Otevřete webový projekt, který jste vytvořili v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet.md).

2. Otevřete *Controllers\HomeController.cs*.

3. Odstranit `About()` metoda a vložte následující kód na příslušné místo.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```
4. [Nastavit zarážky](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) na `ViewBag.Message` řádku.

5. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a klikněte na tlačítko **publikovat**.

6. V **profil** rozevíracího seznamu, vyberte stejný profil, že jste použili v [vytvoření webové aplikace ASP.NET v Azure](app-service-web-get-started-dotnet.md). Potom klikněte na nastavení.

7. V **publikovat** dialogové okno, klikněte na tlačítko **nastavení** a pak změňte **konfigurace** k **ladění**a pak klikněte na tlačítko  **Uložit**.

    ![Publikování v režimu ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Klikněte na **Publikovat**. Po dokončení nasazení a na adresu URL Azure vaší webové aplikace se zobrazí prohlížeč zavřete prohlížeč.

9. V **Průzkumníka serveru**, klikněte pravým tlačítkem na vaší webové aplikace a pak klikněte na tlačítko **připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    V prohlížeči se automaticky otevře na domovskou stránku běžící v Azure. Můžete chtít počkejte 20 sekund nebo tak Azure slouží k nastavení serveru pro ladění. Tato prodleva situace nastane pouze při prvním spuštění v režimu ladění na webové aplikace za 48 hodin. Při spuštění ladění znovu ve stejném období, není k dispozici ke zpoždění.

    > [!NOTE] 
    > Pokud máte jakékoli potíže při spuštění ladicího programu, zkuste to pomocí **Průzkumník cloudu** místo **Průzkumníka serveru**.
    >

10. Klikněte na tlačítko **o** v nabídce.

     Visual Studio zastaví na zarážce a kód běží v Azure, není v místním počítači.

11. Najeďte myší `currentTime` proměnné a zobrazit tak hodnotu času.

     ![Zobrazení proměnné v režimu ladění běžící v Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Zobrazí čas je čas Azure serveru, který může být v jiném časovém pásmu než místního počítače.

12. Zadejte novou hodnotu `currentTime` proměnné, jako je například "Teď spuštění v Azure".

13. Stisknutím klávesy F5 dál běžet.

     Na stránce o spuštění v Azure se zobrazí nová hodnota, kterou jste zadali do proměnné aktualnicas.

     ![O stránce s novou hodnotou](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Vzdálené ladění webové úlohy
V této části ukazuje, jak ladit vzdáleně pomocí projektu a webové aplikace, které vytvoříte v [Začínáme s Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkce uvedené v této části jsou k dispozici pouze v sadě Visual Studio 2013 s aktualizací 4 nebo novější.

Vzdálené ladění pracuje pouze s nepřetržité webové úlohy. Naplánované i na vyžádání webové úlohy se nepodporuje ladění.

1. Otevřete webový projekt, který jste vytvořili v [Začínáme s Azure WebJobs SDK][GetStartedWJ].

2. Otevřete v projektu ContosoAdsWebJob *Functions.cs*.

3. [Nastavit zarážky](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) na prvním příkazem v `GnerateThumbnail` metoda.

    ![Sada zarážek](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt webové (nikoli projekt webové úlohy) a klikněte na tlačítko **publikovat**.

5. V **profil** rozevíracího seznamu, vyberte stejný profil, že jste použili v [Začínáme s Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klikněte na tlačítko **nastavení** kartě a změňte **konfigurace** k **ladění**a potom klikněte na **publikovat**.

    Visual Studio nasadí web a webové úlohy projekty a otevře prohlížeč na adresu URL Azure vaší webové aplikace.

7. V **Průzkumníka serveru**, rozbalte položku **Azure > aplikační služby > vaší skupiny prostředků > vaší webové aplikace > webové úlohy > souvislý**a potom klikněte pravým tlačítkem na **ContosoAdsWebJob**.

8. Klikněte na tlačítko **připojit ladicí program**.

    ![Připojit ladicí program](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    V prohlížeči se automaticky otevře na domovskou stránku běžící v Azure. Můžete chtít počkejte 20 sekund nebo tak Azure slouží k nastavení serveru pro ladění. Tato prodleva situace nastane pouze při prvním spuštění v režimu ladění na webové aplikace za 48 hodin. Při spuštění ladění znovu ve stejném období, není k dispozici ke zpoždění.

9. Ve webovém prohlížeči, který se otevírá na domovskou stránku Contoso Ads vytvořte nové reklamy.

    Vytváření ad způsobí, že zprávu fronty bude vytvořen, která jsou zachyceny pomocí vytvářené webové úlohy a zpracovat. Když WebJobs SDK volá funkci pro zpracování zprávy ve frontě, přístupů kódu vaší zarážce.

10. Při ladicího programu dělí na vaší zarážce, můžete zkontrolovat a změnit hodnoty proměnné cloudu spuštěného programu. Na následujícím obrázku, ladicí program zobrazí obsah blobInfo objekt, který byl předán `GenerateThumbnail` metoda.

     ![objekt blobInfo v ladicím programu](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Stisknutím klávesy F5 dál běžet.

     `GenerateThumbnail` Metoda dokončí vytváření miniaturu.

12. V prohlížeči indexovou stránku aktualizujte a zobrazí miniaturu.

13. V sadě Visual Studio stiskněte klávesu SHIFT + F5 ukončete ladění.

14. V **Průzkumníka serveru**, klikněte pravým tlačítkem na uzel ContosoAdsWebJob a klikněte na **zobrazení řídicího panelu**.

15. Přihlaste se pomocí přihlašovacích údajů Azure a potom klikněte na název webové úlohy přejdete na stránku pro vaše webová úloha.

     ![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Řídicího panelu ukazuje, že `GenerateThumbnail` funkce nedávno.

     (Při příštím kliknutí na tlačítko **zobrazení řídicího panelu**, nemusíte se přihlásit a prohlížeč přejde přímo na stránku pro vaše webová úloha.)

16. Klikněte na název funkce zobrazíte podrobnosti o spuštění funkce.

     ![Podrobnosti funkce](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Pokud funkce [napsali protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki), uživatel může klepnout **ToggleOutput** k jejich zobrazení.

## <a name="notes-about-remote-debugging"></a>Poznámky o vzdálené ladění

* Spuštění v režimu ladění v produkčním prostředí se nedoporučuje. Pokud vaše produkční webová aplikace není škálovat na více instancí serveru, ladění bránit ve webovém serveru neodpovídá na požadavky ostatních požadavků. Pokud máte několika instancemi webového serveru, pokud připojíte k ladicí program, získat náhodné instance a mít žádný způsob, jak zajistit, že požadavky následné prohlížeče přejděte na stejnou instanci. Navíc obvykle nenasazujete sestavení ladicí verze do produkčního prostředí a optimalizace kompilátoru pro verzi sestavení může znemožnit zobrazit, co se děje řádek po řádku ve zdrojovém kódu. Při řešení problémů produkční, je nejlepší prostředku aplikace protokoly trasování a webového serveru.
* Vyhněte se dlouho se zastavuje na zarážky při vzdáleném ladění. Azure zpracovává proces, který je zastavena po dobu delší než několik minut jako reagovat proces a ukončí se.
* Při ladění, server, který odesílá data na Visual Studio, které by mohly ovlivnit poplatky šířky pásma. Informace o sazbách šířky pásma najdete v tématu [Azure – ceny](https://azure.microsoft.com/pricing/calculator/).
* Ujistěte se, že `debug` atribut `compilation` element v *Web.config* soubor je nastaven na hodnotu true. Je nastaven na hodnotu true ve výchozím nastavení při publikování konfiguraci sestavení ladění.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Pokud zjistíte, že není ladicího programu kroku do kódu, kterou chcete ladit, budete možná muset změnit nastavení pouze můj kód.  Další informace najdete v tématu [omezit zanoříte se pouze můj kód](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* Časovač se spustí na serveru, když povolíte funkci vzdáleného ladění a po 48 hodinách je tato funkce automaticky vypnuta. Tento limit 48 hodin se provádí z důvodů zabezpečení a výkonu. Můžete snadno zapnout funkci zpět jako tolikrát, kolikrát chcete. Doporučujeme ponechat zakázané při nejsou aktivně ladění.
* Ladicí program můžete ručně připojit k libovolnému procesu, ne jenom webové aplikace proces (w3wp.exe). Další informace o tom, jak používat režim ladění v sadě Visual Studio najdete v tématu [ladění v sadě Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Diagnostické protokoly – přehled
Aplikace ASP.NET, která běží ve webové aplikace Azure můžete vytvořit následující typy protokolů:

* **Protokoly trasování aplikací**<br/>
  Aplikace vytvoří tyto protokoly voláním metod [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) třídy.
* **Protokoly webového serveru**<br/>
  Webový server vytvoří položku protokolu pro každý požadavek HTTP do webové aplikace.
* **Protokoly podrobné chybové zprávy**<br/>
  Webový server vytvoří stránku HTML se některé další informace o selhání požadavků HTTP (počet požadavků, jejichž výsledkem stavový kód 400 nebo vyšší).
* **Protokoly trasování požadavku se nezdařilo**<br/>
  Webový server vytvoří soubor XML s informacemi o podrobného trasování pro chybné žádosti HTTP. Webový server poskytuje také soubor XSL do formátu XML v prohlížeči.

Protokolování ovlivňuje výkon webové aplikace, takže Azure vám dává možnost povolit nebo zakázat každého typu protokolu podle potřeby. Pro protokoly aplikací můžete zadat, že by měly být zapsány pouze protokoly do určité míry závažnosti. Při vytváření nové webové aplikace ve výchozím nastavení všechny protokolování je zakázána.

Protokoly se zapisují do souborů *LogFiles* složku v systému souborů webové aplikace a jsou přístupné přes FTP. Protokoly webového serveru a v protokolu aplikací můžete rovněž zapsány do účtu Azure Storage. Můžete zachovat větší objem protokolů v účtu úložiště, než je možné v systému souborů. Jste omezeny na maximálně 100 megabajtů protokolů při použití systému souborů. (Soubor systémové protokoly jsou pouze pro krátkodobé ukládání. Azure odstraní staré soubory protokolu, aby uvolnil prostor pro nové po dosažení limitu).  

## <a name="apptracelogs"></a>Vytvořit a zobrazit protokoly trasování aplikací
V této části proveďte následující úlohy:

* Přidání příkazů trasování do webového projektu, který jste vytvořili v [Začínáme s Azure a ASP.NET][GetStarted].
* Zobrazte protokoly při spusťte projekt lokálně.
* Zobrazte protokoly generovaná aplikace běžící v Azure.

Informace o tom, jak vytvořit aplikaci přihlásí webové úlohy, najdete v části [jak pracovat s Azure queue storage pomocí WebJobs SDK – jak napsat protokoly](https://github.com/Azure/azure-webjobs-sdk/wiki). Následující pokyny pro prohlížení protokolů a řídí, jak byly uloženy do Azure platí také pro protokoly aplikací, které jsou vytvořené webové úlohy.

### <a name="add-tracing-statements-to-the-application"></a>Přidání příkazů trasování do aplikace
1. Otevřete *Controllers\HomeController.cs*a nahraďte `Index`, `About`, a `Contact` metody následujícím kódem, aby bylo možné přidat `Trace` příkazy a `using` příkaz pro `System.Diagnostics`:

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

    Naslouchací proces výchozí trasování zapíše veškerý výstup trasování do **výstup** okno, společně s další výstup ladění. Následující obrázek znázorňuje výstupu trasovacích příkazů, které jste přidali do `Index` metoda.

    ![Trasování v okně ladění](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Následující kroky ukazují, jak zobrazit výstup trasování na webové stránce, bez kompilace v režimu ladění.
2. Otevřete soubor Web.config aplikace (jeden, umístěný ve složce projektu) a přidejte `<system.diagnostics>` prvek na konec souboru, těsně před uzavírací `</configuration>` element:

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

`WebPageTraceListener` Umožňuje zobrazit výstup trasování procházením `/trace.axd`.
3. Přidat <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trasování element</a> pod `<system.web>` v souboru Web.config, jako je například v následujícím příkladu:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Stiskněte klávesy CTRL+F5 a spusťte aplikaci.
5. Na panelu Adresa v okně prohlížeče přidat *trace.axd* na adresu URL a potom stiskněte klávesu Enter (adresa URL je podobná http://localhost:53370/trace.axd).
6. Na **trasování aplikací** klikněte na tlačítko **zobrazit podrobnosti** na prvním řádku (ne řádek BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **Podrobností žádosti o** se zobrazí stránka a v **informace trasování** uvidíte výstup trasování příkazy, které jste přidali do části `Index` metoda.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Ve výchozím nastavení `trace.axd` je k dispozici pouze místně. Pokud jste chtěli zpřístupnit ze vzdálené webové aplikace, můžete přidat `localOnly="false"` k `trace` element v *Web.config* souboru, jak je znázorněno v následujícím příkladu:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Povolení však `trace.axd` v produkční webové aplikace se nedoporučuje z bezpečnostních důvodů. V následujících částech uvidíte snadný způsob, jak číst protokoly trasování v webové aplikace Azure.

### <a name="view-the-tracing-output-in-azure"></a>Zobrazit výstup trasování v Azure
1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na webový projekt a klikněte na **publikovat**.
2. V **Publikovat Web** dialogové okno, klikněte na tlačítko **publikovat**.

    Po Visual Studio publikuje aktualizace, otevře se okno prohlížeče na domovskou stránku (za předpokladu, že nebyla zrušíte **cílová adresa URL** na **připojení** karta).
3. V **Průzkumníka serveru**, klikněte pravým tlačítkem na vaší webové aplikace a vyberte **zobrazit protokoly streamování**.

    ![Protokoly streamování zobrazení v místní nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Výstup** okně se zobrazí připojeni ke službě protokolu streamování a přidá řádek oznámení každou minutu, které přejde bez protokolu pro zobrazení.

    ![Protokoly streamování zobrazení v místní nabídce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. V okně prohlížeče, který ukazuje domovskou stránku aplikace, klikněte na tlačítko **kontaktujte**.

    Během pár sekund, můžete přidat do trasování výstup z úrovně Chyba `Contact` metoda se zobrazí v **výstup** okno.

    ![Chyba trasování v okně výstupu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio je vzhledem k tomu, který je výchozí nastavení, když povolíte protokol sledování služby zobrazuje pouze chyby na úrovni trasování. Když vytvoříte novou Azure webovou aplikaci, všechny protokolování ve výchozím nastavení vypnutá, jako jste viděli při otevření stránky nastavení dříve:

    ![Aplikace odhlášení](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Ale když vyberete **zobrazit protokoly streamování**, Visual Studio automaticky změnit **aplikace Logging(File System)** k **chyba**, což znamená, že protokoly úroveň chyb získat ohlásil. Chcete-li zobrazit všechny protokoly trasování, můžete změnit toto nastavení **podrobné**. Když vyberete úroveň závažnosti, která je nižší než chyba, jsou rovněž uvedeny všechny protokoly pro vyšší úrovně závažnosti. Proto když vyberete podrobné, je také zobrazit informace, upozornění a protokoly chyb.  

1. V **Průzkumníka serveru**, klikněte pravým tlačítkem na webovou aplikaci a pak klikněte na tlačítko **nastavení zobrazení** stejně jako dříve.
2. Změna **protokolování aplikace (systém souborů)** k **podrobné**a potom klikněte na **Uložit**.

    ![Nastavení úrovně trasování na podrobné](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. V okně prohlížeče, který se teď zobrazuje vaše **obraťte se na** klikněte na tlačítko **Domů**, pak klikněte na tlačítko **o**a potom klikněte na **kontaktujte**.

    Během pár sekund **výstup** v okně se zobrazí všechny vaše výstup trasování.

    ![Výstup podrobného trasování](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    V této části povolit a zakázat protokolování pomocí nástroje nastavení Azure webové aplikace. Můžete také povolit nebo zakázat trasování – moduly naslouchání úpravou souboru Web.config. Ale upravovat soubor Web.config způsobí, že doména aplikace recyklovat, zatímco povolení protokolování prostřednictvím konfiguraci webové aplikace není to udělat. Pokud problém trvá dlouho reprodukovat, nebo je přerušované, recyklace doména aplikace může "Automatická oprava" a vynutit Počkejte, dokud se stane znovu. Povolení diagnostiky v Azure umožňuje spustit zachytávání informace o chybě okamžitě bez recyklace domény aplikace.

### <a name="output-window-features"></a>Funkce výstup – okno
**Protokolů Microsoft Azure** kartě **výstup** okno obsahuje několik tlačítek a textové pole:

![Protokoly kartě tlačítka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Tyto provádět následující funkce:

* Vymazat **výstup** okno.
* Povolit nebo zakázat zalamování řádků.
* Spuštění nebo zastavení monitorování protokolů.
* Zadejte, protokoly, které se mají monitorovat.
* Stažení protokolů.
* Filtrujte protokoly na základě hledaný řetězec nebo regulární výraz.
* Zavřít **výstup** okno.

Pokud zadáte hledaný řetězec nebo regulární výraz, Visual Studio filtruje informace o protokolování na straně klienta. To znamená, že můžete zadat kritéria po protokoly jsou zobrazeny v **výstup** okno nebo můžete změnit kritéria filtrování bez nutnosti znovu vygenerovat protokoly.

## <a name="webserverlogs"></a>Zobrazení protokolů webového serveru
Protokoly webového serveru zaznamenejte všechny aktivitu protokolu HTTP pro webovou aplikaci. Chcete-li je zobrazit **výstup** okno, musíte je povolit pro webovou aplikaci a sdělte Visual Studio chcete monitorovat.

1. V **konfiguraci webové aplikace Azure** karty, které jste otevřeli z **Průzkumníka serveru**, změňte protokolování webového serveru na **na**a pak klikněte na tlačítko **Uložit**.

    ![Povolení protokolování webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. V **výstup** okně klikněte na tlačítko **zadejte, které protokoly Microsoft Azure se mají monitorovat** tlačítko.

    ![Určete, které protokoly Azure se mají monitorovat](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. V **možnosti protokolování Microsoft Azure** dialogové okno, vyberte **webové protokoly serveru**a potom klikněte na **OK**.

    ![Monitorování protokolů webového serveru](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. V okně prohlížeče, který ukazuje webové aplikace, klikněte na tlačítko **Domů**, pak klikněte na tlačítko **o**a potom klikněte na **kontaktujte**.

    V protokolech aplikací obecně zobrazit jako první, za nímž následuje protokolů webového serveru. Možná budete muset chvíli počkejte protokolů se objeví.

    ![Protokoly webového serveru v okně výstupu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Ve výchozím nastavení když poprvé povolíte protokolů webového serveru pomocí sady Visual Studio, Azure zapisuje protokoly do systému souborů. Jako alternativu můžete portál Azure k určení tento webový server, které protokoly, které mají být zapsána do kontejneru objektů blob v účtu úložiště.

Pokud používáte portál povolit webový server k účtu úložiště Azure a pak zakažte je protokolování v sadě Visual Studio, pokud znovu povolíte protokolování v sadě Visual Studio nastavení svého účtu úložiště se obnoví.

## <a name="detailederrorlogs"></a>Zobrazit podrobné chybové zprávy protokoly
Podrobné protokoly chyb zadat nějaké další informace o požadavcích HTTP, jejichž výsledkem odpovědi kódy chyb (400 nebo novější). Chcete-li je zobrazit **výstup** okno, musíte je povolit pro webovou aplikaci a říct sadě Visual Studio chcete monitorovat.

1. V **konfiguraci webové aplikace Azure** karty, které jste otevřeli z **Průzkumníka serveru**, změňte **podrobné chybové zprávy** k **na**a potom Klikněte na tlačítko **Uložit**.

    ![Povolit podrobné chybové zprávy](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. V **výstup** okně klikněte na tlačítko **zadejte, které protokoly Microsoft Azure se mají monitorovat** tlačítko.

3. V **možnosti protokolování Microsoft Azure** dialogové okno, klikněte na tlačítko **všechny protokoly**a potom klikněte na **OK**.

    ![Monitorování všech protokolů](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na panelu Adresa v okně prohlížeče přidat další znak na adresu URL způsobí chybu 404 (například `http://localhost:53370/Home/Contactx`), a stiskněte klávesu Enter.

    Za několik sekund, protokol podrobné informace o chybě se zobrazí v sadě Visual Studio **výstup** okno.

    ![Podrobné informace o chybě protokolu – výstup – okno](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Řízení a klikněte na odkaz zobrazíte výstup protokolu ve formátu v prohlížeči:

    ![Podrobné informace o chybě protokolu – okno prohlížeče](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Stáhněte si soubor protokolu systému
Všechny protokoly, které můžete sledovat v **výstup** okno lze také stáhnout jako *.zip* souboru.

1. V **výstup** okně klikněte na tlačítko **stažení protokolů streamování**.

    ![Protokoly kartě tlačítka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Otevře Průzkumníka souborů vaší *stáhne* složku s stažený soubor vybraný.

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahování *.zip* soubor a v tématu následující strukturu složek:

    ![Stažený soubor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Protokoly trasování aplikace jsou ve *.txt* soubory *LogFiles\Application* složky.
   * Protokoly webového serveru jsou v *.log* soubory *LogFiles\http\RawLogs* složky. Nástroj můžete použít jako [analyzátoru protokolů](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) k zobrazení a zpracování těchto souborů.
   * Podrobné chybové zprávy protokoly jsou ve *.html* soubory *LogFiles\DetailedErrors* složky.

    ( *Nasazení* složka je pro soubory vytvořené serverem správy zdrojového kódu publikování; nemá nic souvisejících s publikováním sady Visual Studio. *Git* složka je pro trasování týkající se řízení zdroj publikování a protokol streamování služba souborů.)  

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

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Zobrazit protokoly trasování neúspěšných žádostí.
Protokoly trasování neúspěšných žádostí jsou užitečné, pokud budete potřebovat zjistit podrobnosti o tom, jak IIS zpracovává požadavek HTTP ve scénářích, jako je například adresa URL přepisování nebo ověřování problémy.

Webové aplikace Azure pomocí stejné funkce trasování chybných požadavků, které bylo k dispozici se službou IIS 7.0 nebo novější. Nemáte přístup k nastavení služby IIS, která konfigurace, které chyby se Zaprotokolují, ale. Pokud je povoleno trasování chybných požadavků, všechny chyby zaznamenání.

Trasování chybných požadavků můžete povolit pomocí sady Visual Studio, ale nelze je zobrazit v sadě Visual Studio. Tyto protokoly jsou soubory formátu XML. Streamování služby protokolování sleduje pouze soubory, které se považují za čitelný v režimu prostého textu: *.txt*, *.html*, a *.log* soubory.

Protokoly trasování chybných požadavků můžete zobrazit v prohlížeči přímo přes FTP nebo místně po pomocí nástroje FTP je stáhnout do místního počítače. V této části budete zobrazí je v prohlížeči přímo.

1. V **konfigurace** kartě **webové aplikace Azure** okno, které jste otevřeli z **Průzkumníka serveru**, změňte **trasování chybných požadavků** na **Na**a potom klikněte na **Uložit**.

    ![Povolit trasování neúspěšných žádostí.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na panelu Adresa v okně prohlížeče, který ukazuje webové aplikace přidat další znak na adresu URL a klikněte na Enter způsobí chybu 404.

    To způsobí, že protokolu pro trasování chybných požadavků, který se má vytvořit, a následující kroky ukazují, jak zobrazit nebo stáhnout v protokolu.

3. V sadě Visual Studio v **konfigurace** kartě **webové aplikace Azure** okně klikněte na tlačítko **otevřít na portálu pro správu**.

4. V [portál Azure](https://portal.azure.com) **nastavení** stránky pro webovou aplikaci, klikněte na tlačítko **přihlašovací údaje nasazení**a poté zadejte nové uživatelské jméno a heslo.

    ![Nové FTP uživatelské jméno a heslo](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Když se přihlásíte, budete muset použít úplné uživatelské jméno s předponu k němu název webové aplikace. Například pokud zadáte "myid" jako uživatelské jméno a lokalita je "myexample", můžete přihlásit jako "myexample\myid".
    >

5. V nové okno prohlížeče, přejděte na adresu URL, které se zobrazí v části **název hostitele FTP** nebo **FTPS hostname** v **přehled** stránky pro webovou aplikaci.

6. Přihlaste se pomocí přihlašovacích údajů FTP, které jste vytvořili dříve (včetně webových předpona názvu aplikace pro uživatelské jméno).

    Prohlížeč zobrazí kořenové složce webové aplikace.

7. Otevřete *LogFiles* složky.

    ![Otevřete složku LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otevřete složku s názvem W3SVC plus číselná hodnota.

    ![Otevřete složku W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Složka obsahuje soubory XML pro všechny chyby, které byly zaprotokolovány po povolení trasování chybných požadavků a soubor XSL, můžete prohlížeč formátu XML.

    ![W3SVC složky](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klikněte na soubor XML pro chybných požadavků, které chcete zobrazit informace o trasování pro.

    Následující obrázek znázorňuje součástí informací o trasování pro ukázkové chybu.

    ![Trasování neúspěšných žádostí v prohlížeči](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Další kroky
Už víte, jak Visual Studio lze snadno zobrazit protokoly vytvořené webové aplikace Azure. Následující části obsahují odkazy na další zdroje informací na související témata:

* Řešení potíží s Azure webové aplikace
* Ladění v sadě Visual Studio
* Vzdálené ladění v Azure
* Trasování v aplikacích ASP.NET
* Analýza protokolů webového serveru
* Analýza protokolů trasování neúspěšných žádostí.
* Ladění cloudové služby

### <a name="azure-web-app-troubleshooting"></a>Řešení potíží s Azure webové aplikace
Další informace o odstraňování potíží s webovými aplikacemi ve službě Azure App Service najdete v následujících zdrojích informací:

* [Postup monitorování webové aplikace](/manage/services/web-sites/how-to-monitor-websites/)
* [Nevracení paměti v Azure Web Apps s nástroji Visual Studio 2013 příčin](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM příspěvku na blogu o funkcích nástroje Visual Studio pro analýzu spravovat problémy s pamětí.
* [Službě Azure web apps online nástroje byste měli vědět o](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Příspěvek blogu společností Apple Amitu.

Nápovědu k řešení problémů s konkrétní dotaz spusťte vlákno v jednom z následujících fóra:

* [Azure fórum na webu technologie ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Azure fórum na webu MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Ladění v sadě Visual Studio
Další informace o tom, jak používat režim ladění v sadě Visual Studio najdete v tématu [ladění v sadě Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) a [ladění tipy sadou Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Vzdálené ladění v Azure
Další informace o vzdálené ladění pro webové aplikace Azure a webové úlohy najdete v následujících zdrojích informací:

* [Úvod do vzdálené ladění webových aplikací služby Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Úvod do vzdáleného ladění Azure App Service Web Apps část 2 - uvnitř vzdálené ladění](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Úvod do vzdáleného ladění na Azure App Service Web Apps část 3 - prostředí s více instancemi a GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Webové úlohy ladění (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Pokud vaše webová aplikace používá back-end Azure webového rozhraní API nebo Mobile Services a je nutné ladit, najdete v části [ladění v rozhraní .NET back-end v sadě Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Trasování v aplikacích ASP.NET
Na Internetu nejsou k dispozici žádné důkladné a aktuální přehled trasování rozhraní ASP.NET. Které může provádět nejvhodnější je začít pracovat s původní úvodní materiály, které jsou vytvořeny pro webové formuláře protože MVC nebyla ještě neexistuje a který doplnit s novější blog odešle které se zaměřují na konkrétní problémy. Některé místa vhodná zahájíte jsou v následujících zdrojích informací:

* [Monitorování a Telemetrie (vytváření reálných cloudových aplikací s Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Elektronická kniha kapitoly s doporučeními pro trasování v cloudu Azure aplikace.
* [Trasování rozhraní ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Starý, ale stále dobrý prostředku pro základní informace o subjektu.
* [Trasování – moduly naslouchání](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informace o trasování – moduly naslouchání, ale není zmínili [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Návod: Integrace s trasováním System.Diagnostics trasování technologie ASP.NET](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Tento článek je v minulosti, ale zahrnuje některé další informace, které nezahrnuje úvodní článek.
* [Trasování v zobrazení syntaxe Razor rozhraní ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Kromě toho trasování v zobrazení syntaxe Razor v příspěvku taky vysvětluje, jak vytvořit filtr chyby, aby bylo protokolování všechny neošetřených výjimek v aplikaci MVC. Informace o tom, jak protokolování všechny neošetřených výjimek v aplikaci webových formulářů, podívejte se na příklad souboru Global.asax v [kompletní příklad pro obslužné rutiny chyba](http://msdn.microsoft.com/library/bb397417.aspx) na webu MSDN. V MVC nebo webového formuláře Pokud chcete protokolovat určité výjimky, ale nechat rozhraní výchozí zpracování vstoupí v platnost, můžete zachytit a opětovné jako v následujícím příkladu:

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

* [Streamování diagnostické trasování protokolování z příkazového řádku Azure (plus balíčku Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Postup pomocí příkazového řádku provádět jaké tento kurz ukazuje, jak provést v sadě Visual Studio. [Balíčku glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) je nástroj pro ladění aplikací ASP.NET.
* [Webové aplikace, protokolování a diagnostiky - pomocí David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) a [protokoly z webových aplikací – s David Ebbo streamování](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videa Scott Hanselman a David Ebbo.

Pro protokolování chyb, je použití rozhraní protokolování open source, jako alternativu k vytvoření vlastního kódu trasování [ELMAH](http://nuget.org/packages/elmah/). Další informace najdete v tématu [příspěvky Scott Hanselman o ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Navíc nemusíte pomocí technologie ASP.NET nebo `System.Diagnostics` protokoly trasování pro získání streamování z Azure. Webové aplikace Azure streamování služby protokolování datové proudy žádné *.txt*, *.html*, nebo *.log* soubor, který najde v *LogFiles* složky. Proto můžete vytvořit vlastní protokolování systému, který zapíše do systému souborů webové aplikace a souboru je automaticky streamování a stažena. Stačí je zápisu aplikační kód, který vytvoří soubory v *d:\home\logfiles* složky.

### <a name="analyzing-web-server-logs"></a>Analýza protokolů webového serveru
Další informace o analýze protokolů webového serveru najdete v následujících zdrojích informací:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Nástroj pro zobrazení dat v protokolů webového serveru (*.log* soubory).
* [Řešení potíží s problémy s výkonem služby IIS nebo pomocí LogParser chyb aplikací ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Úvod do protokolu analyzátoru nástroj, který můžete použít k analýze protokolů webového serveru.
* [Příspěvky podle Roberta Mcmurrayho na pomocí LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Stavový kód HTTP ve službě IIS 7.0, IIS 7.5 a IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analýza protokolů trasování neúspěšných žádostí.
Zahrnuje webu Microsoft TechNet [pomocí trasování chybných požadavků](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) oddíl, což může být užitečné pro pochopení jak používat tyto protokoly. Tato dokumentace je však zaměřen především na Konfigurace trasování neúspěšných žádostí ve službě IIS, což nelze provést ve službě Azure Web Apps.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
