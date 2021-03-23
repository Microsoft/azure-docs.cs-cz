---
title: Vytvoření posouzení virtuálního počítače Azure pomocí nástroje pro zjišťování a hodnocení služby Azure Migrate | Microsoft Docs
description: Popisuje, jak vytvořit posouzení virtuálního počítače Azure pomocí nástroje Azure Migrate Discovery and Assessment Tool.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786715"
---
# <a name="create-an-azure-vm-assessment"></a>Vytvoření hodnocení virtuálních počítačů Azure

Tento článek popisuje, jak vytvořit vyhodnocení virtuálního počítače Azure pro místní servery v rámci VMware, Hyper-V nebo fyzického/jiného cloudového prostředí s Azure Migrate: zjišťování a posouzení.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran. 

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](./create-manage-projects.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) nástroj Azure Migrate: Discovery and Assessment Tool.
- Chcete-li vytvořit posouzení, je třeba nastavit zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní servery a odesílá data o metadatech a výkonu Azure Migrate: zjišťování a posouzení. [Další informace](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Přehled posouzení virtuálních počítačů Azure
Existují dva typy kritérií změny velikosti, pomocí kterých můžete vytvořit vyhodnocení virtuálního počítače Azure pomocí Azure Migrate: zjišťování a posouzení.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)**: na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě velikosti místního virtuálního počítače<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Na stránce **přehled** > **Windows, Linux a SQL Server** klikněte na možnost **zhodnotit a migrovat servery**.

   ![Umístění tlačítka pro posouzení a migraci serverů](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. V **Azure Migrate: zjišťování a hodnocení** klikněte na **zhodnotit** a vyberte **virtuální počítač Azure** .

    ![Umístění tlačítka pro vyhodnocení](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. V **vyhodnocení**  >  **typu vyhodnocení** serverů
4. Ve **zdroji zjišťování**:

    - Pokud jste zjistili servery, které používají zařízení, vyberte **servery zjištěné z Azure Migrate zařízení**.
    - Pokud jste servery zjistili pomocí importovaného souboru CSV, vyberte **importované servery**. 
    
1. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.

    ![Umístění tlačítka Zobrazit vše pro kontrolu vlastností posouzení](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

1. Ve vlastnostech **posouzení** vlastnosti  >  **cíle**:
    - V části **cílové umístění** zadejte oblast Azure, do které chcete migrovat.
        - Doporučení pro velikost a náklady jsou založena na umístění, které zadáte. Po změně cílového umístění z výchozího nastavení budete vyzváni k zadání **rezervovaných instancí** a **řady virtuálních počítačů**.
        - V Azure Government můžete cílit na posouzení v [těchto oblastech](migrate-support-matrix.md#supported-geographies-azure-government) .
    - V **typu úložiště**
        - Pokud chcete v rámci posouzení použít data založená na výkonu, vyberte možnost **automaticky** pro Azure Migrate, která doporučuje typ úložiště, a to na základě vstupně-výstupních operací disku a propustnosti.
        - Případně vyberte typ úložiště, který chcete použít pro virtuální počítač, když ho migrujete.
    - V části **rezervované instance** určete, jestli chcete pro virtuální počítač použít rezervované instance, když ho migrujete.
        - Pokud vyberete možnost použití rezervované instance, nemůžete zadat hodnotu "**sleva (%)**" nebo **Doba provozu virtuálního počítače**. 
        - [Další informace](https://aka.ms/azurereservedinstances).
 1. Ve **velikosti virtuálního počítače**:
     - V části **kritéria změny velikosti** vyberte, pokud chcete vyhodnotit vyhodnocení pro data konfigurace serveru/metadata nebo na data založená na výkonu. Pokud používáte údaje o výkonu:
        - V části **Historie výkonu** určete dobu trvání dat, na které chcete vyhodnotit základ posouzení.
        - V části **využití percentilu** zadejte hodnotu percentilu, kterou chcete použít pro vzorek výkonu. 
    - V poli **série virtuálních počítačů** určete řadu virtuálních počítačů Azure, které chcete zvážit.
        - Pokud používáte posouzení na základě výkonu, Azure Migrate navrhne hodnotu.
        - Podle potřeby selepšit nastavení. Pokud například nemáte produkční prostředí, které potřebuje pro virtuální počítače řady A-Series v Azure, můžete ze seznamu řad vyloučit řady.
    - V části **faktor komfortu** určete vyrovnávací paměť, kterou chcete použít při posuzování. Tyto účty jsou důležité pro problémy, jako je sezónní využití, historie krátkého výkonu a pravděpodobný nárůst využití v budoucnu. Pokud například použijete faktor komfortu 2:
    
        **Komponenta** | **Efektivní využití** | **Přidat faktor pohodlí (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memory (Paměť) | 8 GB | 16 GB
   
1. V **ceně**:
    - Pokud jste zaregistrovaní, zadejte v **nabídce** nabídku [Azure](https://azure.microsoft.com/support/legal/offer-details/) . Posouzení odhaduje náklady na tuto nabídku.
    - V části **Měna** vyberte fakturační měnu vašeho účtu.
    - V části **sleva (%)** přidejte do nabídky Azure všechny slevy specifické pro předplatné, které obdržíte. Výchozí nastavení je 0 %.
    - V části **Doba provozu virtuálního počítače** zadejte dobu (ve dnech měsíčně za hodinu za den), po kterou budou virtuální počítače běžet.
        - To je užitečné pro virtuální počítače Azure, které neběží nepřetržitě.
        - Odhad nákladů vychází z určeného trvání.
        - Výchozí hodnota je 31 dní za měsíc/24 hodin denně.
    - V **předplatném EA** určete, jestli se má při odhadu nákladů vzít v úvahu sleva předplatného smlouva Enterprise (EA). 
    - V **zvýhodněné hybridní využití Azure** určete, jestli už máte licenci Windows serveru. Pokud tak učiníte a jsou zahrnuté v aktivním programu Software Assurance předplatných Windows serveru, můžete požádat o [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) , když přenášíte licence do Azure.

1. Pokud provedete změny, klikněte na **Uložit** .

    ![Vlastnosti posouzení](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. V **vyhodnocování serverů** > klikněte na **Další**.

1. V části **Vybrat servery pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení. 

1. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny. 
    
     ![Přidání virtuálních počítačů do skupiny](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. Vyberte zařízení a vyberte virtuální počítače, které chcete do skupiny přidat. Potom klikněte na **Další**.


1. V části **Revize + vytvořit posouzení** zkontrolujte podrobnosti posouzení a kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

1. Po vytvoření posouzení ho zobrazte na **serverech**  >  **Azure Migrate: zjišťování a posouzení posouzení**  >  .

1. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.
    > [!NOTE]
    > Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení počkali alespoň den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. V ideálním případě po zahájení zjišťování počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc) pro hodnocení s vysokou mírou jistoty.

## <a name="review-an-azure-vm-assessment"></a>Kontrola posouzení virtuálních počítačů Azure

Posouzení virtuálních počítačů Azure popisuje:

- **Připravenost pro Azure:** Určuje, jestli jsou servery vhodné k migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště:** Odhadované náklady na diskové úložiště po migraci

### <a name="view-an-azure-vm-assessment"></a>Zobrazit posouzení virtuálního počítače Azure

1. V **systémech Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a hodnocení** klikněte na číslo vedle pole **Azure VM Assessment**.
2. Výběrem posouzení v části **Posouzení** ho otevřete. Příklad (odhad a náklady pouze pro příklad): 

    ![Souhrn posouzení](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti pro Azure

1. V **Azure Readiness** ověřte, jestli jsou servery připravené na migraci do Azure.
2. Zkontrolujte stav serveru:
    - **Připraveno pro Azure:** Azure Migrate pro virtuální počítače v posouzení doporučí velikost virtuálního počítače a odhady nákladů.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

3. Klikněte na stav **připravenosti na Azure** . Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem a zobrazit podrobnosti o serveru, včetně výpočetních prostředků, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Kontrola podrobností o nákladech

Toto zobrazení informuje o odhadovaných nákladech na výpočetní prostředky a úložiště při provozu virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se agregují pro všechny servery v posuzované skupině.

    - Odhad nákladů vychází z doporučení na velikost serveru a jeho disků a vlastností.
    - Zobrazí se odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních serverů jako virtuálních počítačů IaaS. Posouzení virtuálního počítače Azure nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti o konkrétních serverech.


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