---
title: Vytvoření posouzení služby AVS pomocí Azure Migrate posouzení serveru | Microsoft Docs
description: V této části najdete popis postupu vytvoření posouzení funkce AVS pomocí nástroje Azure Migrate Server Assessment Tool.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e386db1ee2042d75a31d4a9de2a5174e904c6b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732968"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Vytvoření posouzení řešení Azure VMware (AVS)

Tento článek popisuje, jak vytvořit vyhodnocení řešení Azure VMware (AVS) pro místní virtuální počítače VMware s Azure Migrate: posouzení serveru.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](./create-manage-projects.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Chcete-li vytvořit posouzení, je třeba nastavit zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md), které zjišťuje místní počítače a odesílá data o metadatech a výkonu do Azure Migrate: posouzení serveru. [Další informace](migrate-appliance.md).
- [Metadata serveru](./tutorial-discover-import.md) můžete také importovat ve formátu hodnot oddělených čárkami (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Přehled posouzení řešení Azure VMware (AVS)

Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. [Další informace](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md) k migraci do služby Azure VMware Solution (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Posouzení řešení Azure VMware (AVS) se dá vytvořit jenom pro virtuální počítače VMware.


Existují dva typy kritérií změny velikosti, které můžete použít k vytvoření posouzení služby Azure VMware Solution (AVS):

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu místních virtuálních počítačů. | **Doporučená velikost uzlu**: na základě dat o využití procesoru a paměti spolu s typem uzlu, typem úložiště a FTT nastavením, které jste vybrali pro posouzení.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost uzlu**: v závislosti na velikosti místního virtuálního počítače spolu s typem uzlu, typem úložiště a nastavením FTT, které jste vybrali pro posouzení.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Spuštění posouzení řešení Azure VMware (AVS)

1. Na stránce **servery** > **serverech se systémem Windows a Linux** klikněte na možnost **zhodnotit a migrovat servery**.

   ![Umístění tlačítka pro posouzení a migraci serverů](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. V **Azure Migrate: vyhodnocování serveru** klikněte na **vyhodnotit**.

1. V   >  případě vyhodnocení **typu vyhodnocení** serverů vyberte **Azure VMware Solution (AVS)**.

1. Ve **zdroji zjišťování**:

    - Pokud jste v zařízení zjistili počítače, vyberte **počítače zjištěné z Azure Migrate zařízení**.
    - Pokud jste zjistili počítače pomocí importovaného souboru CSV, vyberte **importovat počítače**. 
    
1. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Stránka pro výběr nastavení posouzení":::
 

1. Ve vlastnostech **posouzení** vlastnosti  >  **cíle**:

    - V části **cílové umístění** zadejte oblast Azure, do které chcete migrovat.
       - Doporučení pro velikost a náklady jsou založena na umístění, které zadáte.
   - **Typ úložiště** je nastaven na **síti vSAN**. Toto je výchozí typ úložiště pro privátní cloud služby AVS.
   - **Rezervované instance** se aktuálně pro uzly služby AVS nepodporují.
1. Ve **velikosti virtuálního počítače**:
    - **Typ uzlu** je nastaven na **AV36**. Azure Migrate doporučuje uzel uzlů potřebných k migraci virtuálních počítačů na funkci AVS.
    - V **Nastavení FTT (úroveň RAID**) vyberte možnost Netolerovat a kombinaci RAID.  Vybraná možnost FTT v kombinaci s požadavkem na místní disk virtuálního počítače Určuje celkové úložiště síti vSAN vyžadované v rámci funkce AVS.
    - V části přepočet virtuálních **procesorů** zadejte poměr virtuálních jader přidružených k jednomu fyzickému jádru v uzlu AVS. Přesáhne předplatné větší než 4:1 může způsobit snížení výkonu, ale dá se použít pro úlohy typu webový server.
    - V části faktor převzetí **paměti** zadejte poměr paměti nad potvrzením v clusteru. Hodnota 1 představuje 100% využití paměti, 0,5 například 50% a 2 by používalo 200% dostupné paměti. Hodnoty můžete přidat jenom od 0,5 do 10 až na jedno desetinné místo.
    - V části **deduplicity a kompresní faktor** určete očekávané deduplicity a kompresní faktor pro vaše úlohy. Skutečná hodnota se dá získat z místní síti vSAN nebo konfigurace úložiště a může se lišit podle úlohy. Hodnota 3 by znamenala 3x, takže se použije jenom úložiště 100 GB pro disk 300 GB. Hodnota 1 by znamenala žádné deduplicity ani komprimaci. Můžete přidat jenom hodnoty od 1 do 10 až na jedno desetinné místo.
1. V **uzlu velikost**: 
    - V části **kritéria změny velikosti** vyberte, pokud chcete vyhodnotit vyhodnocení statických metadat nebo dat na základě výkonu. Pokud používáte údaje o výkonu:
        - V části **Historie výkonu** určete dobu trvání dat, na které chcete vyhodnotit základ posouzení.
        - V části **využití percentilu** zadejte hodnotu percentilu, kterou chcete použít pro vzorek výkonu. 
    - V části **faktor komfortu** určete vyrovnávací paměť, kterou chcete použít při posuzování. Tyto účty jsou důležité pro problémy, jako je sezónní využití, historie krátkého výkonu a pravděpodobný nárůst využití v budoucnu. Pokud například použijete faktor komfortu 2:
    
        **Komponenta** | **Efektivní využití** | **Přidat faktor pohodlí (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memory (Paměť) | 8 GB | 16 GB  

1. V **ceně**:
    - V **nabídce** se zobrazí [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste zaregistrovali v nástroji, a zobrazí se odhad serveru. cena za tuto nabídku se vyhodnotí.
    - V části **Měna** vyberte fakturační měnu vašeho účtu.
    - V části **sleva (%)** přidejte do nabídky Azure všechny slevy specifické pro předplatné, které obdržíte. Výchozí nastavení je 0 %.

1. Pokud provedete změny, klikněte na **Uložit** .

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Vlastnosti posouzení":::

1. V nabídce **vyhodnotit servery** klikněte na **Další**.

1. V části **Vybrat počítače pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení. 
 
1. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Přidání virtuálních počítačů do skupiny":::
 
1. Vyberte zařízení a vyberte virtuální počítače, které chcete do skupiny přidat. Potom klikněte na **Další**.

1. V části **Revize + vytvořit posouzení** zkontrolujte podrobnosti posouzení a kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    > [!NOTE]
    > Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení počkali alespoň den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. V ideálním případě po zahájení zjišťování počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc) pro hodnocení s vysokou mírou jistoty.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Projděte si posouzení řešení Azure VMware (AVS).

Posouzení řešení Azure VMware (AVS) popisuje:

- **Připravenost k řešení Azure VMware (AVS)**: to, jestli jsou místní virtuální počítače vhodné pro migraci do řešení Azure VMware (AVS).
- **Počet uzlů pro funkci AVS**: odhadovaný počet uzlů AVS potřebných ke spuštění virtuálních počítačů.
- **Využití v uzlech služby AVS**: předpokládané využití procesoru, paměti a úložiště napříč všemi uzly.
    - Využití zahrnuje přední faktoring v následujících režijních clusterech, jako jsou vCenter Server, NSX Manager (velký), NSX Edge, pokud je nasazený HCX, a to i v případě, že je nasadí zařízení HCX Manager a IX. ~ 44vCPU (11 procesor), 75 GB paměti RAM a 722GB úložiště před komprimací a odstranění duplicitních dat.
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
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů v rámci služby AVS. Posouzení pro funkci AVS nebere v úvahu náklady na PaaS nebo SaaS.
    
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