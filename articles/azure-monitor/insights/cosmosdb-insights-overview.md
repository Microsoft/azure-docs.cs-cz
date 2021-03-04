---
title: Monitorovat Azure Cosmos DB s Azure Monitor pro Cosmos DB | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro funkci Cosmos DB, která poskytuje Cosmos DBm vlastníkům rychlé porozumění problémům s výkonem a využitím jejich účtů CosmosDB.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: d88bf65f1bd94e29bd9f60f5597d655f0040623b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725758"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Prozkoumejte Azure Monitor pro Azure Cosmos DB

Azure Monitor pro Azure Cosmos DB poskytuje přehled o celkovém výkonu, selháních, kapacitě a provozním stavu všech vašich Azure Cosmos DBch prostředků v jednotném interaktivním prostředí. Tento článek vám pomůže pochopit výhody tohoto nového prostředí pro monitorování a jak můžete upravit a přizpůsobit prostředí tak, aby vyhovovalo jedinečným potřebám vaší organizace.   

## <a name="introduction"></a>Úvod

Předtím, než se začnete do prostředí, byste měli pochopit, jak prezentuje a vizualizují informace. 

Poskytuje:

* V **perspektivě škály** svých Azure Cosmos DBch prostředků napříč všemi vašimi předplatnými v jednom místě s možností selektivně přidružit jenom k předplatným a prostředkům, které vás zajímají.

* Procházejte **analýzu** konkrétního prostředku Azure CosmosDB, která vám pomůžou diagnostikovat problémy nebo provádět podrobnou analýzu pomocí kategorií, selhání, kapacity a operací. Výběrem jedné z těchto možností získáte Podrobný pohled na relevantní metriky Azure Cosmos DB.  

* **Přizpůsobitelné** – toto prostředí je postavené na šablonách sešitu Azure monitor, což vám umožní změnit, jaké metriky se zobrazí, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a pak je Uložit do vlastního sešitu. Grafy v sešitech je pak možné připnout na řídicí panely Azure.  

Tato funkce nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tyto Azure Cosmos DB metriky se ve výchozím nastavení shromažďují.

>[!NOTE]
>Pro přístup k této funkci se neplatí žádné poplatky a účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Zobrazit metriky využití a výkonu pro Azure Cosmos DB

