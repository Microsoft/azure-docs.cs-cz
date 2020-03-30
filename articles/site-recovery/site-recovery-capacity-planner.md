---
title: Plánování kapacity pro zotavení po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Tento článek slouží k odhadu kapacity při nastavování zotavení po havárii se službou Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936045"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Plánování kapacity pro zotavení po havárii virtuálního virtuálního aplikace Hyper-V 

[Plánovač nasazení obnovení webu Azure] (site-recovery-hyper-v-deployment-planner.md) pro nasazení Hyper-V do Azure poskytuje následující:

* Posouzení způsobilosti virtuálního počítače na základě počtu disků, velikosti disku, VOPS, konve a několika charakteristik virtuálních počítačů
* Srovnání šířky pásma sítě a posouzení cíle bodu obnovení
* Požadavky na infrastrukturu Azure
* Požadavky na místní infrastrukturu
* Pokyny k rozdělení počáteční replikace do dávek
* Odhadované celkové náklady na zotavení po havárii do Azure


Plánovač kapacity webu Azure vám pomůže určit požadavky na kapacitu při replikaci virtuálních počítačů Hyper-V s Azure Site Recovery.

Pomocí plánovače kapacity obnovení webu můžete analyzovat zdrojové prostředí a úlohy. Pomáhá odhadnout potřeby šířky pásma, serverové prostředky, které potřebujete pro zdrojové umístění, a prostředky (například virtuální chod y a úložiště), které potřebujete v cílovém umístění.

Nástroj můžete spustit ve dvou režimech:

* **Rychlé plánování**: Poskytuje síťové a serverové projekce založené na průměrném počtu virtuálních počítačů, disků, úložiště a rychlosti změny.
* **Podrobné plánování**: Poskytuje podrobnosti o každé pracovní vytížení na úrovni virtuálních mích. Analyzujte kompatibilitu virtuálních aplikací a získejte projekce sítě a serveru.

## <a name="before-you-start"></a>Než začnete

