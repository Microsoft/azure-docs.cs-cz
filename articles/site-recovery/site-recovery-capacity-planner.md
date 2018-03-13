---
title: Odhad replikace kapacity v Azure | Microsoft Docs
description: "Použijte tento článek k zjištění přibližné hodnoty kapacity při replikaci pomocí Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: d9c2645be73c4b6e34d194d6b2444a700e3900d2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Plánování kapacity pro ochranu virtuálních počítačů Hyper-V pomocí Site Recovery

Nové vylepšené verze [Azure Site Recovery nasazení Planner pro technologii Hyper-V nasazení Azure](site-recovery-hyper-v-deployment-planner.md) je nyní k dispozici. Nahradí původní nástroj. Nový nástroj pro plánování nasazení.
Tento nástroj nabízí následující pokyny:

* Vyhodnocení podmínky virtuálních počítačů, na základě počtu disky, velikost disku, IOPS, změn a několik vlastností virtuálního počítače
* Šířka pásma sítě musí versus assessment plánovaný bod obnovení
* Požadavky na infrastrukturu Azure
* Požadavky na infrastrukturu na pracovišti
* Počáteční replikace dávkování pokyny
* Odhadované náklady na obnovení po havárii celkový do Azure


Azure Site Recovery Capacity Planner vám pomůže určit požadavky na kapacitu při replikaci virtuálních počítačů technologie Hyper-V s Azure Site Recovery.

Pomocí Site Recovery Capacity Planner analyzovat vaše zdrojové prostředí a úlohy. Pomůže vám odhadnout požadavky na šířku pásma, prostředky serveru, které potřebujete k umístění zdroje a prostředky (například virtuální počítače a úložiště) musíte v cílovém umístění.

Nástroj můžete spustit ve dvou režimech:

* **Rychlé plánování**: poskytuje sítě a serveru projekce, které jsou založené na průměrném počtu virtuálních počítačů, disků, úložiště a míru změn.
* **Podrobné plánování**: poskytuje podrobné informace o každé zatížení na úrovni virtuálního počítače. Analýza kompatibility virtuálních počítačů a získat projekce sítě a serveru.

## <a name="before-you-start"></a>Než začnete

* Shromážděte informace o prostředí, včetně virtuálních počítačů, disků na virtuální počítač, úložišti na disk.
* Určete vaše denní míry změn s ohledem pro replikovaná data. Stažení [nástroj plánování kapacity služby technologie Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) získat míru změn. [Další informace](site-recovery-capacity-planning-for-hyper-v-replication.md) o tomto nástroji. Doporučujeme, abyste tento nástroj spustit přes týden k zachycení průměry.


