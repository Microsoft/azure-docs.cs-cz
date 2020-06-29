---
title: Správa a aktualizace mezipaměti HPC Azure
description: Jak spravovat a aktualizovat mezipaměť HPC Azure pomocí Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 825b8a34e130286a5772363107311fe4170e8743
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515561"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Správa mezipaměti z Azure Portal

Stránka s přehledem mezipaměti v Azure Portal zobrazuje podrobnosti projektu, stav mezipaměti a základní statistiky pro vaši mezipaměť. Obsahuje taky ovládací prvky pro zastavení nebo spuštění mezipaměti, odstranění mezipaměti, vyprázdnění dat pro dlouhodobé ukládání a aktualizace softwaru.

Chcete-li otevřít stránku Přehled, vyberte prostředek mezipaměti v Azure Portal. Načtěte například stránku **všechny prostředky** a klikněte na název mezipaměti.

![snímek stránky s přehledem instance mezipaměti HPC v Azure](media/hpc-cache-overview.png)

Tlačítka v horní části stránky vám pomůžou spravovat mezipaměť:

* **Spustit** a [**zastavit**](#stop-the-cache) – pozastaví operaci mezipaměti.
* [**Flush**](#flush-cached-data) – zapisuje změněná data do cílů úložiště
* [**Upgrade**](#upgrade-cache-software) – aktualizuje software pro mezipaměť.
* **Refresh** -znovu načte stránku Přehled.
* [**Odstranit**](#delete-the-cache) – trvale zničí mezipaměť

Přečtěte si další informace o těchto možnostech.

Klikněte na obrázek níže a sledujte [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) , které ukazuje úlohy správy mezipaměti.

[![Miniatura videa: Azure HPC cache: Manage (kliknutím můžete navštívit stránku video)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Zastavení mezipaměti

Můžete zastavit mezipaměť a snížit tak náklady během neaktivního období. Za dobu provozu se vám neúčtují při zastavení mezipaměti, ale účtují se vám poplatky za úložiště na disku přidělené mezipamětí. (Podrobnosti najdete na stránce s [cenami](https://aka.ms/hpc-cache-pricing) .)

Zastavená mezipaměť nereaguje na požadavky klientů. Před zastavením mezipaměti byste měli odpojit klienty.

Tlačítko **zastavit** pozastavuje aktivní mezipaměť. Tlačítko **zastavit** je k dispozici, když je stav mezipaměti **v pořádku** nebo je **degradován**.

![snímek obrazovky horních tlačítek se zvýrazněným stopou a automaticky otevíraná zpráva popisující akci zastavení a dotazování chcete pokračovat? s Ano (výchozí) a bez tlačítek](media/stop-cache.png)

Po kliknutí na tlačítko Ano potvrďte zastavení mezipaměti, mezipaměť automaticky vyprázdní svůj obsah do cílů úložiště. Tento proces může nějakou dobu trvat, ale zajišťuje konzistenci dat. Nakonec se stav mezipaměti změní na **Zastaveno**.

Chcete-li znovu aktivovat zastavenou mezipaměť, klikněte na tlačítko **Start** . Není nutné žádné potvrzení.

![snímek obrazovky horních tlačítek se zvýrazněnou možností Start](media/start-cache.png)

## <a name="flush-cached-data"></a>Vyprázdnit data uložená v mezipaměti

Tlačítko **vyprázdnit** na stránce Přehled oznamuje mezipaměť, aby okamžitě zapisovala všechna změněná data, která jsou uložená v mezipaměti, do cíle úložiště back-endu. Mezipaměť rutinně ukládá data do cílů úložiště, takže není nutné ji provádět ručně, pokud nechcete zajistit, aby byl systém back-end úložiště aktuální. Například můžete použít **vyprázdnit** před pořizováním snímku úložiště nebo kontrolou velikosti sady dat.

> [!NOTE]
> V průběhu procesu vyprázdnění nemůže mezipaměť obsluhovat požadavky klienta. Po dokončení operace je přístup do mezipaměti pozastaven a pokračuje.

![snímek obrazovky horních tlačítek s zvýrazněnou možností vyprázdnění a automaticky otevíraná okna popisující akci vyprázdnění a dotaz Chcete pokračovat? s Ano (výchozí) a bez tlačítek](media/hpc-cache-flush.png)

Když zahájíte operaci vyprázdnění mezipaměti, mezipaměť zastaví přijímání požadavků klientů a stav mezipaměti na stránce s přehledem se změní na **vyprázdnění**.

Data v mezipaměti se ukládají do příslušných cílů úložiště. V závislosti na tom, kolik dat je potřeba vyprázdnit, může tento proces trvat několik minut nebo déle než hodinu.

Po uložení všech dat do cílů úložiště mezipaměť automaticky začne přebírat požadavky klienta. Stav mezipaměti se vrátí do **stavu v pořádku**.

## <a name="upgrade-cache-software"></a>Upgrade softwaru pro mezipaměť

Pokud je k dispozici nová verze softwaru, bude tlačítko pro **upgrade** aktivní. V horní části stránky by se měla zobrazit také zpráva o aktualizaci softwaru.

![snímek obrazovky s horním řádkem tlačítek s povoleným tlačítkem upgrade](media/hpc-cache-upgrade-button.png)

Klientský přístup se během upgradu softwaru nepřerušil, ale výkon mezipaměti je pomalý. Naplánujte upgrade softwaru v době nešpičky využití nebo v plánované době údržby.

Aktualizace softwaru může trvat několik hodin. Mezipaměť nakonfigurované s vyšší propustností trvá déle než mezipaměť s menšími hodnotami propustnosti ve špičce.

Pokud je k dispozici upgrade softwaru, budete mít týden, nebo ho budete muset použít ručně. Koncové datum je uvedeno ve zprávě o upgradu. Pokud během této doby upgrade neprovedete, Azure tuto aktualizaci automaticky nainstaluje do mezipaměti. Časování automatického upgradu není možné konfigurovat. Pokud máte obavy o dopad na výkon mezipaměti, měli byste software upgradovat sami ještě předtím, než vyprší časový interval.

Pokud se vaše mezipaměť zastaví po průchodu koncového data, mezipaměť při příštím spuštění automaticky upgraduje software. (Aktualizace se nemusí spustit okamžitě, ale spustí se první hodinu.)

Kliknutím na tlačítko **upgradovat** zahájíte aktualizaci softwaru. Stav mezipaměti se změní na **upgradovat** , dokud se operace nedokončí.

## <a name="delete-the-cache"></a>Odstraní mezipaměť.

Tlačítko **Odstranit** zničí mezipaměť. Když mezipaměť odstraníte, všechny její prostředky se zničí a už se neúčtují poplatky za účet.

Záložní úložné svazky používané jako cíle úložiště nejsou při odstraňování mezipaměti ovlivněny. Později je můžete přidat do budoucí mezipaměti nebo je vyřadit samostatně.

> [!NOTE]
> Mezipaměť HPC Azure nepřed odstraněním mezipaměti automaticky nezapisuje změněná data z mezipaměti do back-endové systémů úložiště.
>
> Chcete-li zajistit, aby byla všechna data v mezipaměti zapsána do dlouhodobého úložiště, [zastavte mezipaměť](#stop-the-cache) před odstraněním. Před kliknutím na tlačítko Odstranit se ujistěte, že zobrazuje stav **Zastaveno** .

## <a name="cache-metrics-and-monitoring"></a>Metriky a monitorování mezipaměti

Stránka Přehled obsahuje grafy pro některé základní statistiky mezipaměti – propustnost mezipaměti, operace za sekundu a latenci.

![snímek obrazovky se třemi spojnicovým grafem ukazující výše uvedenou statistikou pro ukázkovou mezipaměť](media/hpc-cache-overview-stats.png)

Tyto grafy jsou součástí integrovaných nástrojů pro monitorování a analýzu v Azure. Další nástroje a výstrahy jsou k dispozici na stránkách pod hlavičkou **monitorování** na bočním panelu na portálu. Další informace najdete v části portál v [dokumentaci ke službě Azure Monitoring](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Další kroky

* Další informace o [metrikách Azure a nástrojích statistiky](../azure-monitor/index.yml)
* Získejte [pomoc s mezipamětí Azure HPC](hpc-cache-support-ticket.md)
