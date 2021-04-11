---
title: Plánování kapacity pro zotavení po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Tento článek použijte k odhadu kapacity při nastavování zotavení po havárii pomocí služby Azure Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 0bdc66b46e83a1592d1ea74c1080d6f8080f4635
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580664"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Plánování kapacity pro zotavení po havárii virtuálních počítačů Hyper-V 

[Plánovač nasazení služby Azure Site Recovery] (site-recovery-hyper-v-deployment-planner.md) pro nasazení Hyper-V do Azure nabízí následující:

* Vyhodnocení způsobilosti virtuálního počítače na základě počtu disků, velikosti disku, IOPS, změn a několika vlastností virtuálního počítače
* Srovnání šířky pásma sítě a posouzení cíle bodu obnovení
* Požadavky na infrastrukturu Azure
* Požadavky na místní infrastrukturu
* Pokyny k rozdělení počáteční replikace do dávek
* Odhadované celkové náklady na zotavení po havárii do Azure


Azure Site Recovery Capacity Planner vám pomůže určit požadavky na kapacitu při replikaci virtuálních počítačů Hyper-V pomocí Azure Site Recovery.

K analýze zdrojového prostředí a úloh použijte Site Recovery Capacity Planner. Pomáhá odhadnout potřeby šířky pásma, prostředky serveru, které potřebujete pro zdrojové umístění, a prostředky (například virtuální počítače a úložiště), které potřebujete v cílovém umístění.

Nástroj můžete spustit ve dvou režimech:

* **Rychlé plánování**: poskytuje projektové a serverové projekce založené na průměrném počtu virtuálních počítačů, disků, úložišť a četnosti změn.
* **Podrobné plánování**: poskytuje podrobné informace o jednotlivých úlohách na úrovni virtuálního počítače. Analyzujte kompatibilitu virtuálních počítačů a získejte projekce ze sítě a serveru.

## <a name="before-you-start"></a>Než začnete

* Shromážděte informace o vašem prostředí, včetně virtuálních počítačů, disků na virtuální počítač, úložiště na disk.
* Identifikujte míru denní změny (změn) pro replikovaná data. Stáhněte si [Nástroj pro plánování kapacity technologie Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) , abyste získali rychlost změny. [Přečtěte si další informace](./hyper-v-deployment-planner-overview.md) o tomto nástroji. Pro zachycení průměrů doporučujeme spustit tento nástroj za týden.


