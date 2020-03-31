---
title: Zjistěte, jak funguje proces automatické migrace klasických výstrah Azure Monitoru
description: Zjistěte, jak funguje proces automatické migrace.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668243"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Seznamte se s procesem automatické migrace klasických pravidel výstrah

Jak [již bylo oznámeno](monitoring-classic-retirement.md), klasické výstrahy ve službě Azure Monitor se vyřazují v září 2019 (původně v červenci 2019). Jako součást procesu vyřazení je na webu Azure Portal k dispozici [nástroj pro migraci,](alerts-using-migration-tool.md) který zákazníkům může aktivovat migraci sami. Pokud jste nástroj pro migraci nepoužili do 31.
Tento článek vás provede procesem automatické migrace a pomůže vám vyřešit všechny problémy, které by mohly narazit.

  > [!NOTE]
  > Tento článek se vztahuje jenom pro veřejný cloud Azure. Proces vyřazení pro klasické výstrahy Azure Monitor u cloudu Azure Government a Azure China 21Vianet se ozve k budoucímu datu.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Co se stane během procesu automatické migrace?

- **září 2019**nebudou moci zákazníci vytvářet žádná nová klasická pravidla upozornění s výjimkou [určitých metrik](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Pro výjimky může zákazník pokračovat ve vytváření nových klasických pravidel výstrah a používat klasické výstrahy až do června 2020.
- **září 2019**bude migrace klasických výstrah spuštěna v dávkách pro všechny zákazníky, kteří mají klasické výstrahy.
- Stejně jako u nástroje dobrovolné migrace zůstanou některá klasická pravidla výstrah, která nejsou mizivá, tak, jak jsou. Tato klasická pravidla varování budou podporována až do června 2020. Všechna neplatná pravidla klasické výstrahy však budou odstraněna, protože jsou nefunkční.
Všechna klasická pravidla výstrah, která monitorují odstraněné cílové prostředky nebo [metriky, které již nejsou podporovány,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) jsou považována za neplatná.
- Jakmile se spustí migrace pro vaše předplatné, pokud nejsou nějaké problémy, migrace by měla být dokončena do jedné hodiny. Zákazníci můžou sledovat stav migrace na [okně migrace v Azure Monitoru](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Vlastníci předplatného obdrží e-mail po úspěšném dokončení migrace.
- Pokud během migrace dojde k problémům, majitelé předplatného také obdrží e-mail s informací o tom samém. Zákazníci mohou pomocí migračního okna zobrazit úplné podrobnosti o problému.
- V případě, že je potřeba akce od zákazníka, jako je dočasné zakázání zámku prostředku nebo změna přiřazení zásad, zákazníci budou muset vyřešit všechny problémy do 31. Pokud do té doby nebudou problémy vyřešeny, nelze zaručit úspěšnou migraci klasických výstrah.

    > [!NOTE]
    > Pokud nechcete čekat na spuštění procesu automatické migrace, můžete migraci spustit dobrovolně pomocí nástroje pro migraci.

## <a name="important-things-to-note"></a>Důležité věci, které je třeba poznamenat

Proces migrace převede klasická pravidla výstrah na nová ekvivalentní pravidla výstrah a vytvoří skupiny akcí. Při přípravě si uvědomte následující body:

- Formáty datové části oznámení pro nová pravidla výstrah se liší od formátů klasických pravidel výstrah, protože podporují více funkcí. Pokud máte logické aplikace, runbooky nebo webhooky, které se aktivují klasickým pravidlem výstrahy, mohou po dokončení migrace přestat fungovat podle očekávání z důvodu rozdílů v datových částí oznámení. [Přečtěte si, jak se připravit na migraci](alerts-prepare-migration.md).

- Některá klasická pravidla výstrah nelze migrovat pomocí nástroje. [Přečtěte si, která pravidla nelze migrovat a co s nimi dělat](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Proces migrace nebude mít vliv na hodnocení klasických pravidel výstrah. Budou nadále spouštět a odesílat výstrahy, dokud se neprojeví a nová pravidla výstrah se projeví.

## <a name="what-if-the-automatic-migration-fails"></a>Co když se automatická migrace nezdaří?

Pokud se proces automatické migrace nezdaří, vlastníci předplatného obdrží e-mail s upozorněním na problém. Pomocí okna migrace ve službě Azure Monitor zobrazíte úplné podrobnosti o problému.

Nápovědu k problémům, se kterými se během migrace můžete setkat, najdete v [příručce pro řešení potíží.](alerts-understand-migration.md#common-problems-and-remedies)

  > [!NOTE]
  > V případě, že je potřeba akce od zákazníka, jako je dočasné zakázání zámku prostředku nebo změna přiřazení zásad, zákazníci budou muset vyřešit všechny problémy do 31. Pokud do té doby nebudou problémy vyřešeny, nelze zaručit úspěšnou migraci klasických výstrah.

## <a name="next-steps"></a>Další kroky

- [Příprava pro migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