## <a name="run-the-quick-planner"></a>Spustit Rychlé plánovače
1. Stáhněte si a nainstalujte [lokality obnovení Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Budete muset spustit makra. Když se zobrazí výzva, proveďte výběr povolit úpravy a obsahu.

2. V **vyberte typ planner** pole se seznamem, vyberte **rychlé Planner**.

   ![Začínáme](./media/site-recovery-capacity-planner/getting-started.png)

3. Na **Capacity Planner** listu, zadejte požadované informace. Vyplňte všechna pole v kroužku červeně na následujícím snímku obrazovky:

   a. V **vyberte váš scénář**, zvolte **technologie Hyper-V do Azure** nebo **VMware nebo fyzický do Azure**.

   b. V **průměrný denní data změnit rychlost (%)**, zadejte informace shromáždit pomocí [nástroj plánování kapacity služby technologie Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) nebo [Planner nasazení Site Recovery](./site-recovery-deployment-planner.md).

   c. **Komprese** nastavení se nepoužívá při replikaci virtuálních počítačů Hyper-V do Azure. Pro kompresi použijte o zařízení třetích stran, jako je například Riverbed.

   d. V **uchování ve dnech**, zadejte ve dnech, jak dlouho chcete uchovat repliky.

   e. V **počet hodin, ve které počáteční replikace pro dávku virtuální počítače by se měla Dokončit** a **počet virtuálních počítačů na jednu dávku počáteční replikace**, zadejte nastavení, které se používají k výpočtu požadavky na počáteční replikace. Po nasazení Site Recovery se nahraje celé počáteční datové sady.

   ![Vstupy](./media/site-recovery-capacity-planner/inputs.png)

4. Po zadání hodnot pro zdrojové prostředí, zobrazený výstup zahrnuje:

   * **Pásma potřebná pro rozdílová replikace (v MB za sekundu)**: šířku pásma sítě pro rozdílová replikace se počítá na průměrné denní míry změny dat.
   * **Pásma potřebná pro počáteční replikaci (v MB za sekundu)**: šířku pásma sítě pro počáteční replikaci se počítá na hodnotách počáteční replikace je zadat.
   * **Vyžaduje (v GB) úložiště**: Celkový počet úložiště Azure vyžaduje.
   * **Celkový počet IOPS na standardní úložiště**: číslo se počítá na základě velikosti 8 kb IOPS jednotku na účty celkový standardní úložiště. Pro rychlé Planner číslo je vypočítáváno na všechny disky virtuálního počítače zdrojového a denních dat frekvence změny. Pro podrobné Planner číslo se počítá na základě celkového počtu virtuálních počítačů, které jsou namapované na standardní virtuálních počítačích Azure a data změnit rychlost na těchto virtuálních počítačích.
   * **Počet účtů úložiště Standard storage nezbytných**: Celkový počet účtů úložiště standard storage, které jsou potřeba k ochraně virtuálních počítačů. Standardní účet úložiště může obsahovat až 20 000 IOPS napříč všech virtuálních počítačích v standardní úložiště. Na disku je podporováno maximálně 500 IOPS.
   * **Počet objektů Blob disky potřebné**: počet disků, které jsou vytvořené na úložiště Azure.
   * **Počet prémiové účty požadované**: Celkový počet prémiové účty úložiště potřeba k ochraně virtuálních počítačů. Zdrojového virtuálního počítače s vysokou IOPS (větší než 20 000) musí prémiový účet úložiště. Prémiový účet úložiště může obsahovat až 80 000 IOPS.
   * **Celkový počet IOPS na Storage úrovně Premium**: číslo se počítá na základě velikosti jednotky IOPS 256 kB na celkový prémiové účty úložiště. Pro rychlé Planner číslo je vypočítáváno na všechny disky virtuálního počítače zdrojového a denních dat frekvence změny. Pro podrobné Planner číslo se počítá na základě celkového počtu virtuálních počítačů, které jsou namapované na virtuálních počítačích Azure premium (series a GS DS) a data změnit rychlost na těchto virtuálních počítačích.
   * **Počet serverů konfigurace vyžaduje**: ukazuje, kolik serverů konfigurace jsou požadovány pro nasazení.
   * **Počet dalších serverů proces vyžaduje**: zobrazuje, zda jsou povinné, kromě procesový server, který běží na konfiguračním serveru ve výchozím nastavení proces další servery.
   * **100 % dodatečné úložiště pro zdroj**: zobrazuje, zda je vyžadováno dodatečné úložiště ve zdrojovém umístění.

      ![Výstup](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Spustit podrobnou Planner

1. Stáhněte si a nainstalujte [lokality obnovení Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Budete muset spustit makra. Když se zobrazí výzva, proveďte výběr povolit úpravy a obsahu.

2. V **vyberte typ planner**, vyberte **podrobné Planner** z pole se seznamem.

   ![Příručka Začínáme](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na **zatížení kvalifikace** listu, zadejte požadované informace. Je nutné vyplnit všechna pole označené.

   a. V **jader procesoru**, zadejte celkový počet jader na zdrojovém serveru.

   b. V **přidělení paměti (v MB)**, zadejte velikost paměti RAM zdrojového serveru.

   c. V **počet síťových adaptérů**, zadejte počet síťových adaptérů na zdrojovém serveru.

   d. V **celkové úložiště (v GB)**, zadejte je celková velikost úložiště virtuálního počítače. Například pokud na zdrojovém serveru má tři disky s 500 GB, celkovou velikost úložiště je 1 500 GB.

   e. V **počet disků připojených**, zadejte celkový počet disků ze zdrojového serveru.

   f. V **disku využití kapacity (%)**, zadejte průměrné využití.

   g. V **frekvence (%) denní data změny**, zadejte denních dat změnit počet zdrojového serveru.

   h. V **velikost virtuálního počítače Azure mapování**, zadejte velikost virtuálního počítače Azure, který chcete propojit. Pokud nechcete provést ručně, vyberte **výpočetní virtuální počítače IaaS**. Pokud vstupní ruční nastavení a potom vyberte **výpočetní virtuální počítače IaaS**, může být přepsána ručního nastavení. Proces výpočetní automaticky určí nejlepší shodu na velikost virtuálního počítače Azure.

   ![Kvalifikace listu pracovního vytížení](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Pokud vyberete **výpočetní virtuální počítače IaaS**, zde je co umožňuje:

   * Ověří povinné vstupy.
   * Vypočítá IOPS a doporučuje nejlepší shodu velikost virtuálního počítače Azure pro každý virtuální počítač, který by byl způsobilý pro replikaci do Azure. Pokud odpovídající velikost, který nebyl nalezen virtuální počítač Azure, zobrazí chybu. Například pokud je číslo disků připojených 65, zobrazí chybu, protože nejvyšší velikost pro virtuální počítač Azure je 64.
   * Navrhuje účet úložiště, který lze použít pro virtuální počítač Azure.
   * Vypočítá celkový počet účty úložiště standard a premium úložiště účtů nezbytných pro pracovní vytížení. Přejděte dolů a zobrazit typ úložiště Azure a účet úložiště, který lze použít pro zdrojový server.
   * Dokončí a seřadí zbytek v tabulce na základě přiřazené pro virtuální počítač a počet disků připojených typu požadované úložiště (standard nebo premium). U všech virtuálních počítačů, které splňují požadavky pro Azure, sloupci **je virtuální počítač kvalifikovaný?** ukazuje **Ano**. Pokud virtuální počítač nelze zálohovat do Azure, zobrazí se chyba.

Sloupce AA AE jsou výstupem a zadejte informace pro každý virtuální počítač.

![Výstupní sloupce AA AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Příklad:
Jako příklad u šesti virtuálních počítačů s hodnoty zobrazené v tabulce nástroje a nejlepší shodu virtuální počítač Azure a požadavky na úložiště Azure.

![Přiřazení kvalifikace pracovního vytížení](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* V příklad výstupu pamatujte na následující:

  * První sloupec je sloupec ověření pro virtuální počítače, disků a změn.
  * Pro pět virtuální počítače jsou potřeba dva účty úložiště standard storage a jeden prémiový účet úložiště.
  * VM3 není splňují podmínky pro ochranu, protože jeden nebo více disků se více než 1 TB.
  * VM1 a virtuálního počítače 2 můžete použít první účet standardního úložiště
  * VM4 můžete použít druhý účet standardního úložiště.
  * VM5 a VM6 potřebovat prémiový účet úložiště, a jak můžete použít jeden účet.

    > [!NOTE]
    > IOPS na úložiště standard a premium jsou vypočítávány na úrovni virtuálního počítače a nikoli na úrovni disku. Standardní virtuální počítač dokáže zpracovat až 500 IOPS na disku. Pokud jsou větší než 500 IOPS pro disk, je třeba úložiště premium. Pokud jsou IOPS pro disk více než 500, ale jsou IOPS pro celkový disky virtuálních počítačů v rámci omezení podpory standardní virtuální počítač Azure, vybere Plánovač standardní virtuální počítač a ne řady DS nebo GS. (Virtuální počítač Azure omezení jsou velikost virtuálního počítače, počet disků, počet adaptérů, procesoru a paměti). Budete muset ručně aktualizovat buňky Azure velikost mapování příslušné řady DS nebo GS virtuálních počítačů.


Po zadání všech informací, vyberte **odesílání dat do nástroje planner** otevřete Capacity Planner. Úlohy jsou vyznačené na zobrazit, zda jsou způsobilé pro ochranu.

### <a name="submit-data-in-capacity-planner"></a>Odesílání dat v Capacity Planner
1. Když otevřete **Capacity Planner** listu, naplní jej na základě nastavení, které jste zadali. Slovo "Zatížení" se zobrazí v **Infra vstupy zdroj** buňky do ukazují, že vstup je **zatížení kvalifikace** listu.

2. Pokud chcete změnit, budete muset upravit **zatížení kvalifikace** listu. Potom vyberte **odesílání dat do nástroje planner** znovu.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Další postup
[Informace o spouštění](site-recovery-capacity-planning-for-hyper-v-replication.md) nástroj plánování kapacity.
