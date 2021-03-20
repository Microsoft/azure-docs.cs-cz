---
title: Vyhodnocení instancí virtuálních počítačů s GCP pro migraci do Azure pomocí posouzení serveru Azure Migrate
description: Popisuje, jak vyhodnotit instance virtuálních počítačů s GCP pro migraci do Azure pomocí posouzení serveru Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: b5e6a0cd58fca954646640e43a81155822cdba04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567003"
---
# <a name="tutorial-assess-google-cloud-platform-gcp-vm-instances-for-migration-to-azure"></a>Kurz: vyhodnocení instancí virtuálních počítačů Google Cloud Platform (GCP) pro migraci do Azure

V rámci cesty migrace do Azure vyhodnocujete vaše místní úlohy, abyste zjistili připravenost na Cloud, zjistili rizika a odhadované náklady a složitost.

V tomto článku se dozvíte, jak vyhodnocovat instance virtuálních počítačů s Google Cloud Platform (GCP) pro migraci do Azure pomocí nástroje Azure Migrate: Server Assessment Tool.

V tomto kurzu se naučíte:
> [!div class="checklist"]
- Spusťte posouzení na základě metadat počítače a informací o konfiguraci.
- Spusťte posouzení na základě údajů o výkonu.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky

- Než budete postupovat podle kroků v tomto kurzu, dokončete první kurz v této sérii a [Zjistěte svůj místní inventář](tutorial-discover-gcp.md). 


## <a name="decide-which-assessment-to-run"></a>Rozhodněte, které posouzení se má spustit.

Rozhodněte, jestli chcete spustit posouzení pomocí kritérií pro změnu velikosti na základě dat konfigurace počítače/metadat, která se shromažďují místně, nebo na základě dat o výkonu.

**Posouzení** | **Podrobnosti** | **Doporučení**
--- | --- | ---
**Jako v místním prostředí** | Vyhodnoťte na základě dat nebo metadat konfigurace počítače.  | Doporučená velikost virtuálního počítače Azure je založena na velikosti místního virtuálního počítače.<br/><br> Doporučený typ disku Azure je založený na tom, co jste vybrali v nastavení typ úložiště v posouzení.
**Na základě výkonu** | Vyhodnoťte na základě shromážděných dat o výkonu. | Doporučená velikost virtuálního počítače Azure vychází z dat využití procesoru a paměti.<br/><br/> Doporučený typ disku vychází z IOPS a propustnosti místních disků.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Na stránce **servery** > **serverech se systémem Windows a Linux** klikněte na možnost **zhodnotit a migrovat servery**.

   ![Umístění tlačítka pro posouzení a migraci serverů](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. V **Azure Migrate: vyhodnocování serveru** klikněte na **vyhodnotit**.

    ![Umístění tlačítka pro vyhodnocení](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. V   >  případě vyhodnocení **typu vyhodnocení** serverů vyberte **virtuální počítač Azure**.
4. Ve **zdroji zjišťování**:

    - Pokud jste v zařízení zjistili počítače, vyberte **počítače zjištěné z Azure Migrate zařízení**.
    - Pokud jste zjistili počítače pomocí importovaného souboru CSV, vyberte **importovat počítače**. 
    
1. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Umístění tlačítka pro úpravy pro kontrolu vlastností posouzení":::

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
     - V části **kritéria změny velikosti** vyberte, pokud chcete vyhodnotit vyhodnocení pro data konfigurace počítače/metadata nebo na data založená na výkonu. Pokud používáte údaje o výkonu:
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
    - Pokud jste zaregistrovaní, zadejte v **nabídce** nabídku [Azure](https://azure.microsoft.com/support/legal/offer-details/) . Posouzení serveru odhaduje náklady na tuto nabídku.
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

1. V části **Vybrat počítače pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení. 

1. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny. 
    
    :::image type="content" source="./media/tutorial-assess-physical/assess-group.png" alt-text="Přidání virtuálních počítačů do skupiny":::


1. Vyberte zařízení a vyberte virtuální počítače, které chcete do skupiny přidat. Potom klikněte na **Další**.


1. V části **Revize + vytvořit posouzení** zkontrolujte podrobnosti posouzení a kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

1. Po vytvoření můžete posouzení zobrazit v části **Servery** > **Azure Migrate: Hodnocení serverů** > **Posouzení**.

1. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.
    > [!NOTE]
    > Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení počkali alespoň den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. V ideálním případě po zahájení zjišťování počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc) pro hodnocení s vysokou mírou jistoty.

## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště:** Odhadované náklady na diskové úložiště po migraci

Zobrazení posouzení:

1. V části **servery**  >  **Azure Migrate: vyhodnocování serveru** klikněte na číslo vedle **posouzení**.
2. Výběrem posouzení v části **Posouzení** ho otevřete. Příklad (odhad a náklady pouze pro příklad): 

    ![Souhrn posouzení](./media/tutorial-assess-gcp/assessment-summary.png)

3. Přečtěte si Souhrn posouzení. Můžete také upravit vlastnosti posouzení nebo přepočítat vyhodnocení.
 
 
### <a name="review-readiness"></a>Kontrola připravenosti

1. Klikněte na **připravenost na Azure**.
2. V části **připravenost na Azure** zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: používá se, když Azure Migrate pro virtuální počítače v posouzení doporučuje velikost virtuálního počítače a odhad nákladů.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nemůže vyhodnotit připravenost, protože dojde k problémům s dostupností dat.

3. Vyberte stav **připravenosti pro Azure**. Můžete si prohlédnout podrobnosti připravenosti na virtuální počítače. Můžete také přejít k podrobnostem a zobrazit podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů

Souhrn posouzení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure. 

1. Zkontrolujte měsíční celkové náklady. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině.

    - Odhad nákladů vychází z doporučení týkajících se velikosti počítače, jeho disků a vlastností.
    - Zobrazí se odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů na virtuálních počítačích Azure. Odhad nebere v úvahu náklady na PaaS nebo SaaS.

2. Projděte si měsíční náklady na úložiště. Zobrazení ukazuje agregované náklady na úložiště pro skupinu pohodnocenou a rozdělené přes různé typy disků úložiště. 
3. Můžete přejít k podrobnostem a zobrazit podrobnosti o cenách pro konkrétní virtuální počítače.

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Posouzení serveru přiřadí hodnocení spolehlivosti pro posouzení na základě výkonu. Hodnocení je od jedné hvězdičky (nejnižší) až pěti hvězdiček (nejvyšší).

![Hodnocení spolehlivosti](./media/tutorial-assess-gcp/confidence-rating.png)

Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti v posouzení. Hodnocení je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.

> [!NOTE]
> Hodnocení spolehlivosti není přiřazeno, pokud vytvoříte posouzení založené na souboru CSV.


Hodnocení spolehlivosti je následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si další informace](concepts-assessment-calculation.md#confidence-ratings-performance-based) o hodnocení spolehlivosti.

## <a name="next-steps"></a>Další kroky

- Vyhledá závislosti počítačů pomocí [Mapování závislostí](concepts-dependency-visualization.md).
- Nastavte mapování závislostí [na základě agenta](how-to-create-group-machine-dependencies.md) .
