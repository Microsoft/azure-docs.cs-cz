---
title: Sledování dostupnosti a odezvy libovolných webů | Dokumentace Microsoftu
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 11a421a30508774d976def8d5836451743ecb6ea
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270378"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Sledování dostupnosti a odezvy libovolných webů
Po nasazení webové aplikace nebo webu na libovolném serveru můžete nastavit testy ke sledování dostupnosti a odezvy. [Azure Application Insights](app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Upozorní vás v případě, že vaše aplikace reaguje pomalu nebo nereaguje vůbec.

Testy dostupnosti můžete nastavit pro libovolný koncový bod HTTP nebo HTTPS, který je přístupný z veřejného internetu. Na testovaný web není třeba nic přidávat. Dokonce se ani nemusí jednat o váš web – můžete například testovat službu REST API, na které jste závislí.

Existují dva typy testů dostupnosti:

* [Testování ping adresy URL](#create): jednoduchý test, který můžete vytvořit na portálu Azure Portal.
* [Vícekrokový webový test](#multi-step-web-tests): který můžete vytvořit v sadě Visual Studio Enterprise a odeslat na portál.

Pro každý prostředek aplikace můžete vytvořit až 100 testů dostupnosti.


## <a name="create"></a>Otevření prostředku pro sestavy testů dostupnosti

**Pokud jste již nakonfigurovali Application Insights** pro webovou aplikaci, otevřete její prostředek Application Insights na webu [Azure Portal](https://portal.azure.com).

**Nebo pokud chcete zobrazit sestavy v novém prostředku,** přejděte na web [Azure Portal](https://portal.azure.com) a vytvořte prostředek Application Insights.

![Nový > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Kliknutím na možnost **Všechny prostředky** otevřete okno Přehled pro nový prostředek.

## <a name="setup"></a>Vytvoření testu adresy URL pomocí příkazu Ping
Otevřete okno Dostupnost a přidejte test.

![Vyplňte alespoň adresu URL webu](./media/app-insights-monitor-web-app-availability/001-create-test.png)

* **Adresa URL** může být libovolná webová stránka, kterou chcete otestovat, ale musí být viditelná z veřejného internetu. Adresa URL může obsahovat řetězec dotazu. To znamená, že můžete také trochu vyzkoušet svou databázi. Pokud se adresa URL přeloží na přesměrování, budeme ji sledovat až po 10 přesměrování.
* **Analyzovat závislé požadavky**: Pokud tato možnost zaškrtnutá, test si vyžádá obrázky, skripty, soubory stylu a další soubory, které jsou součástí testované webové stránky. Zaznamenaná doba odezvy zahrnuje i čas potřebný k získání těchto souborů. Pokud tyto prostředky nelze úspěšně stáhnout v časovém limitu pro celý test, test se nezdaří. Pokud tato možnost není zaškrtnutá, test si vyžádá pouze soubor na zadané adrese URL.

* **Povolit opakované pokusy**:  Pokud tato možnost zaškrtnutá, test se nezdaří, zopakuje se za krátkou dobu. Selhání je nahlášeno pouze v případě tří po sobě jdoucích neúspěšných pokusů. Následné testy jsou pak provedeny s obvyklou frekvencí testu. Opakování je dočasně pozastaveno do dalšího úspěchu. Toto pravidlo platí nezávisle na každém umístění testu. Doporučujeme tuto možnost. V průměru přibližně 80 % selhání při opakování zmizí.

* **Frekvence testů**: Nastaví, jak často test spustí z umístění každého testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.

* **Testovací umístění** jsou místa, ze kterých naše servery odesílají webové požadavky na adresu URL. Naše minimální počet umístění doporučené testu je pět aby bylo možné zajistit, že možné rozlišit problémy ve vašem webu od problémů se sítí. Můžete vybrat až 16 umístění.

> [!NOTE]
> * Důrazně doporučujeme testování z více míst s minimálně pět umístění. Toto je zabránit falešných poplachů, které mohou být způsobeny přechodné problémy s konkrétní umístění. Kromě toho jsme našli, optimální konfigurace je počet umístění testu být roven mezní hodnota umístění upozornění + 2. 
> * Povolení možnosti výsledků "Analyzovat závislé požadavky" v přísnější kontroly. Test může selhat pro případy, které nemusí být patrné při procházení webu ručně.

* **Kritéria úspěchu**:

    **Časový limit testu**: Zmenšete tuto hodnotu pro upozornění pomalé odezvy. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.

    **Odpověď HTTP**: Vrácený kód stavu, který se počítá jako úspěšný. 200 je kód, který označuje, že byla vrácena normální webová stránka.

    **Shoda obsahu**: řetězec, například „Vítejte!“ U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat.

* **Mezní hodnota umístění upozornění**: Doporučujeme aspoň 3 nebo 5 umístění. Optimální vztah mezi mezní hodnota umístění upozornění a počet umístění testu je **mezní hodnota umístění upozornění** = **počet umístění testu** - 2, s minimálně pět testování umístění.

## <a name="multi-step-web-tests"></a>Vícekrokové webové testy
Je možné sledovat scénář, který zahrnuje posloupnost adres URL. Například pokud sledujete prodejní web, můžete otestovat, zda správně funguje přidávání položek do nákupního košíku.

> [!NOTE]
> Vícekrokové webové testy jsou zpoplatněné. [Cenové schéma](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Pro vytvoření vícekrokového testu uložte scénář pomocí sady Visual Studio Enterprise a pak nahrajte tento záznam do služby Application Insights. Application Insights přehrává scénář v intervalech a ověřuje odezvy.

> [!NOTE]
> * V testech nelze použít programové funkce nebo smyčky. Test musí být zcela obsažený ve skriptu .webtest. Můžete však použít standardní moduly plug-in.
> * Vícekrokové webové testy podporují pouze anglické znaky. Pokud používáte sadu Visual Studio v jiném jazyce, aktualizujte definiční soubor webového testu pro překlad/vyloučení jiných než anglických znaků.
>

#### <a name="1-record-a-scenario"></a>1. Záznam scénáře
Slouží k zaznamenání relace webové aplikace Visual Studio Enterprise.

1. Vytvořte projekt testu výkonnosti webu.

    ![Vytvořte v sadě Visual Studio Enterprise nový projekt ze šablony výkonnostního testu webu a zátěžového testu.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Nevidíte šablonu výkonnostního testu webu a zátěžového testu?* – Zavřete sadu Visual Studio Enterprise. Otevřete **Instalační program sady Visual Studio**, pomocí kterého upravíte svou instalaci sady Visual Studio Enterprise. V části **Jednotlivé komponenty** vyberte **Nástroje pro testování výkonnosti webů a zátěžové testování**.

2. Otevřete soubor .webtest a spusťte záznam.

    ![Otevřete soubor .webtest a klikněte na tlačítko Záznam.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Proveďte uživatelské akce, kterou chcete simulovat v testu: otevřete webovou stránku, přidat do košíku produkt a tak dále. Pak test zastavte.

    ![Záznamník testování webu běží v aplikaci Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Nevytvářejte příliš dlouhý scénář. Platí limit 100 kroků a 2 minut.
4. Upravte test na:

   * Přidání ověření ke kontrole přijatého textu a kódů odpovědi.
   * Odeberte všechny nadbytečné interakce. Můžete také odebrat závislé požadavky pro obrázky, reklamy nebo sledování lokalit.

     Mějte na paměti, že můžete upravit pouze zkušební skript – nelze přidat vlastní kód nebo volat jiné webové testy. Nevkládejte do testu smyčky. Můžete použít standardní zásuvné moduly webového testu.
5. Spusťte test v sadě Visual Studio a ujistěte se, že funguje.

    Spouštěč webových testů otevře webový prohlížeč a zopakuje zaznamenané akce. Zkontrolujte, zda vše funguje podle očekávání.

    ![V sadě Visual Studio otevřete soubor .webtest a klikněte na tlačítko Spustit.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Nahrajte test webu do služby Application Insights
1. Na portálu služby Application Insights vytvořte webový test.

    ![V okně webového testu zvolte možnost Přidat.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Vyberte vícekrokový test a nahrajte soubor .webtest.

    ![Vyberte vícekrokový webový test.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Nastavte umístění testu, četnost a parametry výstrah stejným způsobem jako u testů pomocí příkazu ping.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Doba zapojení a náhodná čísla do vícekrokového testu
Předpokládejme, že testujete nástroj, který získá data závislá na čase, například akcie z externího kanálu. Při záznamu webového testu je nutné použít konkrétní časy, ale nastavit je jako parametry testu, čas spuštění a čas ukončení.

![Webový test s parametry.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Při spuštění testu chcete, aby čas ukončení vždy představoval aktuální čas a čas spuštění by měl začínat před 15 minutami.

Zásuvné moduly webového testu nabízejí způsob parametrizace časů.

1. Přidejte zásuvný modul webového testu pro každou hodnotu parametru proměnné, kterou chcete. V panelu nástrojů webového testu zvolte **Přidat zásuvný modul pro testování webu**.

    ![Zvolte možnost přidat zásuvný modul pro testování webu a vyberte typ.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    V tomto příkladu používáme dvě instance zásuvného modulu Datum čas. Jedna instance je „před 15 minutami“ a druhá „teď“.
2. Otevřete vlastnosti každého zásuvného modulu. Pojmenujte ho a nastavte ho na použití aktuálního času. Pro jeden z nich nastavte Přidat minuty = -15.

    ![Nastavte název, použijte aktuální čas a přidejte minuty.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. V parametrech webového testu použijte {{plug-in name}} pro odkazování na název zásuvného modulu.

    ![V parametru testu použijte {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Teď nahrajte svůj test na portál. Při každém spuštění testu se budou používat dynamické hodnoty.


## <a name="monitor"></a>Zobrazení výsledků testu dostupnosti

Po pár minutách kliknutím na **Aktualizovat** zobrazíte výsledky testů. 

![Souhrnné výsledky na domácím okně](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

Korelační diagram znázorňuje ukázky výsledků testu s podrobnostmi o krocích diagnostického testu. V případě testů obsahujících selhání ukládá testovací modul diagnostické informace. U úspěšných testů se diagnostické informace ukládají pro celou dílčí sadu provedení. Podržením ukazatele na zelených/červených tečkách zobrazíte časové razítko, dobu trvání, umístění a název testu. Klikáním na jednotlivé tečky v korelačním diagramu zobrazíte podrobnosti o výsledcích testu.  

Výběrem určitého testu nebo umístění nebo zkrácením časového období zobrazíte více výsledků v požadovaném časovém období. V Průzkumníku služby Hledání můžete zobrazit výsledky všech provedení a pomocí dotazů služby Analytics můžete na těchto datech spouštět vlastní sestavy.

Vedle nezpracovaných výsledků jsou v Průzkumníku metrik dostupné dvě metriky dostupnosti: 

1. Dostupnost: Procento testů, které proběhly úspěšně, všechny provádění testů. 
2. Doba trvání testu: Průměrná doba trvání u všech provedení testu.

Můžete použít filtry podle názvu a umístění testu a analyzovat trendy určitého testu nebo umístění.

## <a name="edit"></a> Kontrola a úprava testů

Na stránce souhrnu zvolte konkrétní test. Zde můžete zobrazit jeho konkrétní výsledky a upravit ho nebo dočasně zakázat.

![Upravit nebo zakázat webový test](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

Při provádění údržby služby může být vhodné zakázat testy dostupnosti nebo pravidla upozornění, která jsou s nimi spojená. 

## <a name="failures"></a>Pokud se zobrazí chyby
Klikněte na červenou tečku.

![Klikněte na červenou tečku](./media/app-insights-monitor-web-app-availability/open-instance-3.png)

V výsledek testu dostupnosti můžete zobrazit podrobnosti o transakci napříč všemi komponentami. Zde můžete:

* Kontrolovat odpověď přijatou ze serveru.
* Diagnostikujte selhání pomocí telemetrie na straně korelační serveru získané při zpracování test dostupnosti se nezdařilo.
* Protokolovat chyby nebo pracovní položky v Gitu nebo panely Azure kvůli sledování problému. Chyba bude obsahovat odkaz na tuto událost.
* Otevřít výsledek webového testu v sadě Visual Studio.

Přečtěte si další informace o Diagnostika transakcí kompletní prostředí [tady](app-insights-transaction-diagnostics.md).

Klikněte na řádku výjimky a zobrazit podrobnosti o výjimce na straně serveru, který způsobil selhání testu dostupnosti syntetického. Můžete získat také [snímek ladění](app-insights-snapshot-debugger.md) pro bohatší úrovně diagnostiky kódu.

![Diagnostika na straně serveru](./media/app-insights-monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Upozornění na dostupnost
Může mít následující typy pravidel upozornění na dostupnost dat pomocí prostředí upozornění classic:
1. X z Y míst hlášení chyb v časovém období.
2. Procento drops agregační dostupnosti pod prahovou hodnotou
3. Průměrná doba trvání zvýšení nad prahovou hodnotou

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Upozornění na X z Y míst hlášení chyb
X z Y umístění, pravidlo upozornění je povolená ve výchozím nastavení [oznámení pro nové sjednocené prostředí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), když vytvoříte nový test dostupnosti. Je můžete odhlásit výběrem možnosti "klasickém" nebo zakázání pravidla upozornění.

![Vytvoření prostředí](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

**Důležité**: S [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pravidlo upozornění předvolby závažnosti a oznámení s [skupiny akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **musí být** nakonfigurované v prostředí upozornění. Bez následujících kroků budou dostávat jen oznámení na portálu. 

1. Po uložení test dostupnosti, klikněte na nový název testu přejděte do jeho podrobnosti. Klikněte na "Upravit upozornění" ![upravit po uložení](./media/app-insights-monitor-web-app-availability/editaftersave.png)

2. Nastavte úroveň závažnosti požadované, popis pravidla a co je nejdůležitější – skupina akcí, která má předvolby oznámení, které chcete použít pro toto pravidlo upozornění.
![Upravit po uložení](./media/app-insights-monitor-web-app-availability/setactiongroup.png)


> [!NOTE]
> * Konfigurace skupin akcí pro příjem oznámení při aktivaci upozornění podle výše uvedených kroků. Bez tohoto kroku budou dostávat jen na portálu oznámení, když se pravidlo aktivuje.
>

### <a name="alert-on-availability-metrics"></a>Upozornění na metriky dostupnosti
Použití [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), můžete upozornění na segmentovaným agregační dostupnosti a otestovat také doba trvání metriky:

1. Vyberte prostředek Application Insights v prostředí metrik a vyberte metriku dostupnosti:  ![Výběr metriky dostupnosti](./media/app-insights-monitor-web-app-availability/selectmetric.png)

2. Konfigurace výstrah, které možnosti z nabídky se dostanete na nové prostředí kde můžete vybrat konkrétní testy nebo nastavit pravidlo upozornění na umístění. Můžete také nakonfigurovat akce skupiny pro toto pravidlo upozornění tady.
    ![Konfigurace výstrah dostupnosti](./media/app-insights-monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Upozornění na vlastních analytických dotazů
Použití [oznámení pro nové sjednocené](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), může upozornit na [vlastní protokol dotazů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Pomocí vlastních dotazů můžete upozornění na jakékoli libovolného podmínku, která vám pomůže využít vaše nejspolehlivější signál problémy s dostupností. To platí také zejména, pokud odesíláte pomocí sady SDK TrackAvailability výsledky vlastní dostupnosti. 

> [!Tip]
> * Metriky na údaje o dostupnosti zahrnují vlastní dostupnost výsledky, které vám mohou odesílání voláním naší sady SDK TrackAvailability. Můžete použít upozornění na metriky podporu, aby upozornění na výsledky vlastní dostupnosti.
>

## <a name="dealing-with-sign-in"></a>Vyřešení přihlášení
Pokud se uživatelé přihlásí do aplikace, máte několik možností pro simulaci přihlášení, takže můžete otestovat stránky následující po přihlášení. Použitý přístup závisí na typu zabezpečení poskytovaném aplikací.

Ve všech případech musíte v aplikaci vytvořit účet jenom pro účely testování. Pokud je to možné, omezte oprávnění tohoto testovacího účtu, aby webové testy nemohly žádným způsobem ovlivnit skutečné uživatele.

### <a name="simple-username-and-password"></a>Jednoduché uživatelské jméno a heslo
Webový test zaznamenejte obvyklým způsobem. Nejprve odstraňte soubory cookie.

### <a name="saml-authentication"></a>Ověřování SAML
Použijte zásuvný modul SAML, který je pro webové testy dostupný.

### <a name="client-secret"></a>Tajný klíč klienta
Pokud vaše aplikace obsahuje trasu přihlášení, která zahrnuje tajný klíč klienta, použijte ji. Azure Active Directory (AAD) je příkladem služby, která poskytuje přihlašování pomocí tajného klíče klienta. Ve službě AAD je tajným klíčem klienta klíč aplikace.

Tady je ukázkový webový test webové aplikace v Azure pomocí klíče aplikace:

![Ukázkový tajný klíč klienta](./media/app-insights-monitor-web-app-availability/110.png)

1. Získejte token ze služby AAD pomocí tajného klíče klienta (AppKey).
2. Extrahujte nosný token z odpovědi.
3. Pomocí nosného tokenu v autorizační hlavičce zavolejte rozhraní API.

Ujistěte se, jestli je webový test skutečným klientem – to znamená, že má vlastní aplikaci ve službě AAD – a použijte jeho clientId + appkey. Testovaná služba má také vlastní aplikaci ve službě AAD: identifikátor URI appID této aplikace se ve webovém testu projevuje v poli „prostředek“.

### <a name="open-authentication"></a>Otevřené ověřování
Příkladem otevřeného ověřování je přihlašování pomocí účtu Microsoft nebo Google. Velký počet aplikací, které používají OAuth, nabízí alternativní tajný klíč klienta, takže prvním cílem bude prozkoumání této možnosti.

Pokud váš test vyžaduje přihlášení pomocí OAuth, bude obecný postup následující:

* K prozkoumání přenosu dat mezi webovým prohlížečem, webem ověřování a aplikací použijte například nástroj Fiddler.
* Proveďte dvě nebo více přihlášení pomocí různých počítačů nebo prohlížečů nebo v dlouhých intervalech (aby mohla vypršet platnost tokenů).
* Porovnáním různých relací identifikujte token předaný zpět z webu ověřování, který byl následně předán do aplikačního serveru po přihlášení.
* Uložte webový test pomocí sady Visual Studio.
* Parametrizujte tokeny, nastavte parametr při vrácení tokenu z ověřovatele a použijte ho v dotazu na web.
  (Sada Visual Studio se pokusí o parametrizaci testu, ale nebude tokeny parametrizovat správně.)

## <a name="performance-tests"></a>Testy výkonnosti
Na svém webu můžete spustit zátěžový test. Podobně jako v testu dostupnosti můžete z našich bodů po celém světě odeslat buď jednoduché požadavky, nebo vícekrokové požadavky. Na rozdíl od testu dostupnosti se odesílá mnoho požadavků, které simulují několik souběžných uživatelů.

V okně Přehled otevřete **Nastavení**, **Testy výkonnosti**. Při vytváření testu budete vyzváni k připojení k nebo si vytvořte účet Azure DevOps.

Po dokončení testu se zobrazí časy odezvy a míra úspěšnosti.


![Test výkonnosti](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> Pokud chcete sledovat účinky testu výkonnosti, použijte [Live Stream](app-insights-live-stream.md) a [Profiler](app-insights-profiler.md).
>

## <a name="automation"></a>Automation
* [Automatické nastavení testu dostupnosti pomocí skriptů PowerShell](app-insights-powershell.md#add-an-availability-test).
* Nastavení [webhook](../azure-monitor/platform/alerts-webhooks.md), který je volán při vydání výstrahy.

## <a name="qna"></a> NEJČASTĚJŠÍ DOTAZY

* *Web vypadá v pořádku, ale vidím selhání testu? Proč je Application Insights upozorňování mě?*

    * Má váš test "Analýzy závislé požadavky" povolené? Který má za následek striktní kontrolu na prostředky, jako jsou skripty, obrázků apod. Tyto typy selhání nemusí být patrné v prohlížeči. Zkontrolujte všechny image, skripty, šablony stylů a všechny další soubory, které stránka načetla. Pokud některý z nich selže, test se ohlásí jako neúspěšný i v případě, že se hlavní html stránka načte bez problémů. Pokud chcete snížit citlivost testu vůči selháním těchto prostředků, v konfiguraci testu jednoduše zrušte zaškrtnutí možnosti „Analyzovat závislé požadavky“. 

    * Abyste snížili riziko výskytu krátkodobých síťových výkyvů apod., zkontrolujte, zda je v konfiguraci zaškrtnutá možnost „Povolit opakované pokusy pro neúspěšné testy“. Můžete také provádět testy z více umístění a vhodným způsobem upravovat prahovou hodnotu pravidla pro výstrahy. Tím zabráníte zbytečným výstrahám, které by byly způsobeny potížemi v konkrétním umístění.

    * Kliknutím na kteroukoli z červené tečky z prostředí dostupnosti nebo jakékoli neúspěchy dostupnosti z Průzkumníka služby Search můžete zobrazit podrobnosti o proč jsme hlášené chyby. Výsledek testu, spolu s telemetrie na straně serveru korelační (je-li povoleno) by měly pomoci pochopit, proč test selhal. Běžné příčiny přechodné problémy jsou problémy s sítě nebo připojení. 

    * Nebyla časový limit testu? Jsme přerušit testy po 2 minuty. Pokud váš příkaz ping nebo vícekrokový test trvá déle než 2 minuty, jsme budou hlásit jako selhání. Zvažte rozdělení do několika ty, které lze provést v kratších dob trvání testu.

    * Nahlášená všechna místa selhání, nebo jenom některé z nich? Pokud jen některé hlásí chyby, může to být kvůli problémům se síti nebo CDN. Kliknutím na červené tečky znovu, by měly pomoci pochopit, proč umístění hlášené chyby.

* *Můžu nezískaly v e-mailu, když výstraha aktivuje nebo vyřešené nebo obojí?*

    Kontrola konfigurace klasických upozornění pro potvrzení e-mailu je uveden přímo, nebo distribuční seznam, ke kterému jste na je nastavena k přijímání oznámení. Pokud se jedná, zkontrolujte konfiguraci distribučního seznamu abychom potvrdili, že může přijímat externí e-mailů. Také kontrolovat, jestli může správce e-mailu mají konfigurace zásad, které může způsobit potíže.

* *Mi nedošel webhooku oznámení?*

    Zkontrolujte aplikace obdrží oznámení webhooku je k dispozici a úspěšně zpracovává požadavky webhooku. Zobrazit [to](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) Další informace.

* *Občasné selhání testu s chybou porušení protokolu*

    Chyba („Porušení protokolu... Znak CR musí být následován znakem LF.“) značí problémy se serverem (nebo závislostmi). To se stává, když jsou v odpovědi nastavena chybně vytvořená záhlaví. Příčinou mohou být nástroje pro vyrovnávání zatížení nebo CDN. Konkrétně se jedná o to, že některá záhlaví možná nepoužívají znaky CRLF k označení konce řádku. Tím porušují specifikaci protokolu HTTP, což má za následek neúspěšné ověření na úrovni třídy .NET WebRequest. Zkontrolujte odpovědi, zda obsahují chybná záhlaví.
    
    Poznámka: Adresa URL nemusí selhat v prohlížečích, které mají rozvolněné ověřování hlaviček protokolu HTTP. Podrobné vysvětlení tohoto problému najdete v tomto blogovém příspěvku: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Nevidím žádnou telemetrii na straně serveru, která by diagnostikovala neúspěšné testy*
    
    Pokud máte pro aplikaci na straně serveru nastavenou službu Application Insights, může být důvodem to, že právě probíhá [vzorkování](app-insights-sampling.md). Vyberte příslušný výsledek různých dostupnosti.

* *Můžu z webového testu volat kód?*

    Ne. Kroky testu musí být v souboru .webtest. A nemůžete volat jiné webové testy nebo používat smyčky. Existují různé zásuvné moduly, které se vám můžou hodit.

* *Je podporovaný protokol HTTPS?*

    Podporujeme protokoly TLS 1.1 a TLS 1.2.
* *Je nějaký rozdíl mezi „webovými testy“ a „testy dostupnosti“?*

    Významy těchto dvou výrazů jsou zaměnitelné. Testy dostupnosti jsou obecnější označení, které kromě webových testů s více kroky zahrnuje taky jednorázové testy adres URL příkazem Ping.
    
* *Chci používat testy dostupnosti na našem interním serveru, který je spuštěný za bránou firewall.*

    Existují dvě možná řešení:
    
    * Nakonfigurujte bránu firewall, aby povolovala příchozí požadavky z [IP adres našich agentů webového testu](app-insights-ip-addresses.md).
    * Napište vlastní kód, který pravidelně testuje interní server. Spusťte kód na testovacím serveru jako proces na pozadí za vaší bránou firewall. Testovací proces můžete odesílat své výsledky do Application Insights pomocí rozhraní API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) v balíčku Core SDK. To vyžaduje, aby měl váš testovací server odchozí přístup ke koncovému bodu ingestování Application Insights, ale to je mnohem menší riziko zabezpečení než případné povolení příchozích požadavků. Výsledky se nebudou zobrazovat v oknech webových testů dostupnosti, ale zobrazí se jako výsledky dostupnosti ve službě Analytics, ve službě Hledání a v Průzkumníku metrik.

* *Vícekrokový webový test se nepodařilo nahrát.*

    Několik důvodů, proč že k tomu může dojít:
    * Maximální velikost je 300 kB.
    * Smyčky nejsou podporovány.
    * Odkazy na jiné webové testy nejsou podporovány.
    * Zdroje dat nejsou podporovány.

* *Můj vícekrokový test se nedokončil.*

    Existuje limit 100 požadavků na test. Také test bude zastaven, pokud poběží déle než dvě minuty.

* *Jak spustím test s klientskými certifikáty?*

    Tuto možnost nepodporujeme, je nám líto.


## <a name="next"></a>Další kroky
[Prohledávání diagnostických protokolů][diagnostic]

[Řešení potíží][qna]

[IP adresy webových testovacích agentů](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
