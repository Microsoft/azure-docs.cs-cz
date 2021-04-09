---
title: Používání služby Search v Azure Application Insights | Microsoft Docs
description: Hledání a filtrování nezpracovaných telemetrie odesílaných vaší webovou aplikací
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: f7c2229a2e497fb052db79d61fcfeff140897798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579540"
---
# <a name="using-search-in-application-insights"></a>Použití vyhledávání v Application Insights

Hledání je funkce [Application Insights](./app-insights-overview.md) , kterou používáte k vyhledání a prohlížení jednotlivých položek telemetrie, jako jsou například zobrazení stránky, výjimky nebo webové požadavky. A můžete zobrazit protokol trasování a události, které jste nastavili.

(Pro složitější dotazy přes vaše data použijte [Analytics](../logs/log-analytics-tutorial.md).)

## <a name="where-do-you-see-search"></a>Kde se zobrazuje hledání?

### <a name="in-the-azure-portal"></a>Na webu Azure Portal

Můžete otevřít vyhledávání diagnostiky z karty Přehled Application Insights vaší aplikace (nachází se na horním panelu) nebo v části prozkoumat na levé straně.

![Karta Hledat](./media/diagnostic-search/view-custom-events.png)

Chcete-li zobrazit seznam položek telemetrie – požadavky serveru, zobrazení stránky, vlastní události, které jste nastavili, a tak dále, přejděte do rozevírací nabídky typy událostí. V horní části seznamu výsledků je souhrnný graf zobrazující počty událostí v průběhu času.

Klikněte na tlačítko z rozevírací nabídky nebo aktualizovat a získejte nové události.

### <a name="in-visual-studio"></a>V nástroji Visual Studio

V aplikaci Visual Studio je k dispozici také okno hledání Application Insights. Je nejužitečnější pro zobrazení událostí telemetrie generovaných aplikací, kterou ladíte. Ale může také zobrazit události shromážděné z publikované aplikace na Azure Portal.

Otevřete okno hledání v aplikaci Visual Studio:

![Visual Studio – otevřít Application Insights Search](./media/diagnostic-search/32.png)

Okno hledání má podobné funkce jako webový portál:

![Okno hledání Application Insights sady Visual Studio](./media/diagnostic-search/34.png)

Karta sledovat operaci je dostupná, když otevřete žádost nebo zobrazení stránky. ' Operation ' je posloupnost událostí, které jsou spojeny s jedním požadavkem nebo zobrazením stránky. Například volání závislostí, výjimky, protokoly trasování a vlastní události mohou být součástí jedné operace. Karta sledovat operaci znázorňuje graficky časování a dobu trvání těchto událostí ve vztahu k žádosti nebo zobrazení stránky.

## <a name="inspect-individual-items"></a>Kontrola jednotlivých položek

Výběrem jakékoli položky telemetrie zobrazíte klíčová pole a související položky.

![Snímek obrazovky samostatné žádosti o závislost](./media/diagnostic-search/telemetry-item.png)

Tím se spustí zobrazení Podrobnosti o koncovém konci transakce.

## <a name="filter-event-types"></a>Filtrovat typy událostí

Otevřete rozevírací nabídku typy událostí a vyberte typy událostí, které chcete zobrazit. (Pokud později chcete filtry obnovit, klikněte na tlačítko Resetovat.)

Typy událostí:

