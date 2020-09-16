---
title: Posouzení virtuálních počítačů VMware pro migraci do řešení Azure VMware (AVS) pomocí Azure Migrate
description: Přečtěte si, jak vyhodnotit virtuální počítače VMware pro migraci do funkce AVS pomocí posouzení serveru Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 29f7f824d96aedd80e490ba84c390be4d9493683
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604236"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Kurz: posouzení virtuálních počítačů VMware pro migraci do služby AVS

Jako součást cesty k migraci do Azure vyhodnocujete vaše místní úlohy a měříte připravenost k cloudu, identifikaci rizik a odhadu nákladů a složitosti.

V tomto článku se dozvíte, jak vyhodnotit zjištěné virtuální počítače VMware pro migraci do řešení Azure VMware (AVS) pomocí nástroje Azure Migrate: Server Assessment Tool. Služba AVS je spravovaná služba, která umožňuje spustit platformu VMware v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
- Spusťte posouzení na základě metadat počítače a informací o konfiguraci.
- Spusťte posouzení na základě údajů o výkonu.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).



## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle tohoto kurzu a vyhodnoťte počítače pro migraci do služby AVS, ujistěte se, že jste zjistili počítače, které chcete vyhodnotit:

- Pokud chcete zjistit počítače pomocí Azure Migrate zařízení, [postupujte podle tohoto kurzu](tutorial-discover-vmware.md). 
- Pokud chcete zjistit počítače pomocí importovaného souboru CSV, [postupujte podle tohoto kurzu](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Rozhodněte, které posouzení se má spustit.


Rozhodněte, jestli chcete spustit posouzení pomocí kritérií pro změnu velikosti na základě dat konfigurace počítače/metadat, která se shromažďují místně, nebo na dynamická data o výkonu.

**Posouzení** | **Podrobnosti** | **Doporučení**
--- | --- | ---
**V místním prostředí** | Vyhodnoťte na základě dat nebo metadat konfigurace počítače.  | Doporučená velikost uzlů v prostředí AVS je založená na velikosti místního virtuálního počítače, spolu s nastavením, které zadáte při posuzování pro typ uzlu, typ úložiště a nastavení neúspěšného přihlášení.
**Na základě výkonu** | Vyhodnoťte na základě shromážděných dynamických údajů o výkonu. | Doporučená velikost uzlů v rámci služby AVS je založená na datech využití procesoru a paměti spolu s nastaveními, která zadáte při posuzování typu uzlu, typu úložiště a nastavení neúspěšného přihlášení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Na stránce **servery** > **serverech se systémem Windows a Linux**klikněte na možnost **zhodnotit a migrovat servery**.

   ![Umístění tlačítka pro posouzení a migraci serverů](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. V **Azure Migrate: vyhodnocování serveru**klikněte na **vyhodnotit**.

3. V **Assess servers**  >  případě vyhodnocení**typu vyhodnocení**serverů vyberte **Azure VMware Solution (AVS) (verze Preview)**.
4. Ve **zdroji zjišťování**:

    - Pokud jste v zařízení zjistili počítače, vyberte **počítače zjištěné z Azure Migrate zařízení**.
    - Pokud jste zjistili počítače pomocí importovaného souboru CSV, vyberte **importovat počítače**. 
    
5. Zadejte název posouzení. 
6. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Stránka pro výběr nastavení posouzení](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. vlastnosti 1N **posouzení vlastností**  >  **cíle**:

    - V části **cílové umístění**zadejte oblast Azure, do které chcete migrovat.
       - Doporučení pro velikost a náklady jsou založena na umístění, které zadáte.
       - V současné době můžete vyhodnotit tři oblasti (Východní USA, Západní USA Západní Evropa).
   - V **typu úložiště**ponechte **síti vSAN**. Toto je výchozí typ úložiště pro privátní cloud služby AVS.
   - **Rezervované instance** se aktuálně pro uzly služby AVS nepodporují.
8. Ve **velikosti virtuálního počítače**:
    - V části **typ uzlu**vyberte typ uzlu na základě úloh spuštěných na místních virtuálních počítačích.
        - Azure Migrate doporučuje uzel uzlů potřebných k migraci virtuálních počítačů na funkci AVS.
        - Výchozí typ uzlu je AV36.
    - **FTT nastavení, úroveň RAID**, vyberte nemožnost Netolerovat a kombinaci RAID.  Vybraná možnost FTT v kombinaci s požadavkem na místní disk virtuálního počítače Určuje celkové úložiště síti vSAN vyžadované v rámci funkce AVS.
    - V části přepočet virtuálních **procesorů**zadejte poměr virtuálních jader přidružených k jednomu fyzickému jádru v uzlu AVS. Přesáhne předplatné větší než 4:1 může způsobit snížení výkonu, ale dá se použít pro úlohy typu webový server.

9. V **uzlu velikost**: 
    - V části **kritéria změny velikosti**vyberte, pokud chcete vyhodnotit vyhodnocení statických metadat nebo dat na základě výkonu. Pokud používáte údaje o výkonu:
        - V části **Historie výkonu**určete dobu trvání dat, na které chcete vyhodnotit základ posouzení.
        - V části **využití percentilu**zadejte hodnotu percentilu, kterou chcete použít pro vzorek výkonu. 
    - V části **faktor komfortu**určete vyrovnávací paměť, kterou chcete použít při posuzování. Tyto účty jsou důležité pro problémy, jako je sezónní využití, historie krátkého výkonu a pravděpodobný nárůst využití v budoucnu. Pokud například použijete faktor komfortu 2:
    
        **Komponenta** | **Efektivní využití** | **Přidat faktor pohodlí (2,0)**
        --- | --- | ---  
        Cores | 2 | 4
        Memory (Paměť) | 8 GB | 16 GB     

10. V **ceně**:
    - V **nabídce**se zobrazí [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste zaregistrovali v nástroji, a zobrazí se odhad serveru. cena za tuto nabídku se vyhodnotí.
    - V části **Měna**vyberte fakturační měnu vašeho účtu.
    - V části **sleva (%)** přidejte do nabídky Azure všechny slevy specifické pro předplatné, které obdržíte. Výchozí nastavení je 0 %.

11. Pokud provedete změny, klikněte na **Uložit** .

    ![Vlastnosti posouzení](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. V nabídce **vyhodnotit servery**klikněte na **Další**.
13. V části **vyhodnocování serverů**  >  **Vyberte počítače k**vyhodnocení, pro vytvoření nové skupiny serverů pro posouzení vyberte **vytvořit novou**a zadejte název skupiny. 
14. Vyberte zařízení a vyberte virtuální počítače, které chcete do skupiny přidat. Potom klikněte na **Další**.
15. V části **Revize + vytvořit posouzení**zkontrolujte podrobnosti posouzení a kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    > [!NOTE]
    > Pro posouzení na základě výkonu doporučujeme, abyste před vytvořením posouzení počkali alespoň den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. V ideálním případě po zahájení zjišťování počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc) pro hodnocení s vysokou mírou jistoty.

## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení služby AVS popisuje:

- Připravenost na službu AVS: to, jestli jsou místní virtuální počítače vhodné pro migraci do řešení Azure VMware (AVS).
- Počet uzlů pro funkci AVS: odhadovaný počet uzlů AVS potřebných ke spuštění virtuálních počítačů.
- Využití v uzlech služby AVS: předpokládané využití procesoru, paměti a úložiště napříč všemi uzly.
- Odhad měsíčních nákladů: Odhadované měsíční náklady na všechny uzly řešení Azure VMware (AVS), na kterých běží místní virtuální počítače.

## <a name="view-an-assessment"></a>Zobrazit posouzení

Zobrazení posouzení:

1. V části **servery**  >  **Azure Migrate: vyhodnocování serveru**klikněte na číslo vedle **posouzení**.
2. V **posouzení**vyberte posouzení, které chcete otevřít. 
3. Přečtěte si Souhrn posouzení. Můžete také upravit vlastnosti posouzení nebo přepočítat vyhodnocení.
 

### <a name="review-readiness"></a>Kontrola připravenosti

1. Klikněte na **připravenost na Azure**.
2. V části **připravenost na Azure**zkontrolujte stav virtuálního počítače.

    - **Připraveno k funkci AVS**: počítač se dá migrovat tak, jak je, do Azure AVS, aniž by došlo k žádným změnám. Počítač se spustí v programu AVS a plná podpora pro funkci AVS.
    - **Připraveno s podmínkami**: počítač může mít problémy s kompatibilitou s aktuální verzí vSphere. Před úplnými funkcemi funkce AVS může být potřeba nainstalovat nástroje VMware nebo jiná nastavení.
    - **Nepřipraveno na službu AVS**: virtuální počítač se nespustí v prostředí AVS. Pokud má například místní virtuální počítač VMware připojen externí zařízení (například disk CD-ROM) a používáte VMware VMotion, operace VMotion se nezdařila.
 - **Připravenost neznámá**: Azure Migrate nedokázala určit připravenost počítače kvůli nedostatečným metadatům shromážděným z místního prostředí.

3. Projděte si navrhovaný nástroj.

    - VMware HCX nebo Enterprise: pro počítače VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). Víc se uč.
    - Neznámý: V případě počítačů importovaných prostřednictvím souboru CSV je výchozí nástroj pro migraci neznámý. Ale u počítačů VMware je navrženo použití řešení VMware Hybrid Cloud Extension (HCX).
4. Klikněte na stav připravenosti na AVS. Můžete si prohlédnout podrobnosti připravenosti na virtuální počítač a přejít k podrobnostem, kde najdete podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů

Souhrn posouzení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure. 

1. Zkontrolujte měsíční celkové náklady. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině.

    - Odhadované náklady vycházejí z počtu uzlů AVS, které jsou potřeba k tomu, aby se všechny virtuální počítače celkově vyžádaly.
    - Jelikož jsou ceny za funkci AVS na jeden uzel, celkové náklady nebudou mít náklady na výpočetní výkon a distribuci nákladů na úložiště.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů v rámci služby AVS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Kontrola měsíčních odhadů úložiště. Zobrazení ukazuje agregované náklady na úložiště pro skupinu pohodnocenou a rozdělené přes různé typy disků úložiště. 
3. Můžete přejít k podrobnostem a zobrazit podrobnosti o cenách pro konkrétní virtuální počítače.

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

- Vyhledá závislosti počítačů pomocí [Mapování závislostí](concepts-dependency-visualization.md).
- Nastavte mapování závislostí bez [agentů](how-to-create-group-machine-dependencies-agentless.md) nebo [na základě agenta](how-to-create-group-machine-dependencies.md) .