Pokud chcete zobrazit využití a výkon účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Vyhledejte **monitorování** a vyberte **monitor**.

    ![Vyhledávací pole se slovem "monitor" a rozevírací seznam, který říká službám "monitor" s obrázkem stylu rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

3. Vyberte **Cosmos DB**.

    ![Snímek obrazovky s přehledem Cosmos DBového sešitu](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Přehled

V **přehledu** zobrazuje tabulka interaktivní metriky Azure Cosmos DB. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* V seznamu **předplatných** jsou uvedena pouze předplatná, která mají prostředek Azure Cosmos DB.  

* **Cosmos DB** – můžete vybrat vše, podmnožinu nebo jeden prostředek Azure Cosmos DB.

* **Časový rozsah** – ve výchozím nastavení zobrazí poslední 4 hodiny informací na základě odpovídajících výběrů.

Dlaždice počítadla v rozevíracích seznamech shrnuje celkový počet Azure Cosmos DBch prostředků ve vybraných předplatných. Pro sloupce v sešitu jsou podmíněné barevné kódování nebo Heat mapy, které vykazují metriky transakcí. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách. 

Když vyberete šipku rozevíracího seznamu vedle jednoho z Azure Cosmos DB prostředků, zobrazí se rozpis metrik výkonu na úrovni jednotlivého kontejneru databáze:

![Rozšířené rozevírací seznam odhalí jednotlivé kontejnery databáze a související rozpis výkonu.](./media/cosmosdb-insights-overview/container-view.png)

Když vyberete název prostředku Azure Cosmos DB zvýrazněný modře, přejdete k výchozímu **přehledu** pro přidružený účet Azure Cosmos DB. 

### <a name="failures"></a>Selhání

V horní části stránky vyberte **selhání** a otevře se část **selhání** šablony sešitu. Zobrazuje celkový počet požadavků s distribucí odpovědí, které tvoří tyto požadavky:

![Snímek obrazovky s chybami s rozpisem podle typu požadavku HTTP](./media/cosmosdb-insights-overview/failures.png)

| Kód |  Popis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z následujících operací REST byla úspěšná: </br>– ZÍSKAT u prostředku. </br> -PUT do prostředku. </br> – ZVEŘEJNĚNÍ na prostředku. </br> -POST v prostředku uložené procedury a spusťte uloženou proceduru.|
| `201 Created` | Operace POST pro vytvoření prostředku je úspěšná. |
| `404 Not Found` | Operace se pokouší jednat o prostředek, který již neexistuje. Prostředek mohl být například již odstraněn. |

Úplný seznam stavových kódů najdete v [článku Azure Cosmos DB stavový kód HTTP](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacita

V horní části stránky vyberte **kapacitu** a v šabloně sešitu se otevře část **kapacita** . Ukazuje, kolik dokumentů máte, váš dokument roste v čase, využití dat a celkově dostupné úložiště, které jste opustili.  Dá se využít k identifikaci potenciálních problémů s úložištěm a využitím dat.

![Pracovní sešit kapacity](./media/cosmosdb-insights-overview/capacity.png) 

Stejně jako u sešitu s přehledem se po výběru rozevíracího seznamu vedle Azure Cosmos DB prostředku ve sloupci **odběr** zobrazí rozpis jednotlivých kontejnerů, které tvoří databázi.

### <a name="operations"></a>Operace

V horní části stránky vyberte **operace** a otevře se část **operace** v šabloně sešitu. Dává vám možnost Zobrazit vaše požadavky rozdělené podle typu vámi provedených požadavků.

Takže v příkladu níže vidíte, že se v rámci `eastus-billingint` toho dostávají požadavky na čtení, ale s malým počtem Upsert a požadavky na vytvoření. Vzhledem k tomu `westeurope-billingint` , že je k dispozici jen pro čtení z perspektivy žádosti, alespoň během posledních čtyř hodin, po které je sešit aktuálně vymezený prostřednictvím parametru časového rozsahu.

![Sešit operací](./media/cosmosdb-insights-overview/operation.png)

## <a name="view-from-an-azure-cosmos-db-resource"></a>Zobrazit z prostředku Azure Cosmos DB

1. Vyhledejte nebo vyberte libovolný ze stávajících účtů Azure Cosmos DB.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-search.png" alt-text="Vyhledejte Azure Cosmos DB." border="true":::

2. Po přechodu na účet Azure Cosmos DB v části monitorování vyberte **přehledy (Preview)** nebo **sešity** , abyste mohli provádět další analýzu propustnosti, požadavků, úložiště, dostupnosti, latence, správy systémů a účtů.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-overview.png" alt-text="Přehled Cosmos DB Insights" border="true":::

### <a name="time-range"></a>Časové rozmezí

Ve výchozím nastavení se v poli **časový rozsah** zobrazuje data za **posledních 24 hodin**. Časový rozsah můžete upravit tak, aby se data zobrazovala kdekoli za posledních 5 minut až po dobu posledních sedmi dnů. Selektor časového rozsahu zahrnuje také **vlastní** režim, který umožňuje zadat počáteční a koncové datum pro zobrazení vlastního časového rámce na základě dostupných dat pro vybraný účet.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-time-range.png" alt-text="Cosmos DB časový rozsah." border="true":::

### <a name="insights-overview"></a>Přehled Insights

Karta **Přehled** poskytuje nejběžnější metriky pro vybraný Azure Cosmos DB účet, včetně:

* Požadavky celkem
* Neúspěšné žádosti (429s)
* Normalizovaná spotřeba RU (max)
* Využití indexu & dat
* Cosmos DB metriky účtu podle kolekce

**Celkový počet požadavků:** Tento graf poskytuje zobrazení celkových požadavků na účet rozdělený podle stavového kódu. Jednotky v dolní části grafu jsou součtem celkových požadavků za období.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-total-requests.png" alt-text="Graf všech požadavků Cosmos DB celkem." border="true":::

**Neúspěšné žádosti (429s)**: Tento graf poskytuje zobrazení neúspěšných žádostí se stavovým kódem 429. Jednotky v dolní části grafu jsou součtem celkových neúspěšných žádostí za dané období.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-429.png" alt-text="Graf neúspěšných žádostí Cosmos DB." border="true":::

**Normalizovaná spotřeba ru (max)**: Tento graf poskytuje maximální procento mezi 0-100% normalizovaných jednotek ru spotřeby v zadaném období.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-normalized-ru.png" alt-text="Cosmos DB normalizované spotřeby RU." border="true":::

## <a name="pin-export-and-expand"></a>Připnout, exportovat a rozbalit

Libovolný oddíl metriky můžete připnout na [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md) tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Připnout oddíl metriky na příklad řídicího panelu](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu aplikace Excel, vyberte ikonu šipky dolů nalevo od ikony připínáčku.

![Ikona exportovat sešit](./media/cosmosdb-insights-overview/export.png)

Chcete-li rozbalit nebo sbalit všechna rozevírací zobrazení v sešitu, vyberte ikonu rozbalení vlevo od ikony exportu:

![Ikona rozbalení sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Přizpůsobení Azure Monitor pro Azure Cosmos DB

Vzhledem k tomu, že toto prostředí je postavené na šablonách sešitu Azure monitor, máte možnost **přizpůsobit**  >  **Úpravy** a **Uložit** kopii změněné verze do vlastního sešitu. 

![Přizpůsobit pruh](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají v rámci skupiny prostředků, a to buď v části **Mé sestavy** , která je pro vás nebo ve **sdílených sestavách** přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Spustit galerii sešitů z panelu příkazů](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokyny k řešení potíží najdete v článku věnovaném [řešení potíží](troubleshoot-workbooks.md)s vyhrazenými sešity na základě sešitu.

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [Upozornění metrik](../alerts/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md) , abyste nastavili automatizované upozorňování na pomoc při zjišťování problémů.

* Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../visualize/workbooks-overview.md).
