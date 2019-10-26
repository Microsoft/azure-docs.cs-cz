---
title: Správa a aktualizace mezipaměti HPC Azure (Preview)
description: Jak spravovat a aktualizovat mezipaměť HPC Azure pomocí Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: rohogue
ms.openlocfilehash: b5607f1d97e4e240a86ba8e7800af7cb21083217
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952014"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Správa mezipaměti z Azure Portal

Stránka s přehledem mezipaměti v Azure Portal zobrazuje podrobnosti projektu, stav mezipaměti a základní statistiky pro vaši mezipaměť. Obsahuje taky ovládací prvky pro odstranění mezipaměti, vyprazdňování dat do dlouhodobého úložiště nebo aktualizaci softwaru.

Chcete-li otevřít stránku Přehled, vyberte prostředek mezipaměti v Azure Portal. Načtěte například stránku **všechny prostředky** a klikněte na název mezipaměti.

![snímek stránky s přehledem instance mezipaměti HPC v Azure](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

Tlačítka v horní části stránky vám pomůžou spravovat mezipaměť:

* [**Flush**](#flush-cached-data) – zapisuje všechna data uložená v mezipaměti do cílů úložiště.
* [**Upgrade**](#upgrade-cache-software) – aktualizuje software pro mezipaměť.
* **Refresh** -znovu načte stránku Přehled.
* [**Odstranit**](#delete-the-cache) – trvale zničí mezipaměť

Přečtěte si další informace o těchto možnostech.

## <a name="flush-cached-data"></a>Vyprázdnit data uložená v mezipaměti

Tlačítko **vyprázdnit** na stránce Přehled oznamuje mezipaměť, aby okamžitě zapisovala všechna změněná data, která jsou uložená v mezipaměti, do cíle úložiště back-endu. Mezipaměť rutinně ukládá data do cílů úložiště, takže není nutné ji provádět ručně, pokud nechcete zajistit, aby byl systém back-end úložiště aktuální. Například můžete použít **vyprázdnit** před pořizováním snímku úložiště nebo kontrolou velikosti sady dat.

> [!NOTE]
> V průběhu procesu vyprázdnění nemůže mezipaměť obsluhovat požadavky klienta. Po dokončení operace je přístup do mezipaměti pozastaven a pokračuje.

Když zahájíte operaci vyprázdnění mezipaměti, mezipaměť zastaví přijímání požadavků klientů a stav mezipaměti na stránce s přehledem se změní na **vyprázdnění**.

Data v mezipaměti se ukládají do příslušných cílů úložiště. Proces může trvat několik minut, nebo může trvat hodinu nebo déle, a to v závislosti na tom, kolik dat bylo v poslední době zapsáno do mezipaměti.

Po uložení všech dat do cílů úložiště mezipaměť automaticky začne přebírat požadavky klienta. Stav mezipaměti se vrátí do **stavu v pořádku**.

## <a name="upgrade-cache-software"></a>Upgrade softwaru pro mezipaměť

Pokud je k dispozici nová verze softwaru, bude tlačítko pro **upgrade** aktivní. V horní části stránky se taky může zobrazit zpráva o aktualizaci softwaru.

![snímek obrazovky s horním řádkem tlačítek s povoleným tlačítkem upgrade](media/hpc-cache-upgrade-button.png)

Klientský přístup se během upgradu softwaru nepřerušil, ale výkon mezipaměti je pomalý. Naplánujte upgrade softwaru v době nešpičky využití nebo v plánované době údržby.

Aktualizace softwaru může trvat několik hodin. Mezipaměť nakonfigurované s vyšší propustností trvá déle než mezipaměť s menšími hodnotami propustnosti ve špičce.

Pokud je k dispozici upgrade softwaru, budete ho muset použít na několik dní ručně. Koncové datum je uvedeno ve zprávě o upgradu. Pokud během této doby upgrade neprovedete, Azure tuto aktualizaci automaticky nainstaluje do mezipaměti. Časování automatického upgradu není možné konfigurovat. Pokud máte obavy o dopad na výkon mezipaměti, měli byste software upgradovat sami ještě před vypršením časového období.

Kliknutím na tlačítko **upgradovat** zahájíte aktualizaci softwaru. Stav mezipaměti se změní na **upgradovat** , dokud se operace nedokončí.

## <a name="delete-the-cache"></a>Odstraní mezipaměť.

Tlačítko **Odstranit** zničí mezipaměť. Když mezipaměť odstraníte, všechny její prostředky se zničí a už se neúčtují poplatky za účet.

Cíle úložiště nejsou při odstraňování mezipaměti ovlivněny. Později je můžete přidat do budoucí mezipaměti nebo je vyřadit samostatně.

Mezipaměť automaticky vyprázdní Všechna neuložená data do cílů úložiště v rámci jejího finálního vypnutí.

## <a name="cache-metrics-and-monitoring"></a>Metriky a monitorování mezipaměti

Stránka Přehled obsahuje grafy pro některé základní statistiky mezipaměti – propustnost mezipaměti, operace za sekundu a latenci.

![snímek obrazovky se třemi spojnicovým grafem ukazující výše uvedenou statistikou pro ukázkovou mezipaměť](media/hpc-cache-overview-stats.png)

Tyto grafy jsou součástí integrovaných nástrojů pro monitorování a analýzu v Azure. Další nástroje a výstrahy jsou k dispozici na stránkách pod hlavičkou **monitorování** na bočním panelu na portálu. Další informace najdete v části portál v [dokumentaci ke službě Azure Monitoring](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

<!-- * Learn more about metrics and statistics for hpc cache -->
* Další informace o [metrikách Azure a nástrojích statistiky](../azure-monitor/index.yml)
* Získejte [pomoc s mezipamětí Azure HPC](hpc-cache-support-ticket.md)
