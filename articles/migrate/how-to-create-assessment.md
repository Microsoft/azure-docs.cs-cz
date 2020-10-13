---
title: Vytvoření posouzení virtuálního počítače Azure pomocí posouzení serveru Azure Migrate | Microsoft Docs
description: Popisuje, jak vytvořit posouzení virtuálního počítače Azure pomocí nástroje Azure Migrate Server Assessment Tool.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: c4132ca675af136d7fd50b8ddd02277919a5ed28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361088"
---
# <a name="create-an-azure-vm-assessment"></a>Vytvoření hodnocení virtuálních počítačů Azure

Tento článek popisuje, jak vytvořit vyhodnocení virtuálního počítače Azure pro místní virtuální počítače VMware nebo virtuální počítače Hyper-V s Azure Migrate: posouzení serveru.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran. 

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Chcete-li vytvořit posouzení, je třeba nastavit zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru. [Další informace](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Přehled posouzení virtuálních počítačů Azure
Existují dva typy kritérií změny velikosti, pomocí kterých můžete vytvořit vyhodnocení virtuálního počítače Azure pomocí Azure Migrate: posouzení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)**: na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě velikosti místního virtuálního počítače<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení.
2. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na možnost **vyhodnotit**.

    ![Snímek obrazovky ukazuje Azure Migrate servery s vyhodnocením vybraným v části nástroje pro posouzení.](./media/how-to-create-assessment/assess.png)

3. V okně **vyhodnotit servery**vyberte typ posouzení jako "virtuální počítač Azure", vyberte zdroj zjišťování a zadejte název posouzení.

    ![Základní informace o posouzení](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/how-to-create-assessment//view-all.png)

5. Kliknutím na **Další** přejděte do části **Vybrat počítače, které se mají posoudit**. V části **Vybrat nebo vytvořit skupinu** vyberte **Vytvořit novou** a zadejte název skupiny. Skupina sdružuje jeden nebo více virtuálních počítačů pro posouzení.
6. V části **přidat počítače do skupiny**vyberte virtuální počítače, které chcete do skupiny přidat.
7. Kliknutím na **Další** přejděte do části **Zkontrolovat a vytvořit posouzení** a zkontrolujte podrobnosti o posouzení.
8. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    ![Vytvoření posouzení](./media/how-to-create-assessment//assessment-create.png)

9. Po vytvoření můžete posouzení zobrazit v části **Servery** > **Azure Migrate: Hodnocení serverů** > **Posouzení**.
10. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-azure-vm-assessment"></a>Kontrola posouzení virtuálních počítačů Azure

Posouzení virtuálních počítačů Azure popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště:** Odhadované náklady na diskové úložiště po migraci

### <a name="view-an-azure-vm-assessment"></a>Zobrazit posouzení virtuálního počítače Azure

1. V případě **migrace**  >   na**serverech**klikněte na **posouzení** v **Azure Migrate: posouzení serveru**.
2. V **posouzení**klikněte na posouzení a otevřete ho.

    ![Souhrn posouzení](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti pro Azure

1. V **Azure Readiness**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure:** Azure Migrate pro virtuální počítače v posouzení doporučí velikost virtuálního počítače a odhady nákladů.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

3. Klikněte na stav **připravenosti na Azure** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítač a přejít k podrobnostem, kde najdete podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Kontrola podrobností o nákladech

Toto zobrazení informuje o odhadovaných nákladech na výpočetní prostředky a úložiště při provozu virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině.

    - Odhady nákladů vycházejí z doporučení velikosti počítače a z jeho disků a vlastností.
    - Zobrazí se odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti pro konkrétní virtuální počítače.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, bude posouzení k posouzení přiřazeno hodnocení spolehlivosti.

![Hodnocení spolehlivosti](./media/how-to-create-assessment/confidence-rating.png)

- Je uděleno hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti, která poskytuje posouzení.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.

Hodnocení spolehlivosti pro posouzení je následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček




## <a name="next-steps"></a>Další kroky

- Naučte se používat [Mapování závislostí](how-to-create-group-machine-dependencies.md) k vytváření vysoce důvěryhodných skupin.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
