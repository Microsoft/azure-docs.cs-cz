---
title: Vyhodnoťte servery VMware pro migraci do řešení Azure VMware (AVS) pomocí Azure Migrate
description: Přečtěte si, jak vyhodnotit servery v prostředí VMware pro migraci do funkce AVS s Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782117"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Kurz: posouzení serverů VMware pro migraci do služby AVS

Jako součást cesty k migraci do Azure vyhodnocujete vaše místní úlohy a měříte připravenost k cloudu, identifikaci rizik a odhadu nákladů a složitosti.

V tomto článku se dozvíte, jak vyhodnotit zjištěné virtuální počítače a servery VMware pro migraci do řešení Azure VMware (AVS) pomocí Azure Migrate. Služba AVS je spravovaná služba, která umožňuje spustit platformu VMware v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
- Spusťte posouzení na základě metadat serveru a informací o konfiguraci.
- Spusťte posouzení na základě údajů o výkonu.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).



## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle tohoto kurzu a vyhodnoťte servery pro migraci do služby AVS, ujistěte se, že jste zjistili servery, které chcete vyhodnotit:

- Pokud chcete zjistit servery pomocí Azure Migrate zařízení, [postupujte podle tohoto kurzu](tutorial-discover-vmware.md). 
- Pokud chcete zjistit servery pomocí importovaného souboru CSV, [postupujte podle tohoto kurzu](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Rozhodněte, které posouzení se má spustit.


Rozhodněte, jestli chcete spustit posouzení pomocí kritérií velikosti na základě dat konfigurace serveru/metadat, která se shromažďují místně, nebo na dynamických datech výkonu.

**Posouzení** | **Podrobnosti** | **Doporučení**
--- | --- | ---
**Jako v místním prostředí** | Vyhodnocení na základě dat nebo metadat konfigurace serveru.  | Doporučená velikost uzlů v prostředí AVS je založená na velikosti místního virtuálního počítače nebo serveru spolu s nastaveními, která zadáte při posuzování pro typ uzlu, typ úložiště a nastavení neúspěšného přihlášení.
**Na základě výkonu** | Vyhodnoťte na základě shromážděných dynamických údajů o výkonu. | Doporučená velikost uzlů v rámci služby AVS je založená na datech využití procesoru a paměti spolu s nastaveními, která zadáte při posuzování typu uzlu, typu úložiště a nastavení neúspěšného přihlášení.

> [!NOTE]
> Posouzení řešení Azure VMware (AVS) se dá vytvořit jenom pro virtuální počítače a servery VMware.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1.  Na stránce **přehled** > **Windows, Linux a SQL Server** klikněte na možnost **zhodnotit a migrovat servery**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Stránka s přehledem pro Azure Migrate":::

1. V **Azure Migrate: zjišťování a hodnocení** klikněte na **vyhodnotit**.

   ![Umístění tlačítka pro vyhodnocení](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. V   >  případě vyhodnocení **typu vyhodnocení** serverů vyberte **Azure VMware Solution (AVS)**.

1. Ve **zdroji zjišťování**:

    - Pokud jste zjistili servery, které používají zařízení, vyberte **servery zjištěné z Azure Migrate zařízení**.
    - Pokud jste servery zjistili pomocí importovaného souboru CSV, vyberte **importované servery**. 
    
1. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Stránka pro výběr nastavení posouzení":::
 

1. Ve vlastnostech **posouzení** vlastnosti  >  **cíle**:

    - V části **cílové umístění** zadejte oblast Azure, do které chcete migrovat.
       - Doporučení pro velikost a náklady jsou založena na umístění, které zadáte.
   - **Typ úložiště** je nastaven na **síti vSAN**. Toto je výchozí typ úložiště pro privátní cloud služby AVS.
   - **Rezervované instance** se aktuálně pro uzly služby AVS nepodporují.
1. Ve **velikosti virtuálního počítače**:
    - **Typ uzlu** je nastaven na **AV36**. Azure Migrate doporučuje uzel uzlů potřebných k migraci serverů na funkci AVS.
    - V **Nastavení FTT (úroveň RAID**) vyberte možnost Netolerovat a kombinaci RAID.  Vybraná možnost FTT v kombinaci s požadavkem na disk s místním serverem Určuje celkové úložiště síti vSAN vyžadované v rámci funkce AVS.
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
    - V **nabídce** se zobrazí [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste zaregistrovali v nástroji. Posouzení odhaduje náklady na tuto nabídku.
    - V části **Měna** vyberte fakturační měnu vašeho účtu.
    - V části **sleva (%)** přidejte do nabídky Azure všechny slevy specifické pro předplatné, které obdržíte. Výchozí nastavení je 0 %.

1. Pokud provedete změny, klikněte na **Uložit** .

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Vlastnosti posouzení":::

1. V nabídce **vyhodnotit servery** klikněte na **Další**.

1. V části **Vybrat servery pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení. 
 
1. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Přidání serverů do skupiny":::
 
1. Vyberte zařízení a vyberte servery, které chcete přidat do skupiny. Potom klikněte na **Další**.

1. V části **Revize + vytvořit posouzení** zkontrolujte podrobnosti posouzení a kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    > [!NOTE]
    > Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení počkali alespoň den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. V ideálním případě po zahájení zjišťování počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc) pro hodnocení s vysokou mírou jistoty.

## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení služby AVS popisuje:

- Připravenost na službu AVS: to, jestli jsou místní servery vhodné pro migraci do řešení Azure VMware (AVS).
- Počet uzlů pro funkci AVS: odhadovaný počet uzlů AVS potřebných ke spuštění serverů.
- Využití v uzlech služby AVS: předpokládané využití procesoru, paměti a úložiště napříč všemi uzly.
    - Využití zahrnuje přední faktoring v následujících režijních clusterech, jako jsou vCenter Server, NSX Manager (velký), NSX Edge, pokud je nasazený HCX, a to i v případě, že je nasadí zařízení HCX Manager a IX. ~ 44vCPU (11 procesor), 75 GB paměti RAM a 722GB úložiště před komprimací a odstranění duplicitních dat. 
- Odhad měsíčních nákladů: Odhadované měsíční náklady na všechny uzly řešení Azure VMware (AVS), na kterých běží místní servery.

## <a name="view-an-assessment"></a>Zobrazení posouzení

Zobrazení posouzení:

1. V **systémech Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a hodnocení** klikněte na číslo vedle * * řešení Azure VMware * *.

1. Výběrem posouzení v části **Posouzení** ho otevřete. Příklad (odhad a náklady pouze pro příklad): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Souhrn posouzení pro funkci AVS":::

1. Přečtěte si Souhrn posouzení. Můžete také upravit vlastnosti posouzení nebo přepočítat vyhodnocení.
 

### <a name="review-readiness"></a>Kontrola připravenosti

1. Klikněte na **připravenost na Azure**.
2. V části **připravenost k Azure** si přečtěte stav připravenosti.

    - **Připraveno k funkci AVS**: Server se dá migrovat tak, jak je, do Azure AVS, aniž by došlo k žádným změnám. Server se spustí v programu AVS s plnou podporou AVS.
    - **Připraveno s podmínkami**: Server může mít problémy s kompatibilitou s aktuální verzí vSphere. Před úplnými funkcemi funkce AVS může být potřeba nainstalovat nástroje VMware nebo jiná nastavení.
    - **Nepřipraveno na službu AVS**: virtuální počítač se nespustí v prostředí AVS. Pokud má například místní server VMware k němu připojen externí zařízení (například disk CD-ROM) a používáte VMware VMotion, operace VMotion se nezdařila.
 - **Připravenost je neznámá**: Azure Migrate nešlo určit připravenost serveru kvůli nedostatečným metadatům shromážděným z místního prostředí.

3. Projděte si navrhovaný nástroj.

    - VMware HCX nebo Enterprise: pro servery VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). Víc se uč.
    - Neznámé: u serverů importovaných prostřednictvím souboru CSV není výchozí nástroj pro migraci známý. I když pro servery VMware je navrženo použití řešení VMware Hybrid Cloud Extension (HCX).
4. Klikněte na stav připravenosti na AVS. Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem a zobrazit podrobnosti o serveru, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů

Souhrn posouzení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění serverů v Azure. 

1. Zkontrolujte měsíční celkové náklady. Náklady se agregují pro všechny servery v posuzované skupině.

    - Odhadované náklady vycházejí z počtu uzlů AVS, které jsou potřeba k tomu, aby se všechny servery celkově vyžádaly.
    - Jelikož jsou ceny za funkci AVS na jeden uzel, celkové náklady nebudou mít náklady na výpočetní výkon a distribuci nákladů na úložiště.
    - Odhad nákladů slouží ke spuštění místních serverů v prostředí AVS. Posouzení pro funkci AVS nebere v úvahu náklady na PaaS nebo SaaS.

2. Kontrola měsíčních odhadů úložiště. Zobrazení ukazuje agregované náklady na úložiště pro skupinu pohodnocenou a rozdělené přes různé typy disků úložiště. 
3. Můžete přejít k podrobnostem a zobrazit podrobnosti o cenách pro konkrétní servery.

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Posouzení serveru přiřadí hodnocení spolehlivosti pro posouzení na základě výkonu. Hodnocení je od jedné hvězdičky (nejnižší) až pěti hvězdiček (nejvyšší).

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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

- Vyhledá závislosti serveru pomocí [Mapování závislostí](concepts-dependency-visualization.md).
- Nastavte mapování závislostí bez [agentů](how-to-create-group-machine-dependencies-agentless.md) nebo [na základě agenta](how-to-create-group-machine-dependencies.md) .