* Shromážděte informace o vašem prostředí, včetně virtuálních počítačů, disků na virtuální počítač, úložiště na disk.
* Identifikujte rychlost denních změn (změn) pro replikovaná data. Stáhněte si [nástroj pro plánování kapacity Hyper-V,](https://www.microsoft.com/download/details.aspx?id=39057) abyste získali rychlost změny. [Přečtěte si další informace](site-recovery-capacity-planning-for-hyper-v-replication.md) o tomto nástroji. Doporučujeme spustit tento nástroj přes týden zachytit průměry.


## <a name="run-the-quick-planner"></a>Spuštění rychlého plánovače
1. Stáhněte a otevřete [plánovač kapacity obnovení webu](https://aka.ms/asr-capacity-planner-excel). Je třeba spustit makra. Po zobrazení výzvy proveďte výběry, které umožní úpravy a obsah.

2. V seznamu **Vybrat typ plánovače** vyberte **Rychlý plánovač**.

   ![Začínáme](./media/site-recovery-capacity-planner/getting-started.png)

3. Do listu **Plánovač kapacit** zadejte požadované informace. Vyplňte všechna pole zakroužkovaná červeně na následujícím snímku obrazovky:

   a. V **yberte svůj scénář**zvolte **Hyper-V do Azure** nebo **VMware/Physical to Azure**.

   b. Do **položky Průměrná denní míra změny dat (%)** zadejte informace, které shromažďujete, pomocí [nástroje plánování kapacity Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) nebo [plánovače nasazení obnovení webu](./site-recovery-deployment-planner.md).

   c. Nastavení **Komprese** se nepoužívá při replikaci virtuálních počítačů Hyper-V do Azure. Pro kompresi použijte zařízení jiného výrobce, například Riverbed.

   d. V **uchovávání ve dnech**zadejte ve dnech, jak dlouho chcete zachovat repliky.

   e. V **části Počet hodin, ve kterých by měla být dokončena počáteční replikace pro dávku virtuálních počítačů,** a **Počet virtuálních počítačů na počáteční dávku replikace**zadejte nastavení, která se používají k výpočtu počátečních požadavků na replikaci. Při nasazení site recovery se nahraje celá počáteční sada dat.

   ![Vstupy](./media/site-recovery-capacity-planner/inputs.png)

4. Po zadání hodnot pro zdrojové prostředí bude zobrazený výstup zahrnut:

   * **Šířka pásma požadovaná pro rozdílovou replikaci (v megabitech/s):** Šířka pásma sítě pro rozdílovou replikaci se vypočítá na základě průměrné denní rychlosti změny dat.
   * **Šířka pásma požadovaná pro počáteční replikaci (v megabitech/s):** Šířka pásma sítě pro počáteční replikaci se vypočítá na počátečních zadávaných hodnotách replikace.
   * **Úložiště povinné (v GBs):** Celkový počet úložiště Azure požadované.
   * **Celkový počet vops na standardní úložiště**: Číslo se vypočítá na základě velikosti jednotky 8K IOPS na celkových účtech standardního úložiště. Pro rychlý plánovač se číslo vypočítá na základě všech zdrojových disků virtuálních počítačů a denní rychlosti změny dat. Pro podrobný plánovač se číslo vypočítá na základě celkového počtu virtuálních počítačů, které jsou namapovány na standardní virtuální počítače Azure a rychlost změny dat na těchto virtuálních počítačích.
   * **Počet požadovaných účtů standardního úložiště**: Celkový počet účtů standardního úložiště potřebných k ochraně virtuálních počítačů. Účet standardního úložiště může pojmout až 20 000 viopů ve všech virtuálních virtuálních discích ve standardním úložišti. Na jeden disk je podporováno maximálně 500 vipos.
   * **Počet disků objektů Blob, které jsou povinné**: Počet disků vytvořených v úložišti Azure.
   * **Počet prémiových účtů :** Celkový počet účtů úložiště premium potřebných k ochraně virtuálních počítačů. Zdrojový virtuální virtuální účet s vysokým iOPS (větší než 20 000) potřebuje účet úložiště premium. Účet prémiového úložiště pojme až 80 000 VOPS.
   * **Celkový počet vops na úložiště Premium**: Číslo se vypočítá na základě velikosti jednotky 256 K IOPS na celkových účtech úložiště premium. Pro rychlý plánovač se číslo vypočítá na základě všech zdrojových disků virtuálních počítačů a denní rychlosti změny dat. Pro podrobný plánovač se číslo vypočítá na základě celkového počtu virtuálních počítače, které jsou namapovány na prémiové virtuální počítače Azure (řady DS a GS) a rychlost změny dat na těchto virtuálních počítačích.
   * **Počet požadovaných konfiguračních serverů**: Zobrazuje, kolik konfiguračních serverů je pro nasazení vyžadováno.
   * **Počet dalších procesních serverů :** Zobrazuje, zda jsou požadovány další procesní servery, kromě procesního serveru, který je ve výchozím nastavení spuštěn na konfiguračním serveru.
   * **100 % dodatečné úložiště ve zdroji**: Zobrazuje, zda je ve zdrojovém umístění vyžadováno další úložiště.

      ![Výstup](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Spuštění podrobného plánovače

1. Stáhněte a otevřete [plánovač kapacity obnovení webu](https://aka.ms/asr-capacity-planner-excel). Je třeba spustit makra. Po zobrazení výzvy proveďte výběry, které umožní úpravy a obsah.

2. V **části Vybrat typ plánovače**vyberte ze seznamu **položku Podrobný plánovač.**

   ![Úvodní pokyny](./media/site-recovery-capacity-planner/getting-started-2.png)

3. V listu **Kvalifikace pracovního vytížení** zadejte požadované informace. Musíte vyplnit všechna označená pole.

   a. V **části Procesorová jádra**zadejte celkový počet jader na zdrojovém serveru.

   b. V **přidělení paměti (v MBs)** zadejte velikost paměti RAM zdrojového serveru.

   c. V **poli Počet síťových adaptérů**zadejte počet síťových adaptérů na zdrojovém serveru.

   d. V **celkovém úložišti (v GB)** zadejte celkovou velikost úložiště virtuálních zařízení. Pokud má například zdrojový server tři disky s kapacitou 500 GB, celková velikost úložiště je 1 500 GB.

   e. V **poli Počet připojených disků**zadejte celkový počet disků zdrojového serveru.

   f. V **využití kapacity disku (%)** zadejte průměrné využití.

   g. V **poli Denní rychlost změny dat (%)** zadejte denní rychlost změny dat zdrojového serveru.

   h. V **části Mapování velikosti virtuálního počítače Azure**zadejte velikost virtuálního počítače Azure, kterou chcete mapovat. Pokud to nechcete udělat ručně, vyberte **Compute IaaS VM**. Pokud zadáte ruční nastavení a pak **vyberete Compute IaaS VM ,** ruční nastavení může být přepsáno. Výpočetní proces automaticky identifikuje nejlepší shodu na velikosti virtuálního počítače Azure.

   ![List Kvalifikace pracovního vytížení](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Pokud vyberete **Výpočetní virtuální počítače IaaS**, tady je to, co dělá:

   * Ověří povinné vstupy.
   * Vypočítá viops a navrhne nejlepší velikost virtuálního počítače Azure pro každý virtuální počítač, který je vhodný pro replikaci do Azure. Pokud nelze zjistit odpovídající velikost virtuálního počítače Azure, zobrazí se chyba. Například pokud počet připojených disků je 65, zobrazí se chyba, protože nejvyšší velikost pro virtuální počítač Azure je 64.
   * Navrhne účet úložiště, který se dá použít pro virtuální počítač Azure.
   * Vypočítá celkový počet účtů standardního úložiště a účtů úložiště premium požadovaných pro úlohy. Posuňte se dolů a zobrazte typ úložiště Azure a účet úložiště, který se dá použít pro zdrojový server.
   * Dokončí a seřadí zbytek tabulky na základě požadovaného typu úložiště (standardní nebo premium) přiřazené pro virtuální počítač a počet připojených disků. Pro všechny virtuální počítače, které splňují požadavky na Azure, **Yes**sloupec **Je virtuální počítač kvalifikovaný?** Pokud virtuální počítač nelze zálohovat do Azure, zobrazí se chyba.

Sloupce AA až AE jsou výstupní a poskytují informace pro každý virtuální virtuální ms.

![Výstupní sloupce AA až AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Příklad
Například pro šest virtuálních počítače s hodnotami zobrazenými v tabulce nástroj vypočítá a přiřadí nejlepší shodu virtuálních zařízení Azure a požadavky na úložiště Azure.

![Přiřazení kvalifikace pracovního vytížení](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* V ukázkovém výstupu si všimněte následujícího:

  * První sloupec je ověřovací sloupec pro virtuální počítače, disky a změny.
  * Dva účty standardního úložiště a jeden účet úložiště premium jsou potřeba pro pět virtuálních počítačů.
  * VM3 nemá nárok na ochranu, protože jeden nebo více disků je větší než 1 TB.
  * VM1 a VM2 můžete použít první účet standardníúložiště
  * VM4 můžete použít druhý účet standardníúložiště.
  * VM5 a VM6 potřebují účet úložiště premium a oba můžou používat jeden účet.

    > [!NOTE]
    > IOPS na standardní a prémiové úložiště se počítají na úrovni virtuálních počítačů a ne na úrovni disku. Standardní virtuální počítače zvládne až 500 viops na disk. Pokud jsou iOPS pro disk větší než 500, budete potřebovat úložiště premium. Pokud viops pro disk jsou více než 500, ale VOPS pro celkové disky virtuálních počítačů jsou v rámci omezení standardu podpory virtuálních počítačů Azure, plánovač vybere standardní virtuální počítač a ne řady DS nebo GS. (Omezení virtuálních počítačů Azure jsou velikost virtuálního počítače, počet disků, počet adaptérů, procesor a paměť.) Musíte ručně aktualizovat mapování buňky velikosti Azure s příslušným virtuálním počítačem řady DS nebo GS.


Po zadání všech informací vyberte **Odeslat data do nástroje plánovače** a otevřete Plánovač kapacit. Pracovní vytížení jsou zvýrazněny, aby se zobrazilo, zda jsou způsobilé pro ochranu.

### <a name="submit-data-in-capacity-planner"></a>Odeslat data v plánovači kapacit
1. Když otevřete list **Plánovač kapacity,** je vyplněn na základě zadaného nastavení. Slovo "Pracovní vytížení" se zobrazí ve **zdrojové buňce Infra vstupy,** které ukazuje, že vstup je sešit **Kvalifikace pracovního vytížení.**

2. Pokud chcete provést změny, je třeba upravit **sešit Kvalifikace pracovního vytížení.** Pak znovu vyberte **Odeslat data do nástroje plánovače.**

   ![Plánovač kapacity](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak spustit](site-recovery-capacity-planning-for-hyper-v-replication.md) nástroj pro plánování kapacity.
