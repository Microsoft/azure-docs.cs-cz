---
title: Migrace pravidel upozornění Azure Monitor
description: Naučte se používat dobrovolný migrační nástroj k migraci pravidel vašich klasických výstrah.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fa487bec49ab9faa0f7c3dce752a30e4440fb873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037672"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Migrace pravidel vašich klasických výstrah pomocí nástroje k dobrovolné migraci

Jak jsme [dřív oznámili](monitoring-classic-retirement.md), klasické výstrahy v Azure monitor jsou vyřazení pro uživatele veřejného cloudu, i když jsou i v omezeném počtu použití až do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.

Nástroj pro migraci je v Azure Portal k dispozici pro zákazníky, kteří použili pravidla pro klasických výstrah a chtějí spustit migraci sami. Tento článek vysvětluje, jak používat nástroj pro migraci.

## <a name="before-you-migrate"></a>Před migrací

Proces migrace převede pravidla klasických výstrah na nová, ekvivalentní pravidla upozornění a vytvoří skupiny akcí. V přípravě si pamatujte na tyto body:

- Formát datové části oznámení i rozhraní API k vytváření a správě nových pravidel výstrah se liší od pravidel pro klasických výstrah, protože podporují více funkcí. [Přečtěte si, jak připravit na migraci](alerts-prepare-migration.md).

- Některá z klasických pravidel upozornění nelze pomocí tohoto nástroje migrovat. [Zjistěte, která pravidla se nedají migrovat a co s nimi dělat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Proces migrace nebude mít vliv na hodnocení vašich klasických pravidel upozornění. Budou i nadále spouštět a odesílat výstrahy, dokud nebudou migrovány a nová pravidla upozornění vstoupí v platnost.

## <a name="how-to-use-the-migration-tool"></a>Způsoby použití nástroje pro migraci

Pokud chcete aktivovat migraci pravidel vašich klasických výstrah v Azure Portal, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)vyberte **monitorování**.

1. Vyberte **výstrahy** a pak vyberte **Spravovat pravidla výstrah** nebo **Zobrazit klasické výstrahy**.

1. Vyberte **migrovat na nová pravidla** a přejdete na úvodní stránku migrace. Tato stránka zobrazuje seznam všech předplatných a jejich stav migrace:

    ![Snímek obrazovky zobrazující stránku pravidla upozornění migrace](media/alerts-using-migration-tool/migration-landing.png "Migrace pravidel")

    Všechna předplatná, která je možné migrovat pomocí nástroje, jsou označená jako **připravená k migraci**.

    > [!NOTE]
    > Nástroj pro migraci se provádí ve fázích pro všechna předplatná, která používají pravidla pro klasických výstrah. V počátečních fázích zavedení se můžou zobrazit předplatná, která jsou označená jako nepřipravená pro migraci.

1. Vyberte jedno nebo více předplatných a potom vyberte možnost **migrace náhledu**.

    Výsledná stránka obsahuje podrobné informace o pravidlech výstrah, která se budou migrovat pro jedno předplatné najednou. Můžete také vybrat možnost **Stáhnout podrobnosti o migraci pro toto předplatné** a získat podrobnosti ve formátu CSV.

    ![Snímek obrazovky zobrazující stránku pravidla upozornění migrace s odkazem ke stažení podrobností o migraci pro toto předplatné a pro oznámení o migraci můžete zadat e-mail.](media/alerts-using-migration-tool/migration-preview.png "Náhled migrace")

1. Zadejte jednu nebo více e-mailových adres, které budou upozorňovány na stav migrace. Po dokončení migrace obdržíte e-mail nebo budete potřebovat nějakou akci.

1. Vyberte **Spustit migraci**. Přečtěte si informace uvedené v potvrzovacím dialogovém okně a potvrďte, že jste připraveni zahájit proces migrace.

    > [!IMPORTANT]
    > Po zahájení migrace pro předplatné nebudete moct upravit ani vytvořit pravidla pro klasické výstrahy pro toto předplatné. Toto omezení zajistí, že během migrace na nová pravidla nebudou ztraceny žádné změny pravidel pro vaše Klasická upozornění. I když nebudete moct měnit pravidla vašich klasických výstrah, pořád se budou spouštět a zajišťují upozornění, dokud nebudou migrovány. Po dokončení migrace vašeho předplatného už nebudete moct používat pravidla pro klasických výstrah.

    ![Snímek obrazovky s výzvou k migraci zobrazí výzvu k potvrzení, včetně důležitých informací s odkazy, které vám pomají Další informace, než budete pokračovat.](media/alerts-using-migration-tool/migration-confirm.png "Potvrdit spuštění migrace")

1. Po dokončení migrace nebo v případě, že se od vás vyžaduje akce, obdržíte e-mail na adrese, které jste zadali dříve. Na portálu můžete také pravidelně kontrolovat stav na cílové stránce migrace.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Proč je moje předplatné uvedené jako nepřipravené k migraci?

Nástroj pro migraci se zavede na zákazníky ve fázích. V počátečních fázích může být většina nebo všechna předplatná označena jako **nepřipravená pro migraci**. 

Když bude předplatné připravené k migraci, vlastník předplatného obdrží e-mailovou zprávu s oznámením, že nástroj je k dispozici. Ponechte si pro tuto zprávu nějaké oči.

### <a name="who-can-trigger-the-migration"></a>Kdo může migraci aktivovat?

Uživatelé, kteří mají přiřazenou roli Přispěvatel monitorování na úrovni předplatného, můžou aktivovat migraci. [Přečtěte si další informace o řízení přístupu na základě role v Azure pro proces migrace](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak dlouho bude migrace trvat?

Migrace se dokončila pro většinu předplatných za hodinu. Průběh migrace můžete sledovat na cílové stránce migrace. Během migrace se musí zajistit, aby vaše výstrahy pořád běžely v systému klasické výstrahy nebo v novém.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co můžu udělat, když při migraci narazím na problém?

V [Průvodci odstraňováním potíží](alerts-understand-migration.md#common-problems-and-remedies) najdete pomoc s problémy, se kterými se můžete setkat při migraci. Pokud je potřeba, abyste migraci dokončili, budete informováni o e-mailových adresách, které jste zadali při nastavení nástroje.

## <a name="next-steps"></a>Další kroky

- [Příprava na migraci](alerts-prepare-migration.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)