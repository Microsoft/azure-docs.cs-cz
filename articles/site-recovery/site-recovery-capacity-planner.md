---
title: Odhadování kapacity replikace v Azure | Dokumentace Microsoftu
description: Pomocí tohoto článku můžete odhadnout kapacitu při replikaci s využitím Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: 3df7bd5ed44bdf514d48e451468329bd11fdf596
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094033"
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Plánování kapacity pro ochranu virtuálních počítačů Hyper-V pomocí Site Recovery

Nová verze vylepšené [Azure Site Recovery Deployment Planner pro Hyper-V do Azure nasazení](site-recovery-hyper-v-deployment-planner.md) je nyní k dispozici. Nahradí původní nástroj. Použijte nové nástroje pro plánování nasazení.
Tento nástroj nabízí následující pokyny:

* Vyhodnocení způsobilosti virtuálního počítače, na základě počtu disků, velikosti disků, vstupně-výstupních operací, četnosti změn a několika charakteristik virtuálních počítačů
* Srovnání šířky pásma sítě posouzení cíle bodu obnovení
* Požadavky na infrastrukturu Azure
* Požadavky na místní infrastrukturu
* Počáteční replikace do dávek pokyny
* Odhadované celkové náklady na zotavení do Azure


Azure Site Recovery Capacity Planner vám pomůže určit vašim požadavkům na kapacitu při replikaci virtuálních počítačů Hyper-V pomocí Azure Site Recovery.

Pomocí Site Recovery Capacity Planneru analyzovat vaše zdrojové prostředí a úlohy. Pomáhá odhadnout požadavky na šířku pásma, prostředcích serveru, které potřebujete pro umístění zdroje a prostředky (například virtuální počítače a úložiště), je třeba v cílovém umístění.

Nástroj můžete spustit ve dvou režimech:

* **Snadné plánování**: poskytuje sítě a serveru projekce, které jsou založené na průměrném počtu virtuálních počítačů, disků, úložiště a frekvenci změn.
* **Podrobné plánování**: poskytuje podrobné informace o každé úloze na úrovni virtuálního počítače. VM compatibility analyzovat a získat projekce sítě a serveru.

## <a name="before-you-start"></a>Než začnete

* Shromážděte informace o vašem prostředí, včetně virtuálních počítačů, discích jednotlivých virtuálních počítačů, úložiště na disk.
* Identifikujte vaší denní frekvenci změn pro replikovaná data. Stáhněte si [kapacity Hyper-V, nástroj pro plánování](https://www.microsoft.com/download/details.aspx?id=39057) získat o míru změn. [Přečtěte si další informace](site-recovery-capacity-planning-for-hyper-v-replication.md) o tomto nástroji. Doporučujeme spustit tento nástroj za týden k zachycení průměry.


## <a name="run-the-quick-planner"></a>Spuštění rychlého plánovače
1. Stáhnout a otevřít [Site Recovery Capacity Planneru](http://aka.ms/asr-capacity-planner-excel). Budete muset spustit makra. Po zobrazení výzvy, dle popisu na povolit úpravy a obsah.

2. V **vyberte typ Plánovač** pole se seznamem, vyberte **rychlé Planneru**.

   ![Začínáme](./media/site-recovery-capacity-planner/getting-started.png)

3. Na **Capacity Planner** listu, zadejte požadované informace. Vyplňte všechna pole v kroužku červeně na následujícím snímku obrazovky:

   a. V **vyberte váš scénář**, zvolte **Hyper-V do Azure** nebo **VMware/fyzických prostředků do Azure**.

   b. V **průměrná denní frekvence změny dat (%)**, zadejte tyto informace shromáždíte pomocí [kapacity Hyper-V, nástroj pro plánování](site-recovery-capacity-planning-for-hyper-v-replication.md) nebo [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   c. **Komprese** nastavení nepoužívají při replikaci virtuálních počítačů Hyper-V do Azure. Komprese použijte zařízení třetích stran, jako je Riverbed.

   d. V **uchování ve dnech**, zadejte počet dní, jak dlouho se má zachovat repliky.

   e. V **počet hodin, v které počáteční replikace pro dávku virtuálních počítačů by se měla Dokončit** a **počet virtuálních počítačů na počáteční replikaci dávky**, zadejte nastavení, která slouží k výpočtu požadavky na počáteční replikaci. Při nasazení Site Recovery, se nahraje celou počáteční datovou sadu.

   ![Vstupy](./media/site-recovery-capacity-planner/inputs.png)

4. Po zadání hodnoty pro zdrojové prostředí, zobrazený výstup zahrnuje:

   * **Šířka pásma vyžadovaná pro rozdílovou replikaci (v MB za sekundu)**: průměrná denní četnost změn dat se vypočítá šířce pásma pro rozdílovou replikaci.
   * **Šířka pásma vyžadovaná pro počáteční replikaci (v MB za sekundu)**: šířka pásma sítě pro počáteční replikaci se počítají hodnoty počáteční replikace je zadat.
   * **Úložiště (v GB) vyžaduje**: celkový úložiště Azure vyžaduje.
   * **Celkový počet vstupně-výstupních operací na úložiště úrovně Standard**: číslo se počítá na základě velikosti 8 kb IOPS jednotky na účty celkový úložiště úrovně standard. Pro rychlé plánovače číslo se vypočítá podle všech disků zdrojového virtuálního počítače a frekvence změny dat o denním. Pro podrobné plánovače číslo se počítá na základě celkového počtu virtuálních počítačů, které jsou mapovány na standardní virtuální počítače Azure a data změnit rychlost na těchto virtuálních počítačích.
   * **Počet účtů úložiště úrovně Standard vyžaduje**: Celkový počet účtů úložiště úrovně standard, které jsou potřeba k ochraně virtuálních počítačů. Účet úložiště úrovně standard může obsahovat až 20 000 IOPS ve všech virtuálních počítačích ve standardním úložišti. Na disku je podporováno maximálně 500 IOPS.
   * **Počet objektů Blob disků požadovaných**: počet disků, které jsou vytvořeny ve službě Azure storage.
   * **Počet účtů premium vyžaduje**: Celkový počet účtů úložiště úrovně premium potřebné k ochraně virtuálních počítačů. Zdrojový virtuální počítač s vysokým vstupně-výstupních operací (větší než 20 000) potřebuje účet premium storage. Účet úložiště úrovně premium může obsahovat až 80 000 vstupně-výstupních operací.
   * **Celkový počet vstupně-výstupních operací na Premium Storage**: číslo se počítá na základě velikosti jednotek 256 kB vstupně-výstupních operací pro účty úložiště úrovně premium celkový počet. Pro rychlé plánovače číslo se vypočítá podle všech disků zdrojového virtuálního počítače a frekvence změny dat o denním. Pro podrobné plánovače číslo se počítá na základě celkového počtu virtuálních počítačů, které jsou mapovány na virtuálních počítačích Azure úrovně premium (řady DS a GS) a data změnit rychlost na těchto virtuálních počítačích.
   * **Počet konfiguračních serverů vyžaduje**: ukazuje, kolik konfigurační servery jsou potřebné pro nasazení.
   * **Počet dalších procesových serverů, které vyžaduje**: ukazují, jestli jsou povinné, kromě procesový server, na kterém běží na konfiguračním serveru ve výchozím nastavení dalších procesových serverů.
   * **100 % dodatečné úložiště na zdrojovém**: ukazuje, jestli se vyžaduje další storage ve zdrojovém umístění.

      ![Výstup](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Spustit podrobnou Planneru

1. Stáhnout a otevřít [Site Recovery Capacity Planneru](http://aka.ms/asr-capacity-planner-excel). Budete muset spustit makra. Po zobrazení výzvy, dle popisu na povolit úpravy a obsah.

2. V **vyberte typ Plánovač**vyberte **podrobné Planneru** ze seznamu.

   ![Příručka Začínáme](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na **úlohy kvalifikace** listu, zadejte požadované informace. Musíte vyplnit všechna pole označené.

   a. V **jader procesoru**, zadejte celkový počet jader na zdrojovém serveru.

   b. V **přidělení paměti (v MB)**, zadejte velikost paměti RAM zdrojovém serveru.

   c. V **počet síťových adaptérů**, zadejte počet síťových adaptérů na zdrojovém serveru.

   d. V **celková velikost úložiště (v GB)**, zadejte celková velikost úložiště virtuálního počítače. Například pokud na zdrojovém serveru má tři disky s 500 GB, celková velikost úložiště velikost je 1 500 GB.

   e. V **počet disků připojených**, zadejte celkový počet disků ze zdrojového serveru.

   f. V **disku využití kapacity (%)**, zadejte průměrné využití.

   g. V **(%) frekvence každodenní změny dat**, zadejte frekvence každodenní změny dat ze zdrojového serveru.

   h. V **velikost mapování virtuálního počítače Azure**, zadejte velikost virtuálního počítače Azure, který chcete propojit. Pokud nechcete provést ručně, vyberte **výpočetní virtuální počítače IaaS**. Pokud vstupní ruční nastavení a pak vyberte **výpočetní virtuální počítače IaaS**, manuální nastavení se můžou přepsat. Proces výpočetní automaticky určí nejlepší shodu velikosti virtuálního počítače Azure.

   ![Úloha kvalifikace list](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Pokud vyberete **výpočetní virtuální počítače IaaS**, zde jsou jeho funkce:

   * Ověří povinné vstupy.
   * Vypočítá vstupně-výstupních operací a navrhne nejlepší shodu velikosti virtuálního počítače Azure pro každý virtuální počítač, který opravňuje k replikaci do Azure. Pokud odpovídající velikost, kterou virtuální počítač Azure nebyl nalezen, zobrazí chybu. Například pokud počet disků připojených 65, zobrazí chybu, protože nejvyšší velikost virtuálního počítače Azure je 64.
   * Navrhuje účet úložiště, který lze použít pro virtuální počítač Azure.
   * Vypočítá celkový počet účtů úložiště úrovně standard a účty úložiště úrovně premium vyžaduje pro pracovní vytížení. Přejděte dolů a zobrazit typ úložiště Azure a účet úložiště, který je možné pro zdrojový server.
   * Dokončení a seřadí zbytek tabulky na základě přiřazené pro virtuální počítač a počet disků připojených typu úložiště (standard nebo premium). Pro všechny virtuální počítače, které splňují požadavky pro Azure, ve sloupci **je virtuální počítač kvalifikovaný?** ukazuje **Ano**. Pokud virtuální počítač nelze zálohovat do Azure, zobrazí chybu.

Sloupce AA AE výstupu a zadejte informace pro každý virtuální počítač.

![Výstupní sloupce AA AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Příklad:
Jako příklad pro šest virtuálních počítačů pomocí hodnoty uvedené v tabulce nástroje a požadavky na úložiště Azure a virtuální počítač Azure je nejlepší shodou.

![Přiřazení úloh kvalifikace](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Ukázkový výstup vezměte na vědomí následující:

  * První sloupec je sloupec ověření pro virtuální počítače, disky a změny.
  * Dva účty úložiště úrovně standard a premium na jeden účet úložiště je třeba pět virtuálních počítačů.
  * VM3 nemá nárok ochranu, protože minimálně jeden disk jsou větší než 1 TB.
  * VM1 a VM2 můžete použít první účet úložiště úrovně standard
  * VM4 můžete použít druhý účet úložiště úrovně standard.
  * VM5 a VM6 potřebovat účet úložiště úrovně premium a jak můžete použít jeden účet.

    > [!NOTE]
    > Vstupně-výstupních operací na úložiště úrovně standard a premium se počítají na úrovni virtuálního počítače a ne na úrovni disku. Standardní virtuální počítač dokáže zpracovat až 500 IOPS na disk. Pokud jsou větší než 500 IOPS disku, musíte služby premium storage. Pokud se více než 500 IOPS disku, ale vstupně-výstupních operací pro celkový počet disků virtuálních počítačů jsou v rámci omezení podpory standardní virtuální počítač Azure, vybere Plánovač standardní virtuální počítač a nikoli řady DS nebo GS. (Omezení virtuálních počítačů Azure se velikost virtuálního počítače, počet disků, počet adaptérů, procesoru a paměti.) Je potřeba ručně aktualizovat mapování Azure velikost buňky s odpovídající řady DS nebo GS virtuálního počítače.


Po zadání všech informací, vyberte **odesílání dat do nástroje plánovače** otevřete Capacity Planner. Chcete-li zobrazit, jestli máte nárok na ochranu jsou zvýrazněny úlohy.

### <a name="submit-data-in-capacity-planner"></a>Odesílání dat v Capacity Planner
1. Když otevřete **Capacity Planner** listu, naplní jej na základě nastavení, které jste zadali. Slovo "Úloha" se zobrazí v **Infra vstupy zdroj** buňky zobrazíte, že je vstup **úlohy kvalifikace** listu.

2. Pokud chcete provést změny, budete muset upravit **úlohy kvalifikace** listu. Potom vyberte **odesílání dat do nástroje plánovače** znovu.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Další postup
[Zjistěte, jak spustit](site-recovery-capacity-planning-for-hyper-v-replication.md) nástroj pro plánování kapacity.
