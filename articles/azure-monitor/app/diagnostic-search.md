---
title: Použití vyhledávání v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Vyhledávání a filtrování nezpracovaných telemetrických dat odeslaných webovou aplikací.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275981"
---
# <a name="using-search-in-application-insights"></a>Použití vyhledávání v přehledech aplikací

Hledání je funkce [Application Insights,](../../azure-monitor/app/app-insights-overview.md) kterou používáte k vyhledání a prozkoumání jednotlivých položek telemetrie, jako jsou zobrazení stránek, výjimky nebo webové požadavky. A můžete zobrazit trasování protokolu a události, které jste kódovali.

(Pro složitější dotazy týkající se dat použijte [službu Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Kde vidíte vyhledávání?

### <a name="in-the-azure-portal"></a>Na webu Azure Portal

Diagnostické vyhledávání můžete otevřít na kartě Přehled přehledu aplikací aplikace (umístěné na horním panelu) nebo v části Prozkoumat na levé straně.

![Karta Hledat](./media/diagnostic-search/view-custom-events.png)

Přejděte do rozevírací nabídky Typy událostí a zobrazte seznam položek telemetrie – požadavky serveru, zobrazení stránek, vlastní události, které jste kódovali a tak dále. V horní části seznamu výsledků je souhrnný graf zobrazující počty událostí v čase.

Kliknutím na rozbalovací nabídku vypadněte nebo refreshem získejte nové události.

### <a name="in-visual-studio"></a>V nástroji Visual Studio

V sadě Visual Studio je také okno hledání přehledů aplikací. Nejužitečnější je pro zobrazení telemetrických událostí generovaných aplikací, kterou ladíte. Ale může také zobrazit události shromážděné z publikované aplikace na webu Azure Portal.

Otevřete okno Hledat v Sadě Visual Studio:

![Visual Studio otevře hledání Přehledů aplikací](./media/diagnostic-search/32.png)

Okno Hledat má funkce podobné webovému portálu:

![Okno hledání Přehledy aplikací sady Visual Studio](./media/diagnostic-search/34.png)

Karta Sledování operace je k dispozici při otevření požadavku nebo zobrazení stránky. "Operace" je posloupnost událostí, která je přidružena k jednomu požadavku nebo zobrazení stránky. Například volání závislostí, výjimky, protokoly trasování a vlastní události mohou být součástí jedné operace. Karta Sledování operace zobrazuje graficky časování a trvání těchto událostí ve vztahu k požadavku nebo zobrazení stránky.

## <a name="inspect-individual-items"></a>Kontrola jednotlivých položek

Vyberte libovolnou položku telemetrie, chcete-li zobrazit klíčová pole a související položky.

![Snímek obrazovky s individuálním požadavkem na závislost](./media/diagnostic-search/telemetry-item.png)

Tím se spustí zobrazení podrobností o transakcích od konce.

## <a name="filter-event-types"></a>Filtrování typů událostí

Otevřete rozevírací nabídku typů událostí a zvolte typy událostí, které chcete zobrazit. (Pokud později chcete filtry obnovit, klikněte na Obnovit.)

Typy událostí jsou:

* **Protokoly** - [diagnostiky](../../azure-monitor/app/asp-net-trace-logs.md) trasování včetně volání TrackTrace, log4Net, NLog a System.Diagnostic.Trace.
* **Požadavek** – požadavky HTTP přijaté serverovou aplikací, včetně stránek, skriptů, obrázků, souborů stylů a dat. Tyto události se používají k vytvoření grafů přehledu požadavků a odpovědí.
* **Page View** - Telemetrie zobrazení stránky[odeslaná webovým klientem](../../azure-monitor/app/javascript.md)slouží k vytváření sestav zobrazení stránky.
* **Vlastní událost** - Pokud jste vložili volání TrackEvent() za účelem [sledování využití](../../azure-monitor/app/api-custom-events-metrics.md), můžete je prohledat zde.
* **Výjimka** - [Nezachycené výjimky na serveru](../../azure-monitor/app/asp-net-exceptions.md)a ty, které protokolujete pomocí TrackException().
* **Volání závislostí** - [ze serverové aplikace](../../azure-monitor/app/asp-net-dependencies.md) do jiných služeb, jako jsou rozhraní REST API nebo databáze a volání AJAX z [klientského kódu](../../azure-monitor/app/javascript.md).
* **Dostupnost** - Výsledky [testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrování podle hodnot vlastností

Události můžete filtrovat podle hodnot jejich vlastností. Dostupné vlastnosti závisí na vybraných typech událostí. Klikněte na ikonu filtru ![Ikona filtru](./media/diagnostic-search/filter-icon.png) pro spuštění.

Výběr žádné hodnoty určité vlastnosti má stejný účinek jako výběr všech hodnot. Vypne filtrování na této vlastnosti.

Všimněte si, že počty napravo od hodnot filtru ukazují, kolik výskytů je v aktuální filtrované sadě.

## <a name="find-events-with-the-same-property"></a>Hledání událostí se stejnou vlastností

Chcete-li najít všechny položky se stejnou hodnotou vlastnosti, zadejte je do vyhledávacího panelu nebo klepněte na zaškrtávací políčko při procházení vlastností na kartě filtru.

![Klikněte na zaškrtávací políčko vlastnosti na kartě filtru.](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Vyhledávání dat

> [!NOTE]
> Chcete-li psát složitější dotazy, otevřete [**protokoly (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) z horní části okna Hledat.
>

Výrazy můžete vyhledat v libovolné hodnotě vlastností. To je užitečné, pokud jste napsali [vlastní události](../../azure-monitor/app/api-custom-events-metrics.md) s hodnotami vlastností.

Můžete chtít nastavit časový rozsah, protože vyhledávání v kratším rozsahu jsou rychlejší.

![Otevřené diagnostické hledání](./media/diagnostic-search/search-property.png)

Vyhledejte úplná slova, nikoli podřetězce. Pomocí uvozovek uzavřete speciální znaky.

| Řetězec | *Nebyl* nalezen. | Found |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Spojené státy|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Zde jsou vyhledávací výrazy, které můžete použít:

| Ukázkový dotaz | Účinek |
| --- | --- |
| `apple` |Najít všechny události v časovém rozsahu, jehož pole obsahují slovo "jablko" |
| `apple AND banana` <br/>`apple banana` |Najít události, které obsahují obě slova. Použijte velké "A", ne "a". <br/>Krátký tvar. |
| `apple OR banana` |Najít události, které obsahují jedno slovo. Použijte "OR", ne "nebo". |
| `apple NOT banana` |Najít události, které obsahují jedno slovo, ale ne druhé. |

## <a name="sampling"></a>Vzorkování

Pokud vaše aplikace generuje velké množství telemetrie (a používáte ASP.NET SDK verze 2.0.0-beta3 nebo novější), modul adaptivnívzorkování automaticky sníží objem, který je odeslán na portál odesláním pouze reprezentativní zlomek událostí. Události, které souvisejí se stejným požadavkem, jsou však vybrány nebo odznačeny jako skupina, takže můžete procházet mezi souvisejícími událostmi.

[Další informace o vzorkování](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Vytvořit pracovní položku

Můžete vytvořit chybu v GitHub nebo Azure DevOps s podrobnostmi z libovolné položky telemetrie.

Přejděte do zobrazení podrobností transakce od konce kliknutím na libovolnou položku telemetrie a vyberte **vytvořit pracovní položku**.

![Klikněte na Nová pracovní položka, upravte pole a potom klikněte na OK.](./media/diagnostic-search/work-item.png)

Při prvním spuštění budete vyzváni ke konfiguraci propojení s organizací a projektem Azure DevOps.

(Odkaz můžete také nakonfigurovat na kartě Pracovní položky.)

## <a name="send-more-telemetry-to-application-insights"></a>Odeslání další telemetrie do Application Insights

Kromě předem vypočitanové telemetrie odeslané sadou Application Insights SDK můžete:

* Zachyťte trasování protokolů z vašeho oblíbeného rámce protokolování v [rozhraní .NET](../../azure-monitor/app/asp-net-trace-logs.md) nebo [Java](../../azure-monitor/app/java-trace-logs.md). To znamená, že můžete prohledávat trasování protokolu a korelovat je se zobrazeními stránek, výjimkami a dalšími událostmi.
* [Napište kód](../../azure-monitor/app/api-custom-events-metrics.md) pro odeslání vlastních událostí, zobrazení stránek a výjimek.

[Zjistěte, jak odesílat protokoly a vlastní telemetrii do Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Kolik dat je uchováno?

Viz [souhrn Omezení](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak lze zobrazit data POST v mých serverových požadavcích?

Nezaznamenáváme data POST automaticky, ale můžete použít [TrackTrace nebo protokolovat hovory](../../azure-monitor/app/asp-net-trace-logs.md). Vložte data POST do parametru zprávy. Zprávu nelze filtrovat stejným způsobem, jakým můžete filtrovat vlastnosti, ale limit velikosti je delší.

## <a name="next-steps"></a><a name="add"></a>Další kroky

* [Psaní složitých dotazů v Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Odesílání protokolů a vlastní telemetrie do application insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Nastavení testů dostupnosti a odezvy](../../azure-monitor/app/monitor-web-app-availability.md)
* [Řešení potíží](../../azure-monitor/app/troubleshoot-faq.md)
