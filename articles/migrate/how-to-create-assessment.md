---
title: Vytvoření hodnocení pomocí azure migrate server assessment | Dokumenty společnosti Microsoft
description: Popisuje, jak vytvořit hodnocení pomocí nástroje Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229099"
---
# <a name="create-an-assessment"></a>Vytvoření posouzení

Tento článek popisuje, jak vytvořit hodnocení pro místní virtuální počítače VMware nebo virtuální počítače Hyper-V s Azure Migrate: Server Assessment.

[Migrace Azure](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran. 

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Migrace Azure.
- Pokud jste už vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) nástroj Azure Migrate: Server Assessment.
- Chcete-li vytvořit hodnocení, musíte nastavit zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní počítače a odesílá metadata a data o výkonu do Azure Migrate: Server Assessment. [Další informace](migrate-appliance.md).


## <a name="assessment-overview"></a>Přehled posouzení
Existují dva typy hodnocení, které můžete vytvořit pomocí Migrace Azure: Vyhodnocení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na shromážděných údajích o výkonnosti | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vodítek viopa a propustnost místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování. | **Doporučená velikost virtuálního počítače:** Na základě místní velikosti virtuálního počítače<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které vyberete pro posouzení.

[Přečtěte si další informace](concepts-assessment-calculation.md) o hodnoceních.

## <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte hodnocení takto:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **Servery** klikněte na dlaždici **Azure Migrate: Server Assessment** na **Assess**.

    ![Posouzení](./media/how-to-create-assessment/assess.png)

2. V **poli Posoudit servery**zadejte název hodnocení.
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/how-to-create-assessment//view-all.png)

3. V **poli Vybrat nebo vytvořit skupinu**vyberte Vytvořit **nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních virtuálních byl v usa z hodnocení.
4. V **části Přidat počítače do skupiny**vyberte virtuální počítače, které chcete přidat do skupiny.
5. Kliknutím na **Vytvořit hodnocení** vytvořte skupinu a spusťte hodnocení.

    ![Vytvoření posouzení](./media/how-to-create-assessment//assessment-create.png)

6. Po vytvoření hodnocení jej zobrazte v **části Servery, které** > **Azure migruje: Hodnocení hodnocení** > **serveru**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Přezkoumat posouzení

Hodnocení popisuje:

- **Připravenost Azure:** Jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Měsíční odhad nákladů**: Odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění virtuálních počítačů v Azure.
- Měsíční odhad nákladů na úložiště : Odhadované náklady na diskové úložiště po **migraci.**

### <a name="view-an-assessment"></a>Zobrazit hodnocení

1. V**oblasti serverů** **cílů** >  migrace klikněte na **Hodnocení** v **Azure Migrate: Server Assessment**.
2. V **hodnocení**, klikněte na hodnocení otevřít.

    ![Shrnutí hodnocení](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

1. V **připravenosti Azure**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního soudu:
    - **Připraveno pro Azure**: Azure Migrate doporučuje odhady velikosti virtuálních počítače a nákladů pro virtuální počítače v hodnocení.
    - **Připraveno s podmínkami**: Zobrazuje problémy a navrhovanou nápravu.
    - **Není připraven pro Azure**: Zobrazuje problémy a navrhované nápravy.
    - **Připravenost neznámý**: Používá se v případě, že Azure Migrate nemůže posoudit připravenost, z důvodu problémů s dostupností dat.

2. Klikněte na stav **připravenosti Azure.** Můžete zobrazit podrobnosti o připravenosti virtuálního počítače a přejít k podrobnostem, abyste viděli podrobnosti o virtuálním počítači, včetně výpočetních, úložných a síťových nastavení.



### <a name="review-cost-details"></a>Zkontrolovat podrobnosti o nákladech

Toto zobrazení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění virtuálních počítačů v Azure.

1. Zkontrolujte měsíční náklady na výpočetní prostředky a úložiště. Náklady se agregují pro všechny virtuální společnosti v posuzované skupině.

    - Odhady nákladů jsou založeny na doporučení velikosti pro počítač a jeho disky a vlastnosti.
    - Jsou zobrazeny odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů je pro spuštění místních virtuálních počítačů jako virtuálních počítačích IaaS. Azure Migrate Server Assessment nebere v úvahu PaaS nebo SaaS náklady.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení zobrazuje agregované náklady na úložiště pro vyměřenou skupinu rozdělenou na různé typy disků úložiště.
3. Podrobnosti zobrazíte podrobnostmi o konkrétních virtuálních dětech.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Při spuštění hodnocení na základě výkonu je hodnocení přiřazeno hodnocení.

![Hodnocení spolehlivosti](./media/how-to-create-assessment/confidence-rating.png)

- Uděluje se hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti, která hodnocení poskytuje.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu hodnocení.

Hodnocení spolehlivosti pro hodnocení je následující.

**Dostupnost datových bodů** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček




## <a name="next-steps"></a>Další kroky

- Zjistěte, jak pomocí [mapování závislostí](how-to-create-group-machine-dependencies.md) vytvořit skupiny s vysokou spolehlivostí.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
