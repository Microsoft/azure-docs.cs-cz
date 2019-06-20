---
title: Migrace klasického upozornění ve službě Azure Monitor pomocí dobrovolné migrační nástroj
description: Další informace o použití dobrovolné migračního nástroje pro migraci klasického pravidel upozornění.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5149c20431155b92aa6dab3b976fe68e6d9cb8c2
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153948"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Použijte nástroj dobrovolné migrace k migraci klasického pravidel upozornění

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v září 2019 (byla původně 2019. července). Migrační nástroj je dostupný na webu Azure Portal pro zákazníky používající klasický pravidla upozornění a který chcete spustit migrace sami. Tento článek vysvětluje, jak používat nástroj pro migraci dobrovolně migraci klasického pravidel upozornění před zahájením migrace automatické v září 2019.

> [!NOTE]
> Datum vyřazení pro migraci klasických upozornění byla z důvodu zpoždění při uvádění nástroj pro migraci [rozšířená tak, aby 31. srpna 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od původně oznámené data 30. června 2019.

## <a name="benefits-of-new-alerts"></a>Výhody nové výstrahy

Upozornění Classic se nahrazují nové, sjednoceném výstrahy ve službě Azure Monitor. Novou platformu upozornění má následující výhody:

- Může upozornit na širokou škálu vícerozměrné metriky pro [mnoho dalších služeb Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Nové metriky výstrahy podpory [pravidla upozornění více prostředky](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , který výrazně snížit režijní náklady správy mnoho pravidel.
- Mechanismus jednotné oznámení, která podporuje:
  - [Skupiny akcí](action-groups.md), modulární oznámení mechanismus, který funguje s všechny nové typy výstrah (metrik, protokolů a protokolu aktivity).
  - Nové oznámení mechanismy, jako je SMS, hlas a konektor ITSM.
- [Jednotné rozhraní upozornění](alerts-overview.md) přináší všechny výstrahy na různým signálům (metrik, protokolů a protokolu aktivity) do jednoho místa.

## <a name="before-you-migrate"></a>Před zahájením migrace

Proces migrace převede classic pravidla upozornění na nové, odpovídající pravidla upozornění a vytvoří skupiny akcí. V rámci přípravy mějte na paměti následující skutečnosti:

- Formát datové části oznámení a rozhraní API můžete vytvářet a spravovat nové pravidla upozornění se liší od nastavení classic pravidel upozornění, protože podporují další funkce. [Informace o přípravě na migraci](alerts-prepare-migration.md).

- Některé klasické pravidla upozornění není možné migrovat pomocí nástroje. [Zjistěte, která pravidla se nedají migrovat a co dělat s nimi](alerts-understand-migration.md#which-classic-alert-rules-cannot-be-migrated).

    > [!NOTE]
    > Proces migrace nebude mít vliv na vyhodnocení pravidel upozornění classic. Budou dál spouštět a odesílání upozornění, dokud se migrují a projeví nová pravidla upozornění.

## <a name="how-to-use-the-migration-tool"></a>Jak používat nástroj pro migraci

Spustit migraci klasického pravidel upozornění na webu Azure Portal, postupujte podle těchto kroků:

1. V [webu Azure portal](https://portal.azure.com)vyberte **monitorování**.

1. Vyberte **výstrahy**a pak vyberte **spravovat pravidla výstrah** nebo **zobrazit upozornění classic**.

1. Vyberte **migrace do nového pravidla** přejdete na úvodní stránku migrace. Tato stránka zobrazuje seznam všech předplatných a jejich stav migrace:

    ![migrace cílová](media/alerts-migration/migration-landing.png "migrovat pravidla")

    Všechna předplatná, která je možné migrovat pomocí nástroje, které jsou označeny jako **připraveno k migraci**.

    > [!NOTE]
    > Nástroj pro migraci zavádí ve fázích pro všechna předplatná, které používají klasický pravidla upozornění. V počátečních fázích zavádění může se zobrazit některá předplatná označen jako nepřipravený pro migraci.

1. Vyberte jednoho nebo více předplatných a pak vyberte **ve verzi Preview migrace**.

    Výsledná stránka zobrazuje podrobnosti o classic pravidel upozornění, které se budou migrovat pro jedno předplatné najednou. Můžete také vybrat **stáhnout podrobnosti o migraci pro toto předplatné** zobrazíte podrobnosti ve formátu CSV.

    ![verzi preview migrace](media/alerts-migration/migration-preview.png "ve verzi Preview migrace")

1. Zadejte jeden nebo více e-mailové adresy, abyste dostávali oznámení o stavu migrace. Obdržíte e-mailu po dokončení migrace, nebo pokud potřebujete se od vás žádnou akci.

1. Vyberte **zahájit migraci**. Přečtěte si informace uvedené v potvrzovacím dialogovém okně a potvrďte, že jste připravení začít s procesem migrace.

    > [!IMPORTANT]
    > Po zahájení migrace pro předplatné, nebudete moci upravovat nebo vytvářet klasické pravidla upozornění pro toto předplatné. Toto omezení se zajistí, že nejsou žádné změny k pravidel upozornění classic ztraceny během migrace do nového pravidla. I když nebudete moct změnit classic pravidel upozornění, budou pořád dál spouštět a poskytují oznámení, dokud jste migrovali. Po dokončení migrace vašeho předplatného, nemůžete použít klasické pravidla upozornění zobrazovat.

    ![Potvrďte migraci](media/alerts-migration/migration-confirm.png "potvrdit migraci spustit")

1. Po dokončení migrace nebo od vás se nic dělat, obdržíte e-mailem na adresy, které jste zadali dříve. Můžete také pravidelně zkontrolovat stav v cílové stránky migrace na portálu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Proč se Moje předplatné uvedená jako není připraveno k migraci?

Nástroj pro migraci zavádí zákazníkům ve fázích. V počátečních fázích, může být označena většinu nebo všechny z vašich předplatných jako **není připravený k migraci**. 

Až bude předplatné připravené k migraci, vlastník předplatného obdrží e-mailovou zprávu s oznámením, že tento nástroj je k dispozici. Sledujte pro tuto zprávu.

### <a name="who-can-trigger-the-migration"></a>Kdo může aktivovat migrace?

Uživatelé, kteří mají přiřazenou na úrovni předplatného roli Přispěvatel monitorování budou moct aktivovat migrace. [Další informace o řízení přístupu na základě rolí pro proces migrace](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak dlouho bude migrace trvat?

Dokončení migrace pro většinu předplatných na méně než hodinu. Vám může udržovat přehled o průběhu migrace na cílové stránce migrace. Během migrace je třeba zajistit upozornění jsou spuštěné v systému upozornění classic nebo v nové.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mám dělat, když dojde k problému při migraci?

Zobrazit [Průvodce odstraňováním potíží](alerts-understand-migration.md#common-problems-and-remedies) nápovědu k problémům se může setkat během migrace. V případě potřeby jakékoli akce od vás k dokončení migrace budete upozorněni na e-mailové adresy, které jste zadali při nastavování nástroj.

## <a name="next-steps"></a>Další postup

- [Příprava na migraci](alerts-prepare-migration.md)
- [Pochopit, jak funguje nástroj pro migraci](alerts-understand-migration.md)
