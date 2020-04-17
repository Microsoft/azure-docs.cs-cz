---
title: Správa a aktualizace mezipaměti Azure HPC
description: Jak spravovat a aktualizovat Azure HPC Cache pomocí portálu Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537946"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Správa mezipaměti z webu Azure Portal

Stránka přehledu mezipaměti na portálu Azure zobrazuje podrobnosti o projektu, stav mezipaměti a základní statistiky mezipaměti. Má také ovládací prvky pro zastavení nebo spuštění mezipaměti, odstranění mezipaměti, vyprázdnění dat do dlouhodobého úložiště a aktualizaci softwaru.

Pokud chcete otevřít stránku s přehledem, vyberte prostředek mezipaměti na webu Azure Portal. Například načtěte stránku **Všechny prostředky** a klikněte na název mezipaměti.

![snímek obrazovky s přehledem instance Azure HPC Cache](media/hpc-cache-overview.png)

Tlačítka v horní části stránky vám mohou pomoci spravovat mezipaměť:

* **Spustit** a [**zastavit**](#stop-the-cache) - pozastaví operaci mezipaměti
* [**Vyprázdnění**](#flush-cached-data) – zápisy změnily data na cíle úložiště.
* [**Upgrade**](#upgrade-cache-software) – aktualizuje software mezipaměti.
* **Aktualizovat** – znovu načte stránku s přehledem.
* [**Odstranit**](#delete-the-cache) - Trvale zničí mezipaměť

Přečtěte si více o těchto možnostech níže.

## <a name="stop-the-cache"></a>Zastavení mezipaměti

Můžete zastavit mezipaměť snížit náklady během neaktivní období. Při zastavení mezipaměti se vám neúčtuje doba uptime, ale účtuje se vám přidělené diskové úložiště mezipaměti. (Podrobnosti naleznete na stránce [s cenami.)](https://aka.ms/hpc-cache-pricing)

Zastavená mezipaměť nereaguje na požadavky klientů. Před zastavením mezipaměti byste měli odpojit klienty.

Tlačítko **Stop** pozastaví aktivní mezipaměť. Tlačítko **Stop** je k dispozici, pokud je stav **mezipaměti V pořádku** nebo **zhoršený**.

![Snímek obrazovky s horními tlačítky se zvýrazněnou zprávou Stop a vyskakovací zprávou popisující akci stop a s dotazem "chcete pokračovat?". tlačítka Ano (výchozí) a Bez](media/stop-cache.png)

Po klepnutí na tlačítko Ano potvrdíte zastavení mezipaměti, mezipaměť automaticky vyprázdní svůj obsah na cíle úložiště. Tento proces může nějakou dobu trvat, ale zajišťuje konzistenci dat. Nakonec se stav mezipaměti změní na **Zastaveno**.

Chcete-li znovu aktivovat zastavenou mezipaměť, klepněte na tlačítko **Start.** Není potřeba žádné potvrzení.

![snímek obrazovky s horními tlačítky se zvýrazněnou možností Start](media/start-cache.png)

## <a name="flush-cached-data"></a>Vyprázdnění dat uložených v mezipaměti

Tlačítko **Flush** na stránce přehledu říká mezipaměti, aby okamžitě zapsala všechna změněná data uložená v mezipaměti do cílů úložiště back-end. Mezipaměť běžně ukládá data do cílů úložiště, takže to není nutné provést ručně, pokud nechcete mít jistotu, že je systém úložiště back-end aktuální. Můžete například použít **Flush** před pořízením snímku úložiště nebo kontrolou velikosti sady dat.

> [!NOTE]
> Během procesu vyprázdnění mezipaměti nemůže sloužit požadavky klientů. Přístup do mezipaměti je pozastaven a pokračuje po dokončení operace.

![Snímek obrazovky s horními tlačítky se zvýrazněným vyprázdněním a vyskakovací zprávou popisující akci flush a s dotazem "chcete pokračovat?" tlačítka Ano (výchozí) a Bez](media/hpc-cache-flush.png)

Při spuštění operace vyprázdnění mezipaměti přestane mezipaměti přijímat požadavky klientů a stav mezipaměti na stránce přehledu se změní na **Vyprázdnění**.

Data v mezipaměti se uloží do příslušných cílů úložiště. V závislosti na tom, kolik dat je třeba vyprázdnět, proces může trvat několik minut nebo více než hodinu.

Po uložení všech dat do cílů úložiště se mezipaměť automaticky začne znovu přijímat požadavky klientů. Stav mezipaměti se vrátí do **režimu V pořádku**.

## <a name="upgrade-cache-software"></a>Upgrade softwaru mezipaměti

Pokud je k dispozici nová verze softwaru, tlačítko **Upgrade** se aktivuje. V horní části stránky by se také měla zobrazit zpráva o aktualizaci softwaru.

![Snímek obrazovky s horním řádkem tlačítek s povoleným tlačítkem Upgradovat](media/hpc-cache-upgrade-button.png)

Přístup klienta není přerušen během upgradu softwaru, ale výkon mezipaměti se zpomaluje. Plánujte upgrade softwaru během doby mimo špičku nebo v plánovaném období údržby.

Aktualizace softwaru může trvat několik hodin. Upgrade mezipamětí nakonfigurovaných s vyšší propustností trvá upgrade déle než ukládání do mezipamětí s menšími hodnotami špičkové propustnosti.

Pokud je k dispozici upgrade softwaru, budete mít týden nebo tak použít ručně. Koncové datum je uvedeno ve zprávě o upgradu. Pokud během této doby upgradujete, Azure automaticky použije aktualizaci do mezipaměti. Časování automatického upgradu nelze konfigurovat. Pokud máte obavy z dopadu na výkon mezipaměti, měli byste software upgradovat sami před vypršením časového období.

Pokud je mezipaměť zastavena při průchodu koncového data, bude automaticky upgradovat software při příštím spuštění. (Aktualizace se nemusí spustit okamžitě, ale spustí se v první hodině.)

Klepnutím na tlačítko **Upgradovat** zahájíte aktualizaci softwaru. Stav mezipaměti se změní na **Upgrade,** dokud nebude operace dokončena.

## <a name="delete-the-cache"></a>Odstranění mezipaměti

Tlačítko **Odstranit** zničí mezipaměť. Když odstraníte mezipaměť, všechny její prostředky jsou zničeny a již nejsou účtovány poplatky za účet.

Při odstranění mezipaměti nejsou ovlivněny svazky back-endového úložiště používané jako cíle úložiště. Můžete je přidat do budoucí mezipaměti později nebo je samostatně vyřadit z provozu.

> [!NOTE]
> Azure HPC Cache automaticky nezapisuje změněná data z mezipaměti do back-endových úložných systémů před odstraněním mezipaměti.
>
> Chcete-li se ujistit, že všechna data v mezipaměti byla zapsána do dlouhodobého úložiště, [zastavte ji](#stop-the-cache) před odstraněním. Ujistěte se, že zobrazuje stav **Zastaveno** před kliknutím na tlačítko odstranit.

## <a name="cache-metrics-and-monitoring"></a>Metriky mezipaměti a monitorování

Stránka s přehledem zobrazuje grafy pro některé základní statistiky mezipaměti – propustnost mezipaměti, operace za sekundu a latence.

![Snímek obrazovky se třemi spojnicemi grafů znázorňujících výše uvedené statistiky pro ukázkovou mezipaměť](media/hpc-cache-overview-stats.png)

Tyto grafy jsou součástí integrovaných nástrojů Azure pro monitorování a analýzu. Další nástroje a výstrahy jsou k dispozici na stránkách pod nadpisem **Monitorování** na postranním panelu portálu. Další informace najdete v části portálu v [dokumentaci monitorování Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

* Další informace o [metrikách azure a nástrojích statistik](../azure-monitor/index.yml)
* Získání [nápovědy k azure hpc meziporám](hpc-cache-support-ticket.md)