## <a name="run-the-quick-planner"></a>Spuštění rychlého plánovače
1. Stáhněte a otevřete [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Je nutné spustit makra. Po zobrazení výzvy proveďte výběry pro povolení úprav a obsahu.

2. V seznamu **Vyberte typ plánovače** vyberte **rychlé plánování**.

   ![Snímek obrazovky s možností vybrat typ plánovače s vybraným rychlým plánovačem](./media/site-recovery-capacity-planner/getting-started.png)

3. V listu **Capacity Planner** zadejte požadované informace. Vyplňte všechna pole v červeném kruhu na následujícím snímku obrazovky:

   a. V **Možnosti vyberte svůj scénář** zvolte možnost **Hyper-V do Azure** nebo **VMware/fyzický do Azure**.

   b. V části **Průměrná denní četnost změn dat (%)** zadejte informace, které shromažďujete, pomocí [Nástroje pro plánování kapacity technologie Hyper-V](./hyper-v-deployment-planner-overview.md) nebo [Site Recovery Plánovač nasazení](./site-recovery-deployment-planner.md).

   c. Nastavení **Komprese** se nepoužívá při replikaci virtuálních počítačů Hyper-V do Azure. Pro kompresi použijte zařízení třetí strany, například Riverbed.

   d. Do doby **uchování ve dnech** zadejte počet dní, po které chcete uchovávat repliky.

   e. V **počtu hodin, během kterých by měla být počáteční replikace pro dávku virtuálních počítačů dokončena** , a **počet virtuálních počítačů na počáteční dávku replikace** zadejte nastavení, která se použijí k výpočtu požadavků na počáteční replikaci. Při nasazení Site Recovery se nahraje celá počáteční datová sada.

   ![Snímek obrazovky Capacity Planner listu zobrazující požadované vstupní informace](./media/site-recovery-capacity-planner/inputs.png)

4. Po zadání hodnot pro zdrojové prostředí obsahuje zobrazený výstup:

   * **Požadovaná šířka pásma pro rozdílovou replikaci (v MB/s)**: šířka pásma sítě pro rozdílovou replikaci se počítá podle průměrné denní frekvence změny dat.
   * **Požadovaná šířka pásma pro počáteční replikaci (v MB/s)**: šířka pásma sítě pro počáteční replikaci se počítá podle hodnot počáteční replikace, které zadáte.
   * **Vyžadováno úložiště (v GB)**: vyžaduje se celkové úložiště Azure.
   * **Celkový počet IOPS ve standardním úložišti**: číslo se vypočítá na základě velikosti jednotky 8K IOPS v celkových standardních účtech úložiště. Pro rychlé plánování se číslo vypočítá na základě všech disků zdrojového virtuálního počítače a rychlosti denních změn dat. V podrobném plánovači se číslo vypočítá na základě celkového počtu virtuálních počítačů, které jsou namapované na standardní virtuální počítače Azure a četnosti změn dat na těchto virtuálních počítačích.
   * **Vyžaduje se počet standardních účtů úložiště**: celkový počet standardních účtů úložiště potřebných k ochraně virtuálních počítačů. Účet úložiště úrovně Standard může obsahovat až 20 000 IOPS napříč všemi virtuálními počítači ve službě Storage úrovně Standard. Na disk se podporuje maximálně 500 IOPS.
   * **Počet požadovaných disků objektů BLOB**: počet disků, které jsou vytvořeny v Azure Storage.
   * **Počet požadovaných účtů Premium**: celkový počet účtů služby Premium Storage potřebných k ochraně virtuálních počítačů. Zdrojový virtuální počítač s vysokým počtem vstupně-výstupních operací (větším než 20 000) vyžaduje účet Premium Storage. Účet Premium Storage může obsahovat až 80 000 IOPS.
   * **Celkový počet IOPS v Premium Storage**: číslo se počítá na základě velikosti jednotky 256 IOPS v celkových účtech Premium Storage. Pro rychlé plánování se číslo vypočítá na základě všech disků zdrojového virtuálního počítače a rychlosti denních změn dat. V podrobném plánovači se číslo vypočítá na základě celkového počtu virtuálních počítačů, které jsou namapované na virtuální počítače Azure úrovně Premium (DS a GS Series), a frekvence změny dat na těchto virtuálních počítačích.
   * **Požadovaný počet konfiguračních serverů**: zobrazuje, kolik konfiguračních serverů je pro nasazení vyžadováno.
   * **Počet požadovaných dalších procesových serverů**: zobrazuje, jestli jsou nutné další procesové servery, kromě procesového serveru, který běží na konfiguračním serveru ve výchozím nastavení.
   * **100% další úložiště na zdroji**: zobrazuje, zda je v umístění zdroje vyžadováno další úložiště.

      ![Snímek obrazovky zobrazeného výstupu na základě poskytnutého vstupu](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Spuštění podrobného plánovače

1. Stáhněte a otevřete [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Je nutné spustit makra. Po zobrazení výzvy proveďte výběry pro povolení úprav a obsahu.

2. V části **Vyberte typ plánovače** vyberte **podrobný Plánovač** ze seznamu.

   ![Snímek obrazovky s možností vybrat typ plánovače s vybraným podrobným plánovačem](./media/site-recovery-capacity-planner/getting-started-2.png)

3. V listu **kvalifikace úlohy** zadejte požadované informace. Musíte vyplnit všechna označená pole.

   a. V **části procesory** zadejte celkový počet jader na zdrojovém serveru.

   b. V části **přidělení paměti (v MB)** zadejte velikost paměti RAM zdrojového serveru.

   c. V poli **počet** síťových adaptérů určete počet síťových adaptérů na zdrojovém serveru.

   d. V části **Celková velikost úložiště (v GB)** zadejte celkovou velikost úložiště virtuálního počítače. Pokud má například zdrojový server tři disky s 500 GB, celková velikost úložiště je 1 500 GB.

   e. V poli **Počet připojených disků** zadejte celkový počet disků zdrojového serveru.

   f. V části **využití kapacity disku (%)** zadejte průměrné využití.

   například V části **denní četnost změn dat (%)** zadejte denní četnost změn dat na zdrojovém serveru.

   h. V části **mapování velikosti virtuálního počítače Azure** zadejte velikost virtuálního počítače Azure, kterou chcete namapovat. Pokud to nechcete provést ručně, vyberte **COMPUTE IaaS virtuální počítače**. Pokud zadáte ruční nastavení a pak vyberete **výpočetní virtuální počítače IaaS**, může být ruční nastavení přepsáno. Výpočetní proces automaticky identifikuje nejlepší shodu velikosti virtuálního počítače Azure.

   ![Snímek obrazovky s listem kvalifikace úlohy, který zobrazuje požadované vstupní informace](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Pokud vyberete **virtuální počítače COMPUTE IaaS**, najdete tady:

   * Ověří povinné vstupy.
   * Vypočítá IOPS a navrhne nejlepší shodu velikosti virtuálního počítače Azure pro každý virtuální počítač, který je způsobilý pro replikaci do Azure. Pokud se pro virtuální počítač Azure odpovídající velikosti nedá zjistit, zobrazí se chyba. Pokud je například počet připojených disků 65, zobrazí se chyba, protože nejvyšší velikost virtuálního počítače Azure je 64.
   * Navrhuje účet úložiště, který se dá použít pro virtuální počítač Azure.
   * Vypočítá celkový počet standardních účtů úložiště a účtů služby Premium Storage potřebných pro úlohy. Posuňte se dolů a zobrazte typ úložiště Azure a účet úložiště, který se dá použít pro zdrojový server.
   * Dokončí a seřadí zbytek tabulky na základě požadovaného typu úložiště (Standard nebo Premium) přiřazeného k virtuálnímu počítači a počtu připojených disků. U všech virtuálních počítačů, které splňují požadavky pro Azure, **je sloupec kvalifikovaný pro virtuální počítače?** zobrazuje **Ano**. Pokud se virtuální počítač nedá zálohovat do Azure, zobrazí se chyba.

Sloupce AA až AE jsou výstupem a poskytují informace pro každý virtuální počítač.

![Snímek obrazovky zobrazující výstupní sloupce AA až AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Příklad
Například pro šest virtuálních počítačů s hodnotami zobrazenými v tabulce nástroj vypočítá a přiřadí nejlepší shodu virtuálních počítačů s Azure a požadavky na úložiště Azure.

![Snímek obrazovky znázorňující přiřazení kvalifikace úlohy](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* V příkladu výstupu si všimněte následujícího:

  * První sloupec je ověřovací sloupec pro virtuální počítače, disky a změny.
  * Pro pět virtuálních počítačů je potřeba mít dva účty úložiště úrovně Standard a jeden účet Premium Storage.
  * VM3 neopravňují k ochraně, protože jeden nebo více disků je více než 1 TB.
  * VM1 a VM2 můžou používat první účet Standard Storage.
  * VM4 může používat druhý standardní účet úložiště.
  * VM5 a VM6 potřebují účet Premium Storage a obě můžou používat jenom jeden účet.

    > [!NOTE]
    > IOPS v úložišti úrovně Standard a Premium se počítají na úrovni virtuálního počítače a ne na úrovni disku. Standardní virtuální počítač může zpracovávat až 500 vstupně-výstupních operací na disk. Pokud je IOPS pro disk větší než 500, budete potřebovat Premium Storage. Pokud je IOPS pro určitý disk větší než 500, ale IOPS za celkový počet disků virtuálního počítače je v rámci podpory Standard omezení virtuálních počítačů Azure, Plánovač zvolí standardní virtuální počítač, nikoli řadu DS nebo GS. (Omezení virtuálních počítačů Azure jsou velikosti virtuálních počítačů, počet disků, počet adaptérů, procesor a paměť.) Je potřeba ručně aktualizovat buňku s velikostí Azure s odpovídajícím virtuálním počítačem DS nebo GS.


Po zadání všech informací vyberte **Odeslat data do nástroje Planner** a otevřete Capacity Planner. Úlohy jsou zvýrazněné a ukazují, jestli mají nárok na ochranu.

### <a name="submit-data-in-capacity-planner"></a>Odesílat data v Capacity Planner
1. Když otevřete list **Capacity Planner** , naplní se na základě vámi zadaných nastavení. Slovo "úloha" se zobrazí ve zdrojové buňce pro **infračervené vstupy** , aby se zobrazilo, že vstupem je list **kvalifikace úlohy** .

2. Chcete-li provést změny, je nutné upravit sešit **kvalifikace úlohy** . Pak znovu vyberte **Odeslat data do nástroje Planner** .

   ![Snímek obrazovky znázorňující změněné vstupy a výsledné výstupy v listu Capacity Planner.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak spustit](./hyper-v-deployment-planner-overview.md) Nástroj pro plánování kapacity.