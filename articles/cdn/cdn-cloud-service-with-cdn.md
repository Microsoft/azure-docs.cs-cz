---
title: Cloudové služby Azure integrovat Azure CDN | Microsoft Docs
description: Informace o nasazení Cloudová služba, která poskytuje obsah z integrované koncového bodu Azure CDN
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f131eb021d85766f12b0fb6cb8b5a07f965f9c97
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="intro"></a> Cloudové služby integrovat Azure CDN
Cloudové služby můžete integrovat Azure CDN, obsluhující žádný obsah z cloudové služby umístění. Tento přístup poskytuje následující výhody:

* Můžete snadno nasadit a aktualizaci bitové kopie, skriptů a šablon v adresářích projektu cloudové služby
* Snadno upgradujte balíčků NuGet v rámci cloudové služby, například jQuery nebo Bootstrap verze
* Správa webové aplikace a vaše všechna obsahu CDN obsluhovat ze stejné rozhraní sady Visual Studio
* Pracovní postup jednotná nasazení pro webové aplikace a obsah obsluhuje CDN
* ASP.NET sdružování a minimalizace integrovat Azure CDN

## <a name="what-you-will-learn"></a>Co se dozvíte
V tomto kurzu se naučíte, jak:

* [Koncový bod Azure CDN integrovat s cloudovou službou a poskytovat statický obsah na webových stránkách z Azure CDN](#deploy)
* [Konfigurace nastavení mezipaměti pro statický obsah v rámci cloudové služby](#caching)
* [Poskytovat obsah z akce kontroleru prostřednictvím Azure CDN](#controller)
* [Používat spojeno dohromady a minifikovaný obsah prostřednictvím Azure CDN při zachování skript ladění prostředí v sadě Visual Studio](#bundling)
* [Konfigurace záložního skriptů a šablon stylů CSS při offline Azure CDN](#fallback)

## <a name="what-you-will-build"></a>Co se sestavení
Nasazení cloudové služby webové role pomocí výchozí šablony ASP.NET MVC, přidáte kód pro práci s obsahem z integrované CDN Azure, jako je například bitovou kopii, výsledky akce kontroleru a výchozí souborů JavaScript a CSS a také napsat kód pro konfigurace záložní mechanismus pro sady zpracovat v případě, že CDN je offline.

## <a name="what-you-will-need"></a>Co budete potřebovat
V tomto kurzu má následující požadavky:

* Aktivní [účtem Microsoft Azure.](/account/)
* Visual Studio 2015 se [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure:
> 
> * Můžete [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/) -získáte kredity, můžete použít k vyzkoušení placených služeb Azure a i po jejich použití až můžete účet ponechat a používat bezplatné služby Azure, jako jsou weby.
> * Můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -vaše předplatné MSDN vám dává kredity každý měsíc, které můžete použít pro placené služby Azure.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Nasazení cloudové služby
V této části nasazení výchozí šablony aplikace ASP.NET MVC v sadě Visual Studio 2015 do cloudové služby webové role a potom ji integrovat s nástrojem nový koncový bod CDN. Postupujte podle pokynů níže:

1. V sadě Visual Studio 2015, vytvořte novou službu Azure cloud z řádku nabídek přechodem na **soubor > Nový > Projekt > Cloud > Cloudová služba Azure**. Pojmenujte ho a klikněte na tlačítko **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Vyberte **webovou roli ASP.NET** a klikněte na **>** tlačítko. Klikněte na tlačítko OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Vyberte **MVC** a klikněte na tlačítko **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Nyní publikování této webové role do cloudové služby Azure. Klikněte pravým tlačítkem na projekt cloudové služby a vyberte **publikovat**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Pokud jste ještě nebyly přihlášeni Microsoft Azure, klikněte na **přidat účet...**  rozevíracího seznamu a klikněte na tlačítko **přidat účet** položku nabídky.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Na stránce přihlášení Přihlaste se pomocí účtu Microsoft, který jste použili k aktivaci účtu Azure.
7. Jakmile jste přihlášení, klikněte na možnost **Další**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Za předpokladu, že jste nevytvořili účet cloudové služby nebo úložiště, Visual Studio vám pomůže vytvořit obě. V **vytvoření cloudové služby a účet** dialogové okno, zadejte název požadované služby a vyberte požadovanou oblast. Poté klikněte na možnost **Vytvořit**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Na stránce Nastavení publikování ověřte konfiguraci a klikněte na tlačítko **publikovat**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > Proces publikování pro cloudové služby trvá příliš dlouho. Povolit nasazení webu pro všechny role možnost provádět ladění mnohem rychlejší cloudové služby tím, že poskytuje rychlé (ale dočasné) aktualizace webové role. Další informace o této možnosti najdete v tématu [publikování cloudové služby pomocí nástroje Azure](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Když **protokoly aktivit Microsoft Azure** ukazuje, že stav publikování je **dokončeno**, vytvoří koncový bod CDN, která je integrovaná s touto cloudovou službou.
   
   > [!WARNING]
   > Pokud po publikování, nasazené cloudové služby zobrazí chybové obrazovce, je pravděpodobné, protože používá cloudové služby, které jste nasadili [hosta operační systém, který nezahrnuje .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Vám může tento problém obejít tím [nasazení .NET 4.5.2 jako úloha spuštění](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN
Profil CDN je kolekcí koncových bodů CDN.  Jednotlivé profily obsahují jeden nebo víc koncových bodů CDN.  Můžete použít více profilů a uspořádat koncové body CDN podle internetové domény, webové aplikace nebo jiných kritérií.

> [!TIP]
> Pokud již máte profil CDN, který chcete použít pro tento kurz, pokračujte [vytvořte nový koncový bod CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Vytvoření nového koncového bodu CDN
**Chcete-li vytvořit nový koncový bod CDN pro váš účet úložiště**

1. Na webu [Azure Portal](https://portal.azure.com) přejděte na svůj profil CDN.  Je možné, že jste si ho v předchozím kroku připnuli k řídicímu panelu.  Pokud ne, najdete ho kliknutím na položku **Procházet**, poté položku **Profily CDN** a nakonec kliknutím na profil, ke kterému plánujete přidat koncový bod.
   
    Otevře se okno Profil CDN.
   
    ![Profil CDN][cdn-profile-settings]
2. Klikněte na tlačítko **Přidat koncový bod**.
   
    ![Tlačítko Přidat koncový bod][cdn-new-endpoint-button]
   
    Otevře se okno **Přidání koncového bodu**.
   
    ![Okno Přidání koncového bodu][cdn-add-endpoint]
3. Zadejte **Název** tohoto koncového bodu CDN.  Tento název se použije pro přístup k prostředkům v mezipaměti v doméně `<EndpointName>.azureedge.net`.
4. V **typ původu** rozevíracího seznamu vyberte *Cloudová služba*.  
5. V **název počátečního hostitele** rozevíracího seznamu, vyberte cloudovou službu.
6. Ponechte výchozí nastavení pro **cesty ke zdroji**, **hlavičky hostitele počátku**, a **port protokolu nebo původu**.  Je nutné zadat aspoň jeden protokol (HTTP nebo HTTPS).
7. Kliknutím na tlačítko **Přidat** vytvořte nový koncový bod.
8. Jakmile je koncový bod vytvořený, zobrazí se v seznamu koncových bodů daného profilu. Zobrazení seznamu zobrazuje adresu URL, kterou je nutné použít k přístupu k obsahu v mezipaměti, a také doménu původu.
   
    ![Koncový bod CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > Koncový bod nebude hned dostupný pro použití.  To může trvat až 90 minut, než se registrace rozšíří v síti CDN. Uživatelé, kteří se pokusí použít název domény CDN okamžitě obdržet stavový kód 404, dokud nebude obsah k dispozici prostřednictvím CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Testování koncového bodu CDN
Pokud je stav publikování **dokončeno**, otevřete okno prohlížeče a přejděte do **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. V části Moje nastavení je tato adresa URL:

    http://camservice.azureedge.net/Content/bootstrap.css

Která odpovídá následující původní adresu URL na koncový bod CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Když přejdete na **http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, v závislosti na prohlížeči, zobrazí se výzva ke stažení nebo otevřete bootstrap.css, které byly dodány z vaší publikované webové aplikace.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Stejně tak přístup k jakékoli veřejně přístupné URL u **http://*&lt;serviceName >*.cloudapp.net/**, přímých z koncový bod CDN. Příklad:

* Soubor .js z cesty/Script
* Všechny soubory obsahu z/Content cesta
* Kontroler nebo akce
* Pokud řetězec dotazu je povoleno na koncový bod CDN, libovolná adresa URL s řetězci dotazů

Ve skutečnosti s výše konfigurací, můžete hostovat službu celý cloudu v **http://*&lt;cdnName >*.azureedge.net/**. Pokud I přejděte na **http://camservice.azureedge.net/**, výsledek akce dostat z domovské nebo Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

To neznamená, ale, že je vždy vhodné k obsluze celý cloudové služby prostřednictvím Azure CDN. 

CDN s optimalizací statické doručení není nutně urychlí práci doručování dynamické prostředky, které nejsou určeny do mezipaměti, nebo jsou aktualizovány velmi často, protože CDN musí novou verzi asset vyžádat ze zdrojového serveru velmi často. V tomto scénáři můžete povolit [dynamické akcelerace lokality](cdn-dynamic-site-acceleration.md) optimalizace (DSA) na váš koncový bod CDN, která využívá různé postupy pro urychlení doručení neurčené dynamické prostředků. 

Pokud máte síť se smíšenými statické a dynamické obsah, můžete poskytovat statický obsah z CDN s typem statické optimalizace (například doručení obecné webové) a poskytovat dynamický obsah přímo ze zdrojového serveru, nebo prostřednictvím w koncový bod CDN Optimalizace DSA i-tým zapnout případ od případu. Za tímto účelem jste už viděli, jak k jednotlivé soubory obsahu z koncového bodu CDN. I vám ukáže, jak používat obsah z akce kontroleru prostřednictvím Azure CDN mohly konkrétní řadič akce prostřednictvím konkrétní koncový bod CDN.

Alternativou je zjistit, které obsah v případech v cloudové službě působit z Azure CDN. Za tímto účelem jste už viděli, jak k jednotlivé soubory obsahu z koncového bodu CDN. I vám ukáže, jak k obsluze konkrétní řadič akce prostřednictvím koncového bodu CDN v [poskytovat obsah z akce kontroleru prostřednictvím Azure CDN](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Konfigurace možností ukládání do mezipaměti pro statické soubory v rámci cloudové služby
Díky integraci Azure CDN v cloudové službě můžete určit, jak se mají statický obsah do mezipaměti v koncového bodu CDN. Chcete-li to provést, otevřete *Web.config* z vaší webové role projektu (např. WebRole1) a přidejte `<staticContent>` element `<system.webServer>`. Zadaný kód XML nakonfiguruje mezipaměti vyprší za 3 dny.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Jakmile to uděláte, bude sledovat všechny statické soubory v rámci cloudové služby stejného pravidla v mezipaměti CDN. K podrobnějšímu řízení nastavení mezipaměti, přidejte *Web.config* soubor do složky a přidat nastavení existuje. Například přidejte *Web.config* do souboru *\Content* složky a nahraďte obsah s následující kód XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Toto nastavení způsobí, že všechny statické soubory z *\Content* složky ukládat do mezipaměti 15 dní.

Další informace o tom, jak nakonfigurovat `<clientCache>` elementu, najdete v části [mezipaměti klienta &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

V [poskytovat obsah z akce kontroleru prostřednictvím Azure CDN](#controller), I také ukazují, jak můžete konfigurovat nastavení mezipaměti pro výsledky akce kontroleru v mezipaměti CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Poskytovat obsah z akce kontroleru prostřednictvím Azure CDN
Při integraci s Azure CDN webové role cloudové služby, je poměrně snadné ho poskytovat obsah z akce kontroleru prostřednictvím Azure CDN. Než obsluhující vaše cloudové služby přímo přes Azure CDN (ukázán výše), [Maarten Balliauw](https://twitter.com/maartenballiauw) ukazuje, jak to udělat pomocí zábavné MemeGenerator řadiče v [sníží se latence na webu pomocí Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). I bude jednoduše ho znovu vyvolali sem.

Předpokládejme, že ve vašem cloudu podle služby, kterou chcete vygenerovat memes bitovou kopii malí Karel Norris (fotografii podle [Jakub Light](http://www.flickr.com/photos/alan-light/218493788/)) podobné výjimky:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Máte jednoduchou `Index` akce, která umožňuje zákazníkům zadejte jejich v bitové kopii, pak vygeneruje meme po jejich odeslání na akci. Vzhledem k tomu, že je Karel Norris, kterou byste očekávali tuto stránku k velkým oblíbených globálně. Toto je dobrým příkladem obsluhovat polovičním dynamický obsah s Azure CDN.

Výše uvedený postup k nastavení této akce kontroleru:

1. V *\Controllers* složky, vytvořte nový soubor .cs s názvem *MemeGeneratorController.cs* a nahraďte obsah následujícím kódem. Nezapomeňte nahradit název CDN zvýrazněná část.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Klikněte pravým tlačítkem na výchozí `Index()` akce a vyberte **přidat zobrazení**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Přijměte níže uvedených nastavení a klikněte na **přidat**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Otevřete nový *Views\MemeGenerator\Index.cshtml* a nahraďte následující jednoduché HTML pro odesílání jejich obsah:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Znovu publikovat cloudové služby a přejděte do **http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** v prohlížeči.

Po odeslání formuláře hodnoty tak, aby `/MemeGenerator/Index`, `Index_Post` metoda akce vrací odkaz `Show` metoda akce s příslušnými vstupní identifikátor. Když kliknete na odkaz, dostanete následující kód:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Pokud je připojena vaše místní ladicí program, obdržíte regulární ladění zkušenosti s místní přesměrování. Pokud je spuštěn v rámci cloudové služby, se přesměruje na:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Která odpovídá následující původní adresu URL na koncový bod CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Pak můžete použít `OutputCacheAttribute` atributu u `Generate` metoda k určení, jak výsledek akce do mezipaměti, který bude respektovat Azure CDN. Kód uvedený níže zadejte vypršení platnosti mezipaměti 1 hodina (3 600 sekund).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Podobně můžete může sloužit obsah z jakékoli akce kontroleru v rámci cloudové služby prostřednictvím Azure CDN, s požadovanou možnost ukládání do mezipaměti.

V další části I vám ukáže, jak k obsluze připojené a minifikovaný skriptů a šablon stylů CSS pomocí Azure CDN.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>ASP.NET sdružování a minimalizace integrovat Azure CDN
Skripty a šablon stylů CSS předlohy se styly změňte zřídka a prvotní kandidáty pro mezipaměť Azure CDN. Obsluhující celý webovou roli prostřednictvím Azure CDN je nejjednodušší způsob, jak integrovat Azure CDN sdružování a minimalizace. Ale tak, jak chcete nemusí k tomu, I vám ukáže, jak to udělat při zachování požadované vývojářský možností ASP.NET sdružování a minimalizace, jako například:

* Skvělé ladění režimu prostředí
* Zjednodušené nasazení
* Okamžitá aktualizace klientů pro upgrade verze skriptu nebo šablon stylů CSS
* Nouzový mechanismus, když se nezdaří koncový bod CDN
* Minimalizovat úpravy kódu

V **WebRole1** projekt, který jste vytvořili v [koncový bod Azure CDN integrovat svůj web Azure a poskytovat statický obsah na webových stránkách z Azure CDN](#deploy), otevřete *App_Start\ BundleConfig.cs* a podívejte se na `bundles.Add()` volání metody.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

První `bundles.Add()` příkaz přidá na virtuální adresář sady skriptu `~/bundles/jquery`. Potom otevřete *Views\Shared\_Layout.cshtml* zobrazíte vykreslení značky script sady. Nyní byste měli mít vyhledejte následující řádek kódu Razor:

    @Scripts.Render("~/bundles/jquery")

Při spuštění tohoto kódu Razor v roli webů Azure, se budou vykreslovat `<script>` značky pro sady skript, který je podobný následujícímu:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Ale když ji spustí v sadě Visual Studio zadáním `F5`, se budou vykreslovat každý soubor skriptu v sadě jednotlivě (v případě výše uvedené jenom jeden skript soubor je v sadě):

    <script src="/Scripts/jquery-1.10.2.js"></script>

To umožňuje ladit kód JavaScript ve vašem vývojovém prostředí při snížení souběžných klientských připojení (sdružování) a vylepšuje soubor stáhnout výkonu (minimalizace) v provozním prostředí. Je skvělé funkce, která zachovat díky integraci Azure CDN. Navíc vzhledem k tomu, že sada vykreslené již obsahuje řetězec automaticky generované verze, které chcete replikovat tato funkcionalita proto při každé aktualizaci vaší verzí jQuery prostřednictvím balíčku NuGet, může být aktualizován na straně klienta co nejdříve.

Postupujte podle těchto kroků k integraci ASP.NET sdružování a minimalizace s koncový bod CDN.

1. Zpět v *App_Start\BundleConfig.cs*, změnit `bundles.Add()` můžete použít jiné metody [sady konstruktor](http://msdn.microsoft.com/library/jj646464.aspx), ten, který určuje adresu CDN. Chcete-li to provést, nahraďte `RegisterBundles` definici metody s následujícím kódem:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Nezapomeňte nahradit `<yourCDNName>` s názvem Azure CDN.
   
    Uveďte stručný nastavujete `bundles.UseCdn = true` a přidat pečlivě vytvořené adresy URL CDN každého svazku. Například první konstruktor v kódu:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    je stejný jako:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Tento konstruktor informuje ASP.NET sdružování a minimalizace soubory jednotlivých skriptu při místně ladit vykreslit, ale zadaná adresa CDN používat pro přístup k dotyčném skriptu. Pamatujte však dvě důležité charakteristiky s Tento pečlivě vytvořené CDN adresy URL:
   
   * Původ pro tuto adresu URL CDN `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, který je ve skutečnosti virtuální adresář sady skript v rámci cloudové služby.
   * Vzhledem k tomu, že používáte konstruktor CDN, značky script CDN pro sadu už obsahuje automaticky generovaný verze řetězec v adrese URL vykreslené. Řetězec verze jedinečný musíte vygenerovat ručně pokaždé, když je sada skript upravit tak, aby vynutit k neúspěšnému přístupu do mezipaměti v Azure CDN. Ve stejnou dobu musí zůstat tento řetězec jedinečný verze konstantní prostřednictvím dobu životnosti nasazení tak, aby po nasazení sady maximalizovat přístupů k mezipaměti v Azure CDN.
   * Řetězec dotazu v = < W.X.Y.Z > si z *Properties\AssemblyInfo.cs* v projektu webové role. Může mít nasazení pracovního postupu, který zahrnuje zvyšování verze sestavení pokaždé, když publikujete do Azure. Nebo můžete upravit pouze *Properties\AssemblyInfo.cs* ve vašem projektu a automaticky zvýší řetězec verze pokaždé, když vytvoříte, pomocí zástupných znaků ' *'. Příklad:
     
        [sestavení: AssemblyVersion("1.0.0.*")]
     
     Zde bude fungovat jakékoli strategie zefektivnění generování jedinečného řetězce po celou dobu životnosti nasazení.
2. Přístup na domovskou stránku a znovu publikovat cloudové služby.
3. Zobrazte kód HTML pro stránku. Nyní byste měli mít najdete v části Adresa URL CDN vykresleno, řetězcem jedinečný verze pokaždé, když je znovu publikovat změny do cloudové služby. Příklad:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. V sadě Visual Studio, ladění cloudové služby v sadě Visual Studio zadáním `F5`.,
5. Zobrazte kód HTML pro stránku. Zobrazí se stále každý soubor skriptu jednotlivě vykreslen tak, že máte konzistentní ladění prostředí v sadě Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Nouzový mechanismus adresy URL CDN
Pokud z nějakého důvodu selže koncový bod Azure CDN, chcete být dostatečně inteligentní pro přístup k webovému serveru původu jako záložní volbu pro načítání JavaScript nebo Bootstrap webové stránky. Je dostatečně závažné, ztratí obrázků na váš web z důvodu nedostupnosti CDN, ale mnohem závažnější přijít o velmi důležitý stránky funkce poskytované službou skriptů a šablon.

[Sady](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) třída obsahuje vlastnost s názvem [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) , vám umožňuje nakonfigurovat nouzový mechanismus pro selhání CDN. Tato vlastnost, postupujte podle následujících kroků:

1. Otevřete v projektu webové role *App_Start\BundleConfig.cs*, které jste přidali adresu URL CDN v každé [sady konstruktor](http://msdn.microsoft.com/library/jj646464.aspx)a proveďte následující změny zvýrazněné přidat nouzový mechanismus, který na výchozí hodnoty sady:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Když `CdnFallbackExpression` je hodnotou not null, skript je vložit do kódu HTML do otestovat, zda je sada úspěšně načetl a pokud ne, přístup k sadě přímo z původu webového serveru. Tato vlastnost musí být nastavena na výraz jazyka JavaScript, který kontroluje, zda příslušné sady CDN je načtena správně. Výraz potřebné k testování každého svazku se liší podle obsahu. Pro výchozí sady výše:
   
   * `window.jquery` je definována v jquery-{version} .js
   * `$.validator` je definována v jquery.validate.js
   * `window.Modernizr` je definována v modernizer-{version} .js
   * `$.fn.modal` je definována v bootstrap.js
     
     Možná jste si všimli, že I nenastavili CdnFallbackExpression pro `~/Cointent/css` sady. Důvodem je, že je nyní [chyb v System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) , vloží `<script>` značky pro záložní šablon stylů CSS místo očekávané `<link>` značky.
     
     Existuje, ale velká [záložní styl sady](https://github.com/EmberConsultingGroup/StyleBundleFallback) nabízené [členskými konzultace ohledně skupiny](https://github.com/EmberConsultingGroup).
2. Toto řešení použít pro šablon stylů CSS, vytvořte nový soubor .cs v projektu webové role *App_Start* složku s názvem *StyleBundleExtensions.cs*a nahraďte jeho obsah s [kód z Githubu ](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. V *App_Start\StyleFundleExtensions.cs*, přejmenujte obor názvů pro vaši webovou roli název (například **WebRole1**).
4. Přejděte zpět na `App_Start\BundleConfig.cs` a upravit poslední `bundles.Add` příkaz s následující zvýrazněný kód:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Tato nová metoda rozšíření používá stejné nápad skriptu ve formátu HTML ke kontrole DOM pro vložení odpovídající název třídy, název pravidla a pravidla hodnota definovaná v CSS sady a vrátí k původní webový server, pokud není nalezena shoda.
5. Přístup na domovskou stránku a znovu publikovat cloudové služby.
6. Zobrazte kód HTML pro stránku. Vložený skripty pro byste měli najít podobný následujícímu:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Všimněte si, že vloženého skriptu pro sadu šablon stylů CSS stále obsahuje nesprávně pracujících zbývajících z `CdnFallbackExpression` vlastnost v řádku:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Ale od první část || výraz vždy vrátí hodnotu PRAVDA (na řádku přímo vyšší), bude funkce document.write() nebude nikdy spuštěn.

## <a name="more-information"></a>Další informace
* [Přehled sítě pro doručování obsahu Azure (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Používání Azure CDN](cdn-create-new-endpoint.md)
* [ASP.NET sdružování a minimalizace](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
