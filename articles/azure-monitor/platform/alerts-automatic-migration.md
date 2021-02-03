---
title: Informace o tom, jak proces automatické migrace pro Azure Monitor klasické výstrahy funguje
description: Přečtěte si, jak funguje automatický proces migrace.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 9ce3550dcef174a4d970917fabfda3c4fd2a8df4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526547"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Pochopení procesu automatické migrace pro pravidla upozornění na Classic

Jak jsme [dřív oznámili](monitoring-classic-retirement.md), klasické výstrahy v Azure monitor jsou vyřazení pro uživatele veřejného cloudu, i když jsou stále v omezeném rozsahu použití pro prostředky, které ještě nepodporují nové výstrahy. V rámci procesu vyřazení je k dispozici ve Azure Portal [Nástroj pro migraci](alerts-using-migration-tool.md) , který zákazníkům umožňuje aktivovat migraci sami.
Tento článek vás provede procesem automatické migrace a pomůže vám vyřešit všechny problémy, se kterými můžete běžet.

  > [!NOTE]
  > Tento článek se týká jenom veřejného cloudu Azure. Proces odchodu pro Azure Monitor klasické výstrahy v cloudu Azure Government a Azure Čína 21Vianet se budou v budoucnu vyvažovat za budoucí datum.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>K čemu dojde během procesu automatické migrace?

- Od **1. září 2019** nebudou zákazníci moct vytvářet žádná nová pravidla pro klasické výstrahy s výjimkou [určitých metrik](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).
- V případě výjimek může zákazník nadále vytvářet nová pravidla pro klasických výstrah a používat jejich klasické výstrahy, dokud nebudete moci pokračovat v oznamování.
- Od **1. září 2019** se migrace klasických výstrah spustí v dávkách pro všechny zákazníky, kteří mají klasické výstrahy.
- Stejně jako u dobrovolných migračních nástrojů se některá z klasických pravidel upozornění, která nemigrovat, zůstanou v tom, jak jsou. Tato pravidla s klasickými výstrahami budou nadále podporována až do dalšího oznámení. Všechna neplatná pravidla pro klasických výstrah však budou odstraněna, protože nejsou funkční.
Všechna klasická pravidla výstrah, která sledují odstraněné cílové prostředky nebo [metriky, které už nejsou podporované,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) se považují za neplatnou.
- Po spuštění migrace pro vaše předplatné, pokud nedochází k nějakým problémům, by migrace měla být dokončena během hodiny. Zákazníci můžou sledovat stav migrace [v okně migrace v Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Vlastníci předplatného obdrží e-mail po úspěšném dokončení migrace.
- Pokud během migrace dojde k nějakým potížím, vlastníci předplatného budou také dostávat e-maily s upozorněním, že mají stejné. Zákazníci můžou k zobrazení všech podrobností problému použít okno migrace.
- V případě, že je potřeba provést akci od zákazníků, jako je dočasné zakázání zámku prostředku nebo změna přiřazení zásady, budou muset zákazníci tyto problémy vyřešit. Pokud pak problémy nebudou vyřešeny, nelze zaručit úspěšnou migraci klasických výstrah.

    > [!NOTE]
    > Pokud nechcete čekat na spuštění procesu automatické migrace, můžete migraci i přesto aktivovat pomocí nástroje pro migraci.

## <a name="important-things-to-note"></a>Důležité informace, které je potřeba vzít v paměti

Proces migrace převede pravidla klasických výstrah na nová, ekvivalentní pravidla upozornění a vytvoří skupiny akcí. V přípravě si pamatujte na tyto body:

- Formáty datové části oznámení pro nová pravidla výstrahy se liší od pravidel klasických výstrah, protože podporují více funkcí. Pokud máte aplikace logiky, Runbooky nebo webhooků aktivované pravidlem klasického upozornění, můžou přestat fungovat podle očekávání, jakmile se migrace dokončí kvůli rozdílům v datových podčástech oznámení. [Přečtěte si, jak připravit na migraci](alerts-prepare-migration.md).

- Některá z klasických pravidel upozornění se nedají migrovat pomocí tohoto nástroje. [Zjistěte, jaká pravidla se nedají migrovat a co s nimi dělat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Proces migrace nebude mít vliv na hodnocení vašich klasických pravidel upozornění. Budou i nadále spouštět a odesílat výstrahy, dokud nebudou migrovány a nová pravidla upozornění vstoupí v platnost.

## <a name="what-if-the-automatic-migration-fails"></a>Co když Automatická migrace selhává?

V případě neúspěchu procesu automatické migrace obdrží vlastníci předplatného e-mail s upozorněním na problém. K zobrazení všech podrobností problému můžete použít okno migrace v Azure Monitor.

V [Průvodci odstraňováním potíží](alerts-understand-migration.md#common-problems-and-remedies) najdete pomoc s problémy, se kterými se můžete setkat při migraci.

  > [!NOTE]
  > V případě, že je potřeba provést akci od zákazníků, jako je dočasné zakázání zámku prostředku nebo změna přiřazení zásady, budou muset zákazníci tyto problémy vyřešit. Pokud pak problémy nebudou vyřešeny, nelze zaručit úspěšnou migraci klasických výstrah.

## <a name="next-steps"></a>Další kroky

- [Příprava na migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
