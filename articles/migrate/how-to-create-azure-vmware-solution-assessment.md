---
title: Vytvoření posouzení služby AVS pomocí Azure Migrate posouzení serveru | Microsoft Docs
description: V této části najdete popis postupu vytvoření posouzení funkce AVS pomocí nástroje Azure Migrate Server Assessment Tool.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fb1ec55bc68ccc323f8dee90982a9169e3085219
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567651"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Vytvoření posouzení řešení Azure VMware (AVS)

Tento článek popisuje, jak vytvořit vyhodnocení řešení Azure VMware (AVS) pro místní virtuální počítače VMware s Azure Migrate: posouzení serveru.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](./create-manage-projects.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Chcete-li vytvořit posouzení, je třeba nastavit zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md), které zjišťuje místní počítače a odesílá data o metadatech a výkonu do Azure Migrate: posouzení serveru. [Přečtěte si další informace](migrate-appliance.md).
- [Metadata serveru](./tutorial-discover-import.md) můžete také importovat ve formátu hodnot oddělených čárkami (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Přehled posouzení řešení Azure VMware (AVS)

Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. [Další informace](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md) k migraci do služby Azure VMware Solution (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Posouzení řešení Azure VMware (AVS) je aktuálně ve verzi Preview a je možné ho vytvořit jenom pro virtuální počítače VMware.


Existují dva typy kritérií změny velikosti, které můžete použít k vytvoření posouzení služby Azure VMware Solution (AVS):

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu místních virtuálních počítačů. | **Doporučená velikost uzlu**: na základě dat o využití procesoru a paměti spolu s typem uzlu, typem úložiště a FTT nastavením, které jste vybrali pro posouzení.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost uzlu**: v závislosti na velikosti místního virtuálního počítače spolu s typem uzlu, typem úložiště a nastavením FTT, které jste vybrali pro posouzení.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Spuštění posouzení řešení Azure VMware (AVS)

Spusťte posouzení řešení Azure VMware (AVS) následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření posouzení.

2. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na možnost **vyhodnotit**.

    ![Snímek obrazovky ukazuje Azure Migrate servery s vyhodnocením vybraným v části nástroje pro posouzení.](./media/how-to-create-assessment/assess.png)

3. V okně **vyhodnotit servery** vyberte typ posouzení jako "Azure VMware Solution (AVS)", vyberte zdroj zjišťování.

    :::image type="content" source="./media/how-to-create-avs-assessment/assess-servers-avs.png" alt-text="Přidat základy hodnocení":::

4. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Umístění tlačítka pro úpravy pro kontrolu vlastností posouzení":::

1. V části **Vybrat počítače pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení. 
 
1. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny. Skupina sdružuje jeden nebo více virtuálních počítačů pro posouzení.
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Přidání virtuálních počítačů do skupiny":::

1. V části **přidat počítače do skupiny** vyberte virtuální počítače, které chcete do skupiny přidat.

1. Kliknutím na **Další** přejděte do části **Zkontrolovat a vytvořit posouzení** a zkontrolujte podrobnosti o posouzení.

1. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

1. Po vytvoření můžete posouzení zobrazit v části **Servery** > **Azure Migrate: Hodnocení serverů** > **Posouzení**.

1. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Projděte si posouzení řešení Azure VMware (AVS).

Posouzení řešení Azure VMware (AVS) popisuje:

- **Připravenost k řešení Azure VMware (AVS)**: to, jestli jsou místní virtuální počítače vhodné pro migraci do řešení Azure VMware (AVS).
- **Počet uzlů pro funkci AVS**: odhadovaný počet uzlů AVS potřebných ke spuštění virtuálních počítačů.
- **Využití v uzlech služby AVS**: předpokládané využití procesoru, paměti a úložiště napříč všemi uzly.
    - Využití zahrnuje přední faktoring v následujících režijních clusterech, jako jsou vCenter Server, NSX Manager (velký), NSX Edge, pokud je nasazený HCX, a to i v případě, že je nasadí zařízení HCX Manager a IX. ~ 44vCPU (11 procesor), 75 GB paměti RAM a 722GB úložiště před komprimací a odstranění duplicitních dat.
    - Paměť, deduplicity a komprese jsou aktuálně nastavené na 100% využití paměti a 1,5 deduplicity a komprimace, které budou uživatelem definovaným vstupem v přidaných verzích dále umožňující uživateli vyladit požadované změny velikosti.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na všechny uzly řešení Azure VMware (AVS), na kterých běží místní virtuální počítače.


### <a name="view-an-assessment"></a>Zobrazení posouzení

1. V případě **migrace**  >   na **serverech** klikněte na **posouzení** v **Azure Migrate: posouzení serveru**.

2. V **posouzení** klikněte na posouzení a otevřete ho.

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="Souhrn posouzení pro funkci AVS":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>Kontrola připravenosti řešení Azure VMware (AVS)

1. V **Azure Readiness** ověřte, jestli jsou virtuální počítače připravené k migraci na službu AVS.

2. Zkontrolujte stav virtuálního počítače:
    - **Připraveno k funkci AVS**: počítač se dá migrovat tak, jak je, do Azure (AVS) bez jakýchkoli změn. Spustí se v programu AVS s plnou podporou AVS.
    - **Připraveno s podmínkami**: virtuální počítač může mít problémy s kompatibilitou s aktuální verzí vSphere a vyžadovat nástroje VMware a další nastavení před tím, než bude možné dosáhnout plné funkčnosti z virtuálního počítače v prostředí AVS.
    - **Není připravené na službu AVS**: virtuální počítač se nespustí v prostředí AVS. Pokud má například místní virtuální počítač VMware připojené externí zařízení, například disk CD-ROM, operace VMotion se nezdaří (Pokud používáte VMware VMotion).
    - **Připravenost neznámá**: Azure Migrate nedokázala určit připravenost počítače kvůli nedostatečným metadatům shromážděným z místního prostředí.

3. Projděte si navrhovaný nástroj:
    - **VMware HCX nebo Enterprise**: pro počítače VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)
    - **Neznámý:** V případě počítačů importovaných prostřednictvím souboru CSV je výchozí nástroj pro migraci neznámý. Ale u počítačů VMware je navrženo použití řešení VMware Hybrid Cloud Extension (HCX). 

4. Klikněte na stav **připravenosti na AVS** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítač a přejít k podrobnostem, kde najdete podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Kontrola podrobností o nákladech

Toto zobrazení ukazuje odhadované náklady na běžící virtuální počítače v řešení Azure VMware (AVS).

1. Zkontrolujte měsíční celkové náklady. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině. 

    - Odhadované náklady vycházejí z počtu uzlů AVS, které jsou potřeba k tomu, aby se všechny virtuální počítače celkově vyžádaly.
    - Jelikož jsou ceny za Azure VMware Solution (AVS) na jeden uzel, celkové náklady na výpočetní náklady a distribuci nákladů na úložiště se nepočítají.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů v rámci služby AVS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.
    
2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.

3. Můžete přejít k podrobnostem a zobrazit podrobnosti pro konkrétní virtuální počítače.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, bude posouzení k posouzení přiřazeno hodnocení spolehlivosti.

![Hodnocení spolehlivosti](./media/how-to-create-assessment/confidence-rating.png)

- Je uděleno hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti, která poskytuje posouzení.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.
- Pro určení velikosti na základě výkonu potřebují posouzení služby AVS v rámci posuzování serveru data o využití procesoru a paměti virtuálního počítače. Následující údaje o výkonu se shromažďují, ale nepoužívají se při určování velikosti doporučení pro vyhodnocení pro funkci AVS:
  - IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
  - I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

Hodnocení spolehlivosti pro posouzení je následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Další informace](concepts-azure-vmware-solution-assessment-calculation.md) o datech výkonu 


## <a name="next-steps"></a>Další kroky

- Naučte se používat [Mapování závislostí](how-to-create-group-machine-dependencies.md) k vytváření vysoce důvěryhodných skupin.
- [Přečtěte si víc](concepts-azure-vmware-solution-assessment-calculation.md) o tom, jak se vypočítávají vyhodnocení funkce AVS.