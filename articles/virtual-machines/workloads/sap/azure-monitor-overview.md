---
title: Azure Monitor pro přehled a architekturu řešení SAP | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se řešení Azure monitor pro SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 411a95154c9ca36595dff4472e9ab8e1ae8a767e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571364"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Řešení Azure monitor pro SAP (Preview)

## <a name="overview"></a>Přehled

Azure Monitor pro řešení SAP je produkt pro zákazníky nativní pro Azure, ve kterém je spuštěný systém SAP krajiny v Azure. Tento produkt spolupracuje s [SAP v azure Virtual Machines](./hana-get-started.md) i [SAP ve velkých instancích Azure](./hana-overview-architecture.md).
Díky Azure Monitor pro řešení SAP můžou zákazníci shromažďovat data telemetrie z infrastruktury a databází Azure v jednom centrálním umístění a vizuálně korelovat data telemetrie pro rychlejší řešení potíží.

K dispozici jsou Azure Monitor pro řešení SAP prostřednictvím Azure Marketplace. Nabízí jednoduché a intuitivní prostředí pro instalaci a k nasazení prostředku pro Azure Monitor pro řešení SAP (označované jako **prostředek sledování SAP**) stačí jenom několik kliknutí.

Zákazníci mohou monitorovat různé součásti technologie SAP na šířku, jako je například Azure Virtual Machines, cluster s vysokou dostupností, databáze SAP HANA atd., a to přidáním odpovídajícího **poskytovatele** pro danou součást.

Podporovaná infrastruktura:

- Virtuální počítač Azure
- Velká instance Azure

Podporované databáze:
- Databáze SAP HANA
- Microsoft SQL Server

Azure Monitor pro řešení SAP používá výkon stávajících funkcí [Azure monitor](../../../azure-monitor/overview.md) , jako je například Log Analytics a [sešity](../../../azure-monitor/visualize/workbooks-overview.md) , k zajištění dalších možností monitorování. Zákazníci můžou vytvářet [vlastní vizualizace](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) úpravou výchozích sešitů poskytovaných Azure monitor pro řešení SAP, psát [vlastní dotazy](../../../azure-monitor/logs/log-analytics-tutorial.md) a vytvářet [vlastní výstrahy](../../../azure-monitor/alerts/tutorial-response.md) pomocí Azure Log Analytics Workspace, využívat výhod [flexibilního období uchovávání](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) a připojovat data monitorování k systému lístků.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Jaká data Azure Monitor pro shromažďování řešení SAP?

Shromažďování dat v Azure Monitor pro řešení SAP závisí na poskytovatelích nakonfigurovaných zákazníky. Během Public Preview se shromažďují následující data.

