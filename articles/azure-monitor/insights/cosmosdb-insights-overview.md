---
title: Monitorování Azure Cosmos DB pomocí Azure Monitor u Cosmos DB (preview)| Dokumenty společnosti Microsoft
description: Tento článek popisuje funkci Azure Monitor for Cosmos DB, která poskytuje vlastníkům Cosmos DB rychlé pochopení problémů s výkonem a využitím s jejich účty CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250683"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Prozkoumejte Azure Monitor pro Azure Cosmos DB (preview)

Azure Monitor pro Azure Cosmos DB (preview) poskytuje zobrazení celkového výkonu, selhání, kapacity a provozního stavu všech prostředků Azure Cosmos DB v jednotném interaktivním prostředí. Tento článek vám pomůže pochopit výhody tohoto nového prostředí monitorování a jak můžete upravit a přizpůsobit prostředí tak, aby vyhovovalo jedinečným potřebám vaší organizace.   

## <a name="introduction"></a>Úvod

Než se ponoříte do zážitku, měli byste pochopit, jak prezentuje a vizualizuje informace. 

Přináší:

* **V horizontálním navýšení měřítku hlediska** prostředků Azure Cosmos DB ve všech vašich předplatných na jednom místě, se schopností selektivně obor pouze ty předplatná a prostředky, které máte zájem o hodnocení.

* **Přejít k podrobnostem analýzy** konkrétní prostředek Azure CosmosDB pomoci diagnostikovat problémy nebo provést podrobnou analýzu podle kategorie – využití, selhání, kapacity a operace. Výběr některé z těchto možností poskytuje podrobné zobrazení příslušných metrik Azure Cosmos DB.  

* **Přizpůsobitelné** – toto prostředí je postavené na šablonách sešitu Azure Monitoru, což vám umožní změnit, jaké metriky se zobrazí, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a pak je uložit do vlastního sešitu. Grafy v sešitech pak můžou být připnuté na řídicí panely Azure.  

Tato funkce nevyžaduje, abyste nic povolit nebo konfigurovat, tyto metriky Azure Cosmos DB jsou shromažďovány ve výchozím nastavení.

