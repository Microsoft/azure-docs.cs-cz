---
title: Migrace pravidel výstrah Azure Monitor
description: Přečtěte si, jak pomocí nástroje dobrovolné migrace migrovat klasická pravidla výstrah.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114252"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Migrace klasických pravidel výstrah pomocí nástroje dobrovolné migrace

Jak [již bylo oznámeno](monitoring-classic-retirement.md), klasické výstrahy ve službě Azure Monitor se vyřazují v září 2019 (původně v červenci 2019). Nástroj pro migraci je k dispozici na webu Azure Portal zákazníkům, kteří používají klasická pravidla výstrah a chtějí migraci spustit sami. Tento článek vysvětluje, jak pomocí nástroje pro migraci dobrovolně migrovat klasická pravidla výstrah před zahájením automatické migrace v září 2019.

> [!NOTE]
> Kvůli zpoždění při zavádění migračního nástroje bylo datum odchodu do důchodu pro klasickou [výstrahu prodlouženo na 31.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="benefits-of-new-alerts"></a>Výhody nových výstrah

Klasické výstrahy jsou nahrazovány novými jednotnými výstrahami v Azure Monitoru. Nová platforma upozornění má následující výhody:

- Můžete upozornit na různé vícerozměrné metriky pro [mnoho dalších služeb Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Nové upozornění metriky podporují [pravidla výstrah s více prostředky,](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) která výrazně snižují režii správy mnoha pravidel.
- Jednotný oznamovací mechanismus, který podporuje:
  - [Skupiny akcí](action-groups.md), modulární mechanismus oznámení, který pracuje se všemi novými typy výstrah (metrika, protokol a protokol aktivit).
  - Nové mechanismy oznámení, jako je SMS, hlas a konektor ITSM.
- [Jednotné prostředí výstrah](alerts-overview.md) přináší všechna upozornění na různé signály (metriky, protokoly a protokol aktivity) na jedno místo.

## <a name="before-you-migrate"></a>Před migrací

Proces migrace převede klasická pravidla výstrah na nová ekvivalentní pravidla výstrah a vytvoří skupiny akcí. Při přípravě si uvědomte následující body:

- Formát datové části oznámení a api pro vytvoření a správu nových pravidel výstrah se liší od pravidel klasické výstrahy, protože podporují více funkcí. [Přečtěte si, jak se připravit na migraci](alerts-prepare-migration.md).

- Některá klasická pravidla výstrah nelze migrovat pomocí nástroje. [Přečtěte si, která pravidla nelze migrovat a co s nimi dělat](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Proces migrace nebude mít vliv na hodnocení klasických pravidel výstrah. Budou nadále spouštět a odesílat výstrahy, dokud se neprojeví a nová pravidla výstrah se projeví.

## <a name="how-to-use-the-migration-tool"></a>Způsoby použití nástroje pro migraci

Pokud chcete na webu Azure Portal spustit migraci klasických pravidel výstrah, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Monitor**.

1. Vyberte **Výstrahy**a pak vyberte **Spravovat pravidla výstrah** nebo Zobrazit **klasická upozornění**.

1. Vyberte **Migrovat na nová pravidla,** abyste přešli na vstupní stránku migrace. Na této stránce je zobrazen seznam všech předplatných a jejich stavu migrace:

    ![migrace-přistání](media/alerts-migration/migration-landing.png "Migrace pravidel")

    Všechna předplatná, která lze migrovat pomocí nástroje, jsou označena jako **připravená k migraci**.

    > [!NOTE]
    > Nástroj pro migraci se zavádí ve fázích pro všechna předplatná, která používají klasická pravidla výstrah. V počátečních fázích zavedení se může zobrazit některá předplatná označená jako nepřipravená k migraci.

1. Vyberte jedno nebo více předplatných a pak vyberte **Náhled migrace**.

    Výsledná stránka zobrazuje podrobnosti o klasických pravidlech výstrah, která budou migrována pro jedno předplatné najednou. Můžete také vybrat **Stáhnout podrobnosti o migraci pro toto předplatné,** abyste získali podrobnosti ve formátu CSV.

    ![migrace-náhled](media/alerts-migration/migration-preview.png "Náhled migrace")

1. Zadejte jednu nebo více e-mailových adres, které mají být upozorňovány na stav migrace. Po dokončení migrace nebo po nějaké akci od vás obdržíte e-mail.

1. Vyberte **možnost Spustit migraci**. Přečtěte si informace zobrazené v potvrzovacím dialogovém okně a potvrďte, že jste připraveni zahájit proces migrace.

    > [!IMPORTANT]
    > Po zahájení migrace pro předplatné nebudete moct upravovat ani vytvářet klasická pravidla výstrah pro toto předplatné. Toto omezení zajišťuje, že během migrace na nová pravidla nedojde ke ztrátě žádných změn klasických pravidel výstrah. I když nebudete moct změnit klasická pravidla výstrah, budou nadále spouštět a poskytovat výstrahy, dokud nebudou migrována. Po dokončení migrace pro vaše předplatné už nemůžete používat klasická pravidla upozornění.

    ![potvrzení migrace](media/alerts-migration/migration-confirm.png "Potvrdit zahájení migrace")

1. Po dokončení migrace nebo v případě, že je od vás vyžadována akce, obdržíte e-mail na adresy, které jste zadali dříve. Stav můžete také pravidelně kontrolovat na vstupní stránce migrace na portálu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Proč moje předplatné není připraveno k migraci?

Nástroj pro migraci se zavádí pro zákazníky ve fázích. V počátečních fázích může být většina nebo všechna vaše předplatná označena jako **Není připravena k migraci**. 

Když se předplatné připraví na migraci, vlastník předplatného obdrží e-mailovou zprávu s informací, že nástroj je k dispozici. Dávejte pozor na tuto zprávu.

### <a name="who-can-trigger-the-migration"></a>Kdo může spustit migraci?

Uživatelé, kteří mají roli přispěvatele monitorování přiřazenou na úrovni předplatného, mohou migraci spustit. [Další informace o řízení přístupu na základě rolí pro proces migrace](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak dlouho bude migrace trvat?

Migrace je dokončena pro většinu předplatných za méně než hodinu. Průběh migrace můžete sledovat na vstupní stránce migrace. Během migrace si můžete být jisti, že výstrahy jsou stále spuštěny buď v klasickém systému výstrah, nebo v novém.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mám dělat, když se během migrace dostanu do potíží?

Nápovědu k problémům, se kterými se během migrace můžete setkat, najdete v [příručce pro řešení potíží.](alerts-understand-migration.md#common-problems-and-remedies) Pokud od vás k dokončení migrace potřebujete nějakou akci, budete upozorněni na e-mailové adresy, které jste zadali při nastavování nástroje.

## <a name="next-steps"></a>Další kroky

- [Příprava pro migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
