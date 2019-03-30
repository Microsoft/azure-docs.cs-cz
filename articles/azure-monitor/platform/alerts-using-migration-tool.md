---
title: Migrace klasického upozornění ve službě Azure Monitor pomocí dobrovolné migrační nástroj
description: Další informace o použití dobrovolné migrační nástroj pro migraci klasického pravidel upozornění.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632104"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Použijte nástroj dobrovolné migrace k migraci klasického pravidel upozornění

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v červenci 2019. Nástroj pro migraci k aktivaci migrace dobrovolně je k dispozici na webu Azure portal a zavádí pro zákazníky, kteří používají klasický pravidla upozornění. Tento článek vás provede na tom, jak používat nástroj pro migraci dobrovolně migraci klasického pravidel upozornění před zahájením migrace automatické v červenci 2019.

## <a name="benefits-of-new-alerts"></a>Výhody nové výstrahy

Upozornění Classic se nahrazují nový jednotný výstrahy ve službě Azure Monitor. Novou platformu upozornění má následující výhody:

- Upozornění na širokou škálu vícedimenzionálních metrik pro [mnoho dalších služeb Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Nové metriky výstrahy podpory [pravidla upozornění více prostředky](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) což výrazně snížit režijní náklady správy mnoho pravidel.
- Mechanismus jednotné oznámení
  - [Skupiny akcí](action-groups.md) virtuálních sítí je mechanismus modulární oznámení, která funguje s všechny nové typy výstrah (metrik, protokolů a protokolu aktivity)
  - Také budete moct využívat nové oznámení mechanismy, jako je SMS, hlas a konektor ITSM
- [Jednotné rozhraní upozornění](alerts-overview.md) přináší všechny výstrahy na různým signálům (metriky, aktivity v protokolu a protokolu) do jednoho místa

## <a name="before-you-migrate"></a>Před zahájením migrace

Jako součást migrace klasické pravidla upozornění se převedou na ekvivalentní nová pravidla výstrahy a vytvořené skupiny akcí.

- Formát datová část oznámení, jakož i rozhraní API můžete vytvářet a spravovat nové pravidla upozornění se liší od těch, které classic pravidla upozornění jako podporují další funkce. Přečtěte si [postup přípravy na migraci](alerts-prepare-migration.md).

- Některé klasické pravidla upozornění se nedají migrovat pomocí nástroje. [Zjistěte, která pravidla nejsou migrovat a zjistit, jak je migrovat](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Proces migrace nebude mít vliv na vyhodnocení pravidel upozornění classic. Budou se ke spuštění a odeslání oznámení se migrují a pořídím nová pravidla upozornění.


## <a name="how-to-use-the-migration-tool"></a>Jak používat nástroj pro migraci

Následující postup popisuje, jak spustit migraci klasického pravidel upozornění na webu Azure portal:

1. V [webu Azure portal](https://portal.azure.com), klikněte na **monitorování**.

2. Klikněte na tlačítko **výstrahy** klikněte na **spravovat pravidla výstrah** nebo **zobrazit upozornění classic**.

3. Klikněte na tlačítko **migrace do nového pravidla** přejdete na úvodní stránku migrace. Tato stránka zobrazuje seznam všech předplatných a stavu migrace pro ně.

    ![migrace cílová](media/alerts-migration/migration-landing.png "migrovat pravidla")

4. Všechna předplatná, ke které je možné migrovat pomocí nástroje se označí jako **připraveno k migraci**.

    > [!NOTE]
    > Nástroj pro migraci zavádí ve fázích pro všechna předplatná, které používají klasický pravidla upozornění. V počátečních fázích zavádění může se zobrazit některá předplatná jako nepřipravený pro migraci.

5. Vyberte jeden nebo více odběrů a klikněte na **ve verzi Preview migrace**

6. Na této stránce můžete zobrazit podrobnosti classic pravidel upozornění, které se budou migrovat pro jedno předplatné najednou. Můžete také **stáhnout podrobnosti o migraci pro toto předplatné** ve formátu CSV.

    ![verzi preview migrace](media/alerts-migration/migration-preview.png "ve verzi Preview migrace")

7. Zadejte jeden nebo více **e-mailové adresy** abyste dostávali oznámení o stavu migrace. Po dokončení migrace nebo nic dělat z vám pošleme e-mailu.

8. Klikněte na **zahájit migraci**. Přečtěte si informace uvedené v potvrzovacím dialogovém okně a potvrďte, pokud jste připraveni zahájit proces migrace.

    >[!IMPORTANT]
    > Jakmile zahájíte proces migrace pro předplatné, nebudete moci úpravy a vytváření classic pravidla upozornění pro předplatné. Classic pravidel upozornění však budou spuštěna a umožní vám oznámení, dokud nebudou migrováni. Je to z toho věrnost mezi classic pravidla upozornění a nových pravidel vytvořených během migrace. Po dokončení migrace vašeho předplatného, nemůžete použít klasické pravidla upozornění zobrazovat.

    ![Potvrďte migraci](media/alerts-migration/migration-confirm.png "potvrdit migraci spustit")

9. Jako jsme dokončení migrace nebo potřebujete akci, obdrží e-mail na e-mailové adresy uvedené v kroku 8. Můžete také pravidelně zkontrolovat stav z cílové stránky migrace na portálu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Proč se Moje předplatná uvedená jako není připraveno k migraci?**

Nástroj pro migraci zavádí ve fázích pro všechny zákazníky. V počátečních fázích, většinu nebo všechny vaše předplatné může být označený jako **není připravený k migraci**. Pomocí funkce část dne, by měl být připravení migrovat všechna předplatná.

Až bude předplatné připravené k migraci, vlastníky těchto předplatných obdrží e-mail s upozorněním dostupnost nástroje. Sledujte toto oznámení.

### <a name="who-can-trigger-the-migration"></a>**Kdo může aktivovat migrace?**

K aktivaci migrace budou mít uživatelé, kteří mají roli Přispěvatel monitorování přiřazené na úrovni předplatného. Další informace o [roli řízení přístupu na základě pro proces migrace](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Jak dlouho se migrace bude trvat?**

Pro většinu předplatných dokončení migrace obvykle méně než hodinu. Vám může udržovat přehled o průběhu migrace z cílové stránky migrace.  Během této doby prosím zajistíte, že vaše upozornění jsou pořád spuštěné v systému klasických upozornění nebo novým.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Co mám dělat, když dojde k problému při migraci?**

Postupujte prosím podle [nápravy v případě problémů může setkat během migrace najdete v příručce pro řešení potíží s](alerts-understand-migration.md#common-issues-and-remediations). V případě potřeby jakékoli akce od vás k dokončení migrace budete upozorněni na e-mailové adresy k dispozici během migrace.

## <a name="next-steps"></a>Další postup

- [Příprava na migraci](alerts-prepare-migration.md)
- [Pochopit, jak funguje nástroj pro migraci](alerts-understand-migration.md)
