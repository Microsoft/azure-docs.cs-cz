---
title: Informace o tom, jak proces automatické migrace pro Azure Monitor klasické výstrahy funguje
description: Přečtěte si, jak funguje automatický proces migrace.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734855"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Pochopení procesu automatické migrace pro pravidla upozornění na Classic

Jak jsme [dřív oznámili](monitoring-classic-retirement.md), klasické výstrahy v Azure monitor jsou vyřazení pro uživatele veřejného cloudu, i když jsou i v omezeném počtu použití až do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.

[Nástroj pro migraci](alerts-using-migration-tool.md) je k dispozici ve Azure Portal, aby si zákazníci mohli aktivovat migraci sami. Tento článek vysvětluje automatický proces migrace ve veřejném cloudu, který se spustí po 31. května 2021. Také podrobně popisuje problémy a řešení, se kterými můžete běžet.

## <a name="important-things-to-note"></a>Důležité informace, které je potřeba vzít v paměti

Proces migrace převede pravidla klasických výstrah na nová, ekvivalentní pravidla upozornění a vytvoří skupiny akcí. V přípravě si pamatujte na tyto body:

- Formáty datové části oznámení pro nová pravidla výstrahy se liší od datových částí klasických pravidel upozornění, protože podporují více funkcí. Pokud máte klasické pravidlo upozornění s Logic Apps, Runbooky nebo Webhooky, může přestat fungovat podle očekávání po migraci, a to kvůli rozdílům v datové části. [Přečtěte si, jak připravit na migraci](alerts-prepare-migration.md).

- Některá z klasických pravidel upozornění se nedají migrovat pomocí tohoto nástroje. [Zjistěte, jaká pravidla se nedají migrovat a co s nimi dělat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>K čemu dojde během procesu automatické migrace ve veřejném cloudu?

- Od 31. května 2021 nebudete moct vytvářet žádná nová klasická pravidla upozornění a migrace klasických výstrah se spustí v dávkách.
- Všechna klasická pravidla výstrah, která sledují odstraněné cílové prostředky nebo [metriky, které už nejsou podporované,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) se považují za neplatnou.
- Pravidla klasických výstrah, která jsou neplatná, budou odebrána po 31. května 2021.
- Po spuštění migrace pro předplatné by se mělo dokončit během hodiny. Zákazníci mohou monitorovat stav migrace [v nástroji pro migraci v Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Vlastníci předplatného obdrží e-mail o úspěšnosti nebo neúspěchu migrace.

    > [!NOTE]
    > Pokud nechcete čekat na spuštění procesu automatické migrace, můžete migraci i přesto aktivovat pomocí nástroje pro migraci.

## <a name="what-if-the-automatic-migration-fails"></a>Co když Automatická migrace selhává?

V případě neúspěchu procesu automatické migrace obdrží vlastníci předplatného e-mail s upozorněním na problém. K zobrazení všech podrobností problému můžete použít nástroj pro migraci v nástroji Azure Monitor. V [Průvodci odstraňováním potíží](alerts-understand-migration.md#common-problems-and-remedies) najdete pomoc s problémy, se kterými se můžete setkat při migraci.

  > [!NOTE]
  > V případě, že je potřeba provést akci od zákazníků, jako je dočasné zakázání zámku prostředku nebo změna přiřazení zásady, budou muset zákazníci tyto problémy vyřešit. Pokud pak problémy nebudou vyřešeny, nelze zaručit úspěšnou migraci klasických výstrah.

## <a name="next-steps"></a>Další kroky

- [Příprava na migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)