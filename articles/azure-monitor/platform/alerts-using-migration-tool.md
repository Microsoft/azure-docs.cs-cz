---
title: Migrace pravidel upozornění Azure Monitor
description: Naučte se používat dobrovolný migrační nástroj k migraci pravidel vašich klasických výstrah.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: e49525018a3e23ecbbf92d7a8b3f7c50804432b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358657"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Migrace pravidel vašich klasických výstrah pomocí nástroje k dobrovolné migraci

Jak [už jsme oznámili](monitoring-classic-retirement.md), klasické výstrahy v Azure monitor jsou vyřazené, i když jsou pořád v omezeném rozsahu použití pro prostředky, které ještě nepodporují nové výstrahy. Nástroj pro migraci byl v Azure Portal k dispozici pro zákazníky, kteří použili pravidla pro klasických výstrah a chtějí spustit migraci sami. V tomto článku se dozvíte, jak používat nástroj pro migraci, který se taky použije pro zbývající výstrahy, které čekají na další oznámení.

## <a name="benefits-of-new-alerts"></a>Výhody nových výstrah

Klasické výstrahy se nahrazují novými a jednotnými výstrahami v Azure Monitor. Nová platforma pro výstrahy přináší následující výhody:

- Pro [mnoho dalších služeb Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)můžete upozorňovat na nejrůznější multidimenzionální metriky.
- Nové výstrahy metriky podporují [pravidla pro výstrahy s více prostředky](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , která významně snižují režijní náklady na správu mnoha pravidel.
- Jednotný mechanismus oznámení, který podporuje:
  - [Skupiny akcí](action-groups.md), modulární mechanismus oznamování, který funguje se všemi novými typy výstrah (metrika, protokol a protokol aktivit).
  - Nové mechanismy oznámení, jako je SMS, Voice a konektor ITSM.
- [Jednotné prostředí výstrah](alerts-overview.md) přináší na jednom místě všechna upozornění na různé signály (metrika, protokol a protokol aktivit).

## <a name="before-you-migrate"></a>Před migrací

Proces migrace převede pravidla klasických výstrah na nová, ekvivalentní pravidla upozornění a vytvoří skupiny akcí. V přípravě si pamatujte na tyto body:

- Formát datové části oznámení i rozhraní API k vytváření a správě nových pravidel výstrah se liší od pravidel pro klasických výstrah, protože podporují více funkcí. [Přečtěte si, jak připravit na migraci](alerts-prepare-migration.md).

- Některá z klasických pravidel upozornění nelze pomocí tohoto nástroje migrovat. [Zjistěte, která pravidla se nedají migrovat a co s nimi dělat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Proces migrace nebude mít vliv na hodnocení vašich klasických pravidel upozornění. Budou i nadále spouštět a odesílat výstrahy, dokud nebudou migrovány a nová pravidla upozornění vstoupí v platnost.

## <a name="how-to-use-the-migration-tool"></a>Způsoby použití nástroje pro migraci

Pokud chcete aktivovat migraci pravidel vašich klasických výstrah v Azure Portal, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)vyberte **monitorování**.

1. Vyberte **výstrahy**a pak vyberte **Spravovat pravidla výstrah** nebo **Zobrazit klasické výstrahy**.

1. Vyberte **migrovat na nová pravidla** a přejdete na úvodní stránku migrace. Tato stránka zobrazuje seznam všech předplatných a jejich stav migrace:

    ![Snímek obrazovky zobrazující stránku pravidla upozornění migrace](media/alerts-migration/migration-landing.png "Migrace pravidel")

    Všechna předplatná, která je možné migrovat pomocí nástroje, jsou označená jako **připravená k migraci**.

    > [!NOTE]
    > Nástroj pro migraci se provádí ve fázích pro všechna předplatná, která používají pravidla pro klasických výstrah. V počátečních fázích zavedení se můžou zobrazit předplatná, která jsou označená jako nepřipravená pro migraci.

1. Vyberte jedno nebo více předplatných a potom vyberte možnost **migrace náhledu**.

    Výsledná stránka obsahuje podrobné informace o pravidlech výstrah, která se budou migrovat pro jedno předplatné najednou. Můžete také vybrat možnost **Stáhnout podrobnosti o migraci pro toto předplatné** a získat podrobnosti ve formátu CSV.

    ![Snímek obrazovky zobrazující stránku pravidla upozornění migrace s odkazem ke stažení podrobností o migraci pro toto předplatné a pro oznámení o migraci můžete zadat e-mail.](media/alerts-migration/migration-preview.png "Náhled migrace")

1. Zadejte jednu nebo více e-mailových adres, které budou upozorňovány na stav migrace. Po dokončení migrace obdržíte e-mail nebo budete potřebovat nějakou akci.

1. Vyberte **Spustit migraci**. Přečtěte si informace uvedené v potvrzovacím dialogovém okně a potvrďte, že jste připraveni zahájit proces migrace.

    > [!IMPORTANT]
    > Po zahájení migrace pro předplatné nebudete moct upravit ani vytvořit pravidla pro klasické výstrahy pro toto předplatné. Toto omezení zajistí, že během migrace na nová pravidla nebudou ztraceny žádné změny pravidel pro vaše Klasická upozornění. I když nebudete moct měnit pravidla vašich klasických výstrah, pořád se budou spouštět a zajišťují upozornění, dokud nebudou migrovány. Po dokončení migrace vašeho předplatného už nebudete moct používat pravidla pro klasických výstrah.

    ![Snímek obrazovky s výzvou k migraci zobrazí výzvu k potvrzení, včetně důležitých informací s odkazy, které vám pomají Další informace, než budete pokračovat.](media/alerts-migration/migration-confirm.png "Potvrdit spuštění migrace")

1. Po dokončení migrace nebo v případě, že se od vás vyžaduje akce, obdržíte e-mail na adrese, které jste zadali dříve. Na portálu můžete také pravidelně kontrolovat stav na cílové stránce migrace.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Proč je moje předplatné uvedené jako nepřipravené k migraci?

Nástroj pro migraci se zavede na zákazníky ve fázích. V počátečních fázích může být většina nebo všechna předplatná označena jako **nepřipravená pro migraci**. 

Když bude předplatné připravené k migraci, vlastník předplatného obdrží e-mailovou zprávu s oznámením, že nástroj je k dispozici. Ponechte si pro tuto zprávu nějaké oči.

### <a name="who-can-trigger-the-migration"></a>Kdo může migraci aktivovat?

Uživatelé, kteří mají přiřazenou roli Přispěvatel monitorování na úrovni předplatného, můžou migraci aktivovat. [Přečtěte si další informace o Role-Based Access Control procesu migrace](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak dlouho bude migrace trvat?

Migrace se dokončila pro většinu předplatných za hodinu. Průběh migrace můžete sledovat na cílové stránce migrace. Během migrace se musí zajistit, aby vaše výstrahy pořád běžely v systému klasické výstrahy nebo v novém.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co můžu udělat, když při migraci narazím na problém?

V [Průvodci odstraňováním potíží](alerts-understand-migration.md#common-problems-and-remedies) najdete pomoc s problémy, se kterými se můžete setkat při migraci. Pokud je potřeba, abyste migraci dokončili, budete informováni o e-mailových adresách, které jste zadali při nastavení nástroje.

## <a name="next-steps"></a>Další kroky

- [Příprava pro migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