>[!NOTE]
>Přístup k této funkci se neúčtuje a bude se vám účtovat jenom základní funkce Azure Monitoru, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [s podrobnostmi o cenách Azure Monitoru.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Zobrazení metrik využití a výkonu pro Azure Cosmos DB

Chcete-li zobrazit využití a výkon účtů úložiště ve všech předplatných, proveďte následující kroky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyhledejte **monitor** a vyberte **možnost Sledovat**.

    ![Vyhledávací pole se slovem "Monitor" a rozbalovací okno, které říká, že služby "Monitor" s obrázkem stylu rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

3. Vyberte **Cosmos DB (náhled).**

    ![Snímek obrazovky sešitu přehledu Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Přehled

V **přehledu**tabulka zobrazuje interaktivní metriky Azure Cosmos DB. Výsledky můžete filtrovat na základě vybraných možností z následujících rozevíracích seznamů:

* **Předplatná** – jsou uvedeny pouze předplatná, které mají prostředek Azure Cosmos DB jsou uvedeny.  

* **Cosmos DB** – můžete vybrat všechny, podmnožinu nebo jeden prostředek Azure Cosmos DB.

* **Časový rozsah** - ve výchozím nastavení zobrazuje poslední 4 hodiny informací na základě odpovídajících provedených výběrů.

Dlaždice čítače pod rozevírací seznamy rozbalit celkový počet prostředků Azure Cosmos DB jsou ve vybraných předplatných. V sešitu, který hlásí metriky transakcí, existuje podmíněné barevné kódování nebo heatmapy pro sloupce. Nejhlubší barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách. 

Výběr rozevírací šipky vedle jednoho z prostředků Azure Cosmos DB odhalí rozdělení metrik výkonu na úrovni jednotlivých kontejnerů databáze:

![Rozšířené rozevírací položky odhalující jednotlivé databázové kontejnery a související rozdělení výkonu](./media/cosmosdb-insights-overview/container-view.png)

Výběrem názvu prostředku Azure Cosmos DB zvýrazněného modře přejdete na výchozí **přehled** pro přidružený účet Azure Cosmos DB. 

### <a name="failures"></a>Selhání

V horní části stránky vyberte **Chyby** a otevře **se** část Sešitu. Zobrazuje celkový počet požadavků s distribucí odpovědí, které tvoří tyto požadavky:

![Snímek obrazovky s chybami s rozdělením podle typu požadavku HTTP](./media/cosmosdb-insights-overview/failures.png)

| kód      |  Popis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z následujících operací REST byla úspěšná: </br>- ZÍSKEJTE na zdroj. </br> - Dát na zdroj. </br> - POST na zdroj. </br> - POST na uložené procedury prostředku k provedení uložené procedury.|
| `201 Created` | Operace POST pro vytvoření prostředku je úspěšná. |
| `404 Not Found` | Operace se pokouší o činnost s prostředkem, který již neexistuje. Například prostředek již byl odstraněn. |

Úplný seznam stavových kódů najděte v [článku o stavu http kódu Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacita

V horní části stránky vyberte **Kapacita** a otevře se část **Kapacity** šablony sešitu. Ukazuje, kolik dokumentů máte, nárůst dokumentů v průběhu času, využití dat a celkové množství dostupného úložiště, které vám zbylo.  To lze použít k identifikaci potenciální problémy úložiště a využití dat.

![Sešit kapacity](./media/cosmosdb-insights-overview/capacity.png) 

Stejně jako u sešitu s přehledem výběru vedle prostředku Azure Cosmos DB ve sloupci **Předplatné** odhalí rozdělení podle jednotlivých kontejnerů, které tvoří databázi.

### <a name="operations"></a>Provoz 

V horní části stránky vyberte **Operace** a otevře se část **Operace** šablony sešitu. To vám dává možnost vidět vaše požadavky v členění podle typu žádostí. 

Takže v níže uvedeném `eastus-billingint` příkladu vidíte, že je převážně příjem požadavků na čtení, ale s malým počtem upsert a vytvářet požadavky. Vzhledem `westeurope-billingint` k tomu, je jen pro čtení z hlediska požadavku, alespoň za poslední čtyři hodiny, že sešit je aktuálně vymezena prostřednictvím jeho parametr časového rozsahu.

![Operační sešit](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Připnutí, export a rozbalení

Na [ovládací panel Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) můžete připnout kteroukoli z částí metriky tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Příklad připnutí metrické sekce k řídicímu panelu](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu Aplikace Excel, vyberte ikonu šipky dolů vlevo od ikony připínáčku.

![Ikona exportu sešitu](./media/cosmosdb-insights-overview/export.png)

Pokud chcete rozbalit nebo sbalit všechna rozevírací zobrazení v sešitu, vyberte ikonu rozbalení vlevo od ikony exportu:

![Rozbalit ikonu sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Přizpůsobení Azure Monitoru pro Azure Cosmos DB (preview)

Vzhledem k tomu, že toto prostředí je postavené na šablonách sešitu Azure Monitoru, máte možnost **přizpůsobit** > **úpravy** a **uložit** kopii upravené verze do vlastního sešitu. 

![Přizpůsobit pruh](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají do skupiny prostředků, a to buď v části **Moje sestavy,** která je pro vás soukromá, nebo v části **Sdílené sestavy,** která je přístupná všem uživatelům s přístupem ke skupině prostředků. Po uložení vlastního sešitu je třeba přejít do galerie sešitů a spustit jej.

![Spuštění galerie sešitů z panelu příkazů](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [upozornění na metriky](../platform/alerts-metric.md) a [oznámení o stavu služeb](../../service-health/alerts-activity-log-service-notifications.md) a nastavte automatické upozorňování, které pomůže při zjišťování problémů.

* Seznamte se scénáři, které jsou navržené tak, aby podporovaly, jak vytvářet nové a přizpůsobit existující sestavy a další informace v recenzi [Na vytvoření interaktivních sestav pomocí sešitů Azure Monitoru](../app/usage-workbooks.md).
