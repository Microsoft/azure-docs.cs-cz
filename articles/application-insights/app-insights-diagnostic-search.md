---
title: Pomocí vyhledávání ve službě Azure Application Insights | Microsoft Docs
description: Hledat a filtr nezpracovaná telemetrická data odesílaná vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: c6a94fd1cebff4aa657ad5293715550161003d21
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294380"
---
# <a name="using-search-in-application-insights"></a>Pomocí vyhledávání ve službě Application Insights
Hledání je funkce [Application Insights](app-insights-overview.md) můžete použít k vyhledání a prozkoumejte telemetrie jednotlivých položek, například zobrazení stránky, výjimky nebo k webové požadavky. A můžete zobrazit protokol trasování a události, které mají programového.

(Pro složitější dotazy na data, použijte [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Kde uvidíte hledání?
### <a name="in-the-azure-portal"></a>Na portálu Azure
Diagnostické vyhledávání můžete otevřít explicitně v okně Přehled Application Insights vaší aplikace:

![Otevřete vyhledávání diagnostiky](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Otevře také po kliknutí na tlačítko prostřednictvím některé grafy a položek mřížky. V takovém případě jeho filtry jsou předem nastavené a zaměřit se na typ položky, které jste vybrali. 

Například v okně Přehled není pruhový graf požadavků, které jsou klasifikovány podle doby odezvy. Proklikejte se prostřednictvím rozsahu výkonu zobrazíte seznam jednotlivých požadavků v tomto rozsahu čas odezvy:

![Proklikejte se žádost o výkonu](./media/app-insights-diagnostic-search/07-open-from-filters.png)

Hlavní část diagnostické vyhledávání je seznam položek telemetrie – požadavky na server, vyberte zobrazení, vlastních událostí, které mají programového a tak dále. V horní části seznamu je souhrn graf zobrazující počet událostí v čase.

Klikněte na tlačítko Aktualizovat se získat nové události.

### <a name="in-visual-studio"></a>V nástroji Visual Studio

V sadě Visual Studio je také hledání Application Insights okno. Je velmi užitečné pro zobrazení telemetrie události generované aplikací, kterou ladíte. Ale může také zobrazit události shromážděné z publikované aplikace na portálu Azure.

Otevřete okno hledání v sadě Visual Studio:

![Otevřete Visual Studio Application Insights vyhledávání](./media/app-insights-diagnostic-search/32.png)

V okně vyhledávání má funkce podobné webového portálu:

![Visual Studio Application Insights vyhledávání oken](./media/app-insights-diagnostic-search/34.png)

Na kartě Sledování operaci je k dispozici při otevření žádost nebo zobrazení stránky. ' Operace, je posloupnost událostí, který je přidružen k jediné zobrazení požadavku nebo stránky. Například volání závislostí, výjimek, protokoly trasování a vlastních událostí může být součástí jedné operace. Na kartě Sledování operace zobrazí graficky načasování a dobu trvání tyto události ve vztahu k zobrazení požadavku nebo stránky. 

## <a name="inspect-individual-items"></a>Zkontrolujte jednotlivé položky
Vyberte libovolnou položku telemetrie zobrazíte klíčová pole a související položky. Pokud chcete zobrazit úplnou sadu pole, klikněte na tlačítko "...". 

![Klikněte na novou pracovní položku, upravte pole a pak klikněte na tlačítko OK.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Filtrování typů událostí
Otevřete okno filtru a vyberte typy událostí, které chcete zobrazit. (Pokud chcete později, obnovte filtry, pomocí kterých můžete otevřít okno, klikněte na resetovat.)

![Vyberte filtr a vyberte typy telemetrie](./media/app-insights-diagnostic-search/02-filter-req.png)

Typy událostí jsou:

* **Trasování** - [diagnostické protokoly](app-insights-asp-net-trace-logs.md) včetně TrackTrace, log4Net, NLog a System.Diagnostic.Trace volání.
* **Žádosti o** -požadavky HTTP přijatých vaší serverové aplikace, včetně stránky, skripty, bitové kopie, soubory stylu a data. Tyto události se používají k vytvoření žádosti a odpovědi tabulkách Přehled.
* **Zobrazení stránky** - [Telemetrie odesílaný klientem webové](app-insights-javascript.md)používané k vytváření sestav zobrazení stránky. 
* **Vlastní události** – Pokud jste vložili volání TrackEvent() za účelem [sledování využití](app-insights-api-custom-events-metrics.md), můžete je zde prohledat.
* **Výjimka** – nezachycená [výjimky na serveru](app-insights-asp-net-exceptions.md)a ty, které se můžete přihlásit pomocí TrackException().
* **Závislost** - [volání ze serveru aplikace](app-insights-asp-net-dependencies.md) k jiným službám, jako je například rozhraní REST API nebo databáze a AJAX volá z vaší [kód klienta](app-insights-javascript.md).
* **Dostupnost** -výsledky [testy dostupnosti](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrovat podle hodnoty vlastností
Můžete filtrovat události na hodnotách jejich vlastnosti. Dostupné vlastnosti závisí na typy událostí, které jste vybrali. 

Můžete například vyberte požadavků s kódem konkrétní odezvy. 

![Vlastnost rozbalte a vyberte hodnotu](./media/app-insights-diagnostic-search/03-response500.png)

Výběr žádné hodnoty určité vlastnosti má stejný účinek jako volba všechny hodnoty. Přepíná vypnout filtrování pro tuto vlastnost.

### <a name="narrow-your-search"></a>Upřesněte hledání
Všimněte si, že počty napravo od hodnoty filtru zobrazit, kolik výskytů existuje jsou v aktuální sadě filtrované. 

V tomto příkladu je jasné, že 'RTP – nebo zaměstnancům, vyžádat výsledky ve většině "500" chyb:

![Vlastnost rozbalte a vyberte hodnotu](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Najít události se stejnou vlastnost
Najít všechny položky se stejnou hodnotou vlastnosti:

![Klikněte pravým tlačítkem na vlastnosti](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Vyhledávání dat

> [!NOTE]
> Chcete-li psát složitější dotazy, otevřete [ **Analytics** ](app-insights-analytics-tour.md) z horní části okna vyhledávání.
> 

Můžete vyhledat podmínky v některém z hodnoty vlastností. To je zvlášť užitečné, pokud jste napsali [vlastních událostí](app-insights-api-custom-events-metrics.md) s hodnot vlastností. 

Můžete chtít nastavit čas rozsah vyhledávání za kratší rozsah jsou stejně jako rychlejší. 

![Otevřete vyhledávání diagnostiky](./media/app-insights-diagnostic-search/appinsights-311search.png)

Hledat celá slova, není dílčích řetězců. Použijte speciální znaky, uzavřete do uvozovek.

| řetězec | je *není* nalezena. | ale tyto ji najít |
| --- | --- | --- |
| HomeController.About |Domovská složka<br/>Řadiče<br/>na více systémů | homecontroller<br/>o produktu<br/>"homecontroller.about"|
|Spojené státy|UNI<br/>Tomáš|Spojené<br/>stavy<br/>Spojené státy a<br/>"Spojené státy"

Zde jsou vyhledávacích výrazech, které můžete použít:

| Ukázkový dotaz | Efekt |
| --- | --- |
| `apple` |Najde všechny události v časové rozmezí, jejichž pole použít slovo "apple" |
| `apple AND banana` |Najde události, které obsahují i slova. Použít kapitálu "a", není "a". |
| `apple OR banana`<br/>`apple banana` |Najde události, které obsahují buď aplikace word. Použití "Nebo", ne "nebo".<br/>Krátký formulář. |
| `apple NOT banana` |Najde události, které obsahují jeden word, ale ne na druhou. |



## <a name="sampling"></a>Vzorkování
Pokud vaše aplikace generuje mnoho telemetrie (a používáte 2.0.0-beta3 verze sady SDK technologie ASP.NET nebo novější), modul adaptivního vzorkování automaticky sníží svazku, který je odesílán na portál odesláním pouze reprezentativní části události. Události, které se vztahují ke stejnému požadavku jsou však vybrali nebo nevybrány jako skupina, takže mohou procházet mezi souvisejícími událostmi. 

[Další informace o vzorkování](app-insights-sampling.md).



## <a name="create-work-item"></a>Vytvořit pracovní položku
Chyby v Githubu nebo Visual Studio Team Services můžete vytvořit s podrobnostmi z libovolné položce telemetrie. 

![Klikněte na novou pracovní položku, upravte pole a pak klikněte na tlačítko OK.](./media/app-insights-diagnostic-search/42.png)

To poprvé, zobrazí se výzva pro konfiguraci připojení k účtu Team Services a projekt.

![Zadejte adresu URL vašeho serveru Team Services a název projektu a klikněte na autorizovat](./media/app-insights-diagnostic-search/41.png)

(Můžete také nakonfigurovat na odkaz v okně pracovních položek).

## <a name="save-your-search"></a>Uložení hledání
Když nastavíte všechny filtry, které chcete, můžete vyhledávání uložit jako oblíbenou položku. Pokud pracujete v účet organizace, můžete sdílet s ostatními členy týmu.

![Klikněte na tlačítko Oblíbené položky, nastavte název a klikněte na Uložit](./media/app-insights-diagnostic-search/08-favorite-save.png)

Zobrazíte hledání znovu **přejděte do okna Přehled** a otevřete oblíbených položek:

![Dlaždice k oblíbeným položkám.](./media/app-insights-diagnostic-search/09-favorite-get.png)

Pokud jste uložili s relativní časové rozmezí, znovu otevřít okno obsahuje nejnovější data. Pokud jste uložili s absolutní časové rozmezí, zobrazí stejná data pokaždé, když. (Pokud "Relativní" není dostupná, pokud chcete uložit do oblíbených položek, klikněte na tlačítko časový rozsah v záhlaví a nastavte časový rozsah, který není vlastní rozsah.)

## <a name="send-more-telemetry-to-application-insights"></a>Odesílání další telemetrické Application Insights
Kromě out box telemetrické zprávy odesílané Application Insights SDK můžete:

* Zaznamenat trasování protokolu z vašeho oblíbeného rozhraní protokolování v [.NET](app-insights-asp-net-trace-logs.md) nebo [Java](app-insights-java-trace-logs.md). To znamená, můžete vyhledat pomocí protokolů trasování a korelovat je zobrazení stránky, výjimky a dalších událostí. 
* [Psaní kódu](app-insights-api-custom-events-metrics.md) k odeslání vlastní události, zobrazení stránek a výjimek. 

[Zjistěte, jak odeslat protokoly a vlastní telemetrii Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>MODUL OTÁZKY A ODPOVĚDI
### <a name="limits"></a>Množství dat, které se uchovávají?

Najdete v článku [souhrn omezení](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak lze zobrazit následná data v Moje žádosti serveru?
NÁSLEDNÁ data jsme nemáte protokolu automaticky, ale můžete použít [TrackTrace nebo protokolu volání](app-insights-asp-net-trace-logs.md). Uveďte následná data v parametru zprávy. Nelze filtrovat zprávy stejným způsobem, který můžete filtrovat podle vlastností, ale maximální velikost je delší.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Další kroky
* [Zapisovat složitých dotazů v Analytics](app-insights-analytics-tour.md)
* [Odesílání protokolů a vlastní telemetrii Application Insights](app-insights-asp-net-trace-logs.md)
* [Nastavení dostupnosti a odezvy testů](app-insights-monitor-web-app-availability.md)
* [Řešení potíží](app-insights-troubleshoot-faq.md)