Pacemaker telemetrie clusteru s vysokou dostupností:
- Stav zařízení, prostředku a SBD
- Omezení umístění Pacemaker
- Hlasy kvora a stav vyzvánění
- [Ostatní](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetrie SAP HANA:
- Využití procesoru, paměti, disku a sítě
- Replikace systému HANA (HSR)
- Záloha HANA
- Stav hostitele HANA
- Role serveru Index Server a názvového serveru

Telemetrie Microsoft SQL serveru:
- Využití procesoru, paměti, disku
- Název hostitele, název instance SQL, ID systému SAP
- Dávkování požadavků, kompilací a očekávané životního cyklu stránky v čase
- 10 nejčastějších příkazů SQL v průběhu času
- První 12 největší tabulka v systému SAP
- Problémy zaznamenané v protokolu chyb SQL Server
- Blokování procesů a statistiky SQL čekání v průběhu času

Telemetrie operačního systému (Linux) 
- Využití procesoru, počet rozvětvení, spuštěné a Blokované procesy. 
- Využití paměti a distribuce mezi využitými, uloženými do mezipaměti, do vyrovnávací paměti. 
- Vyměňte využití, stránkování a míru prohození. 
- Využití systému souborů, Počet přečtených a zapsaných bajtů na blokové zařízení. 
- Latence čtení a zápisu na blokové zařízení. 
- Průběžný počet vstupů a výstupů, trvalá paměť pro čtení a zápis. 
- Příchozí/odchozí síťové pakety, bajtů v/v sítě 

## <a name="data-sharing-with-microsoft"></a>Sdílení dat s Microsoftem

Azure Monitor pro řešení SAP shromažďuje systémová metadata, která poskytují vylepšenou podporu pro naše SAP na zákaznících Azure. Neshromažďují se žádné PII/EUII.
Zákazníci můžou povolit sdílení dat s Microsoftem v době vytváření Azure Monitor pro prostředek řešení SAP tím, že v rozevíracím seznamu vyberete *sdílet* .
Důrazně doporučujeme, aby zákazníci povolili sdílení dat, protože poskytnou podpoře a technickým týmům společnosti Microsoft Další informace o prostředí zákazníka a poskytují vylepšenou podporu pro naše důležité SAP pro zákazníky Azure.

## <a name="architecture-overview"></a>Přehled architektury

Následující diagram vysvětluje, jak Azure Monitor pro řešení SAP shromažďuje telemetrii z databáze SAP HANA. Architektura je nezávisláá na to, jestli je SAP HANA nasazená v Azure Virtual Machines nebo ve velkých instancích Azure.

![Azure Monitor pro architekturu řešení SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

Klíčové součásti architektury jsou:
- Azure Portal – výchozí bod pro zákazníky. Zákazníci můžou přejít na web Marketplace v rámci Azure Portal a vyhledat Azure Monitor řešení SAP.
- Azure Monitor pro prostředek řešení SAP – cílové místo pro zákazníky, kteří si můžou zobrazit telemetrii monitorování
- Spravovaná skupina prostředků – automaticky nasazená jako součást Azure Monitor pro nasazení prostředků řešení SAP. Prostředky nasazené v rámci spravované skupiny prostředků jsou užitečné při shromažďování telemetrie. Nasazené klíčové prostředky a jejich účel jsou:
   - Virtuální počítač Azure: taky se označuje jako *virtuální počítač kolektoru*. Toto je Standard_B2ms virtuální počítač. Hlavním účelem tohoto virtuálního počítače je hostování *datové části monitorování*. Datová část monitorování označuje logiku shromažďování telemetrie ze zdrojových systémů a přenos shromážděných dat do monitorovacího rozhraní. Ve výše uvedeném diagramu obsahuje datová část monitorování logiku pro připojení k SAP HANA databázi přes port SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): Tento prostředek je nasazen pro bezpečné uchovávání přihlašovacích údajů databáze SAP Hana a ukládání informací o [poskytovatelích](./azure-monitor-providers.md).
   - Log Analytics pracovní prostor: cíl, ve kterém se nacházejí data telemetrie.
      - Vizualizace je postavená na telemetrie v Log Analytics pomocí [sešitů Azure](../../../azure-monitor/visualize/workbooks-overview.md). Zákazníci si můžou vizualizaci přizpůsobit. Zákazníci mohou v sešitech také připnout své sešity nebo konkrétní vizualizaci do řídicího panelu Azure pro funkci AutoRefresh s nejnižším rozlišením 30 minut.
      - Zákazníci můžou použít svůj stávající pracovní prostor v rámci stejného předplatného jako prostředek sledování SAP, a to výběrem této možnosti v době nasazení.
      - Zákazníci můžou pomocí Kusto dotazovacího jazyka (KQL) spouštět [dotazy](../../../azure-monitor/logs/log-query-overview.md) na nezpracované tabulky v pracovním prostoru Log Analytics. Podívejte se na *vlastní protokoly*.

> [!Note]
> Zákazníci zodpovídají za opravy a údržbu virtuálních počítačů, které jsou nasazené ve spravované skupině prostředků.

> [!Tip]
> Zákazníci se můžou rozhodnout použít stávající pracovní prostor Log Analytics pro shromažďování telemetrie, pokud je nasazený v rámci stejného předplatného Azure jako prostředek pro Azure Monitor řešení SAP.

### <a name="architecture-highlights"></a>Nejdůležitější architektura

V této části najdete hlavní nejdůležitější funkce architektury:
 - **Více instancí** – zákazníci mohou vytvořit monitorování pro více instancí daného typu komponenty (například databáze Hana, cluster s vysokou dostupností, Microsoft SQL Server) v rámci více identifikátorů SID SAP v rámci virtuální sítě s jedním prostředkem Azure monitor pro řešení SAP.
 - **Vícenásobný zprostředkovatel** – na výše uvedeném diagramu architektury se jako příklad zobrazuje poskytovatel SAP HANA. Podobně zákazníci můžou nakonfigurovat další poskytovatele pro odpovídající komponenty (například databáze HANA, cluster s vysokou dostupností, Microsoft SQL Server) a shromažďovat data z těchto součástí.
 - **Open Source** – zdrojový kód Azure monitor pro řešení SAP je k dispozici na [GitHubu](https://github.com/Azure/AzureMonitorForSAPSolutions). Zákazníci mohou odkazovat na kód poskytovatele a získat další informace o produktu, přispívání nebo sdílení zpětné vazby.
 - **Rozšiřitelná architektura dotazů** – dotazy SQL pro shromažďování dat telemetrie jsou zapisovány ve formátu [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Další dotazy SQL na shromažďování dalších dat telemetrie lze snadno přidat. Zákazníci můžou požádat o konkrétní data telemetrie, která se mají přidat do Azure Monitor pro řešení SAP, a to tak, že na konci tohoto dokumentu přejdou zpětnou vazbu prostřednictvím odkazu nebo se obrátí na svůj obchodní tým.

## <a name="pricing"></a>Ceny
Azure Monitor pro řešení SAP je bezplatný produkt (bez licenčního poplatku). Zákazníci zodpovídají za hrazení nákladů na základní komponenty ve spravované skupině prostředků.

## <a name="next-steps"></a>Další kroky

Přečtěte si o poskytovatelích a vytvořte svoje první Azure Monitor pro prostředek řešení SAP.
 - Další informace o [poskytovatelích](./azure-monitor-providers.md)
 - [Nasazení Azure Monitor pro řešení SAP pomocí Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Máte dotazy týkající se Azure Monitor pro řešení SAP? Podívejte se na část [Nejčastější dotazy](./azure-monitor-faq.md) .
