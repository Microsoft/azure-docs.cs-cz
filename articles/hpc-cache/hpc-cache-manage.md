---
title: Správa a aktualizace mezipaměti Azure HPC
description: Jak spravovat a aktualizovat Azure HPC Cache pomocí portálu Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252038"
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
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Metriky mezipaměti a monitorování

Stránka s přehledem zobrazuje grafy pro některé základní statistiky mezipaměti – propustnost mezipaměti, operace za sekundu a latence.

![Snímek obrazovky se třemi spojnicemi grafů znázorňujících výše uvedené statistiky pro ukázkovou mezipaměť](media/hpc-cache-overview-stats.png)

Tyto grafy jsou součástí integrovaných nástrojů Azure pro monitorování a analýzu. Další nástroje a výstrahy jsou k dispozici na stránkách pod nadpisem **Monitorování** na postranním panelu portálu. Další informace najdete v části portálu v [dokumentaci monitorování Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

<!-- * Learn more about metrics and statistics for hpc cache -->
* Další informace o [metrikách azure a nástrojích statistik](../azure-monitor/index.yml)
* Získání [nápovědy k azure hpc meziporám](hpc-cache-support-ticket.md)