* **Trasování**  -  [Diagnostické protokoly](./asp-net-trace-logs.md) zahrnující volání TrackTrace, Log4Net, nLOG a System. Diagnostics. Trace.
* Žádosti **o** požadavky HTTP přijaté serverovou aplikací, včetně stránek, skriptů, obrázků, souborů stylu a dat. Tyto události se používají k vytvoření grafů s přehledem požadavků a odpovědí.
* **Zobrazení stránky**  -  [Telemetrie odesílaná webovým klientem](./javascript.md), která se používá k vytvoření sestav zobrazení stránky
* **Vlastní událost** – Pokud jste vložili volání do TrackEvent (), aby bylo možné [monitorovat využití](./api-custom-events-metrics.md), můžete je vyhledat tady.
* **Výjimka** – nezachycené [výjimky na serveru](./asp-net-exceptions.md)a ty, které se protokolují pomocí TrackException ().
* **Závislost**  -  [Volání z vaší serverové aplikace](./asp-net-dependencies.md) do jiných služeb, jako jsou rozhraní REST API nebo databáze, a volání AJAX z [kódu klienta](./javascript.md).
* **Dostupnost** – výsledky [testů dostupnosti](./monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrovat hodnoty vlastností

Události můžete filtrovat podle hodnot jejich vlastností. Dostupné vlastnosti závisejí na typech událostí, které jste vybrali. Klikněte na ikonu filtru. ![Ikona filtru](./media/diagnostic-search/filter-icon.png) spustí.

Výběr všech hodnot určité vlastnosti má stejný účinek jako výběr všech hodnot. Odpíná filtrování u této vlastnosti.

Všimněte si, že počty napravo od hodnot filtru ukazují, kolik výskytů je v aktuální filtrované sadě.

## <a name="find-events-with-the-same-property"></a>Najde události se stejnou vlastností.

Chcete-li najít všechny položky se stejnou hodnotou vlastnosti, buď je zadejte do panelu hledání, nebo klikněte na zaškrtávací políčko při prohlížení vlastností na kartě filtr.

![Klikněte na zaškrtávací políčko vlastnosti na kartě filtr.](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Hledání dat

> [!NOTE]
> Pokud chcete v horní části okna hledání zapisovat složitější dotazy, otevřete [**protokoly (Analytics)**](../logs/log-analytics-tutorial.md) .
>

Můžete hledat výrazy v jakékoli z hodnot vlastností. To je užitečné, pokud jste napsali [vlastní události](./api-custom-events-metrics.md) s hodnotami vlastností.

Možná budete chtít nastavit časový rozsah, protože hledání v kratším rozsahu je rychlejší.

![Otevřít vyhledávání diagnostiky](./media/diagnostic-search/search-property.png)

Vyhledejte úplná slova, nikoli podřetězce. Použijte uvozovky k uzavření speciálních znaků.

| Řetězec | *Nenalezeno* | Found |
| --- | --- | --- |
| HomeController. about |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|USA|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Tady jsou hledané výrazy, které můžete použít:

| Ukázkový dotaz | Účinek |
| --- | --- |
| `apple` |Najde všechny události v časovém rozsahu, jehož pole obsahují slovo "Apple". |
| `apple AND banana` <br/>`apple banana` |Najde události, které obsahují obě slova. Použijte velká písmena "a", nikoli "a". <br/>Krátká forma. |
| `apple OR banana` |Najde události, které obsahují buď slovo. Použijte "nebo", nikoli "nebo". |
| `apple NOT banana` |Najde události, které obsahují jedno slovo, ale ne druhý. |

## <a name="sampling"></a>Vzorkování

Pokud vaše aplikace vygeneruje velké množství telemetrie (a používáte sadu SDK ASP.NET verze 2.0.0-beta3 nebo novější), modul adaptivního vzorkování automaticky zmenší svazek, který se pošle na portál, tím, že posílá jenom reprezentativní zlomek událostí. Nicméně události, které se vztahují ke stejné žádosti, se vyberou nebo odliší jako skupina, takže můžete přecházet mezi souvisejícími událostmi.

[Další informace o vzorkování](./sampling.md).

## <a name="create-work-item"></a>Vytvořit pracovní položku

Můžete vytvořit chybu v GitHubu nebo v Azure DevOps s podrobnostmi z jakékoli položky telemetrie.

Kliknutím na libovolnou položku telemetrie a výběrem možnosti **vytvořit pracovní položku** přejdete do zobrazení Podrobnosti o koncových transakcích.

![Klikněte na nová pracovní položka, upravte pole a pak klikněte na OK.](./media/diagnostic-search/work-item.png)

Když to uděláte poprvé, zobrazí se výzva, abyste nakonfigurovali odkaz na vaši organizaci a projekt Azure DevOps.

(Můžete také nakonfigurovat odkaz na kartě pracovní položky.)

## <a name="send-more-telemetry-to-application-insights"></a>Odeslat další telemetrii do Application Insights

Kromě předem připravené telemetrie, kterou odesílá Application Insights SDK, můžete:

* Zachyťte trasování protokolu z oblíbeného protokolovacího rozhraní v [rozhraní .NET](./asp-net-trace-logs.md) nebo [Java](./java-trace-logs.md). To znamená, že můžete procházet trasování protokolu a korelovat je s zobrazeními stránky, výjimkami a dalšími událostmi.
* [Napíšete kód](./api-custom-events-metrics.md) pro odesílání vlastních událostí, zobrazení stránky a výjimek.

[Naučte se, jak odesílat protokoly a vlastní telemetrii do Application Insights](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>OTÁZKA & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Kolik dat se zachová?

Podívejte se na [Souhrn omezení](./pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak se dá v žádostech na server zobrazit POST data?

Data POST neprotokolují automaticky, ale můžete použít [TrackTrace nebo log Calling](./asp-net-trace-logs.md). Umístěte POST data do parametru zprávy. Zprávu nelze filtrovat stejným způsobem, jakým je možné filtrovat vlastnosti, ale omezení velikosti je delší.

## <a name="next-steps"></a><a name="add"></a>Další kroky

* [Zápis složitých dotazů do analýz](../logs/log-analytics-tutorial.md)
* [Odeslání protokolů a vlastní telemetrie do Application Insights](./asp-net-trace-logs.md)
* [Nastavení testů dostupnosti a odezvy](./monitor-web-app-availability.md)
* [Řešení potíží](../faq.md)