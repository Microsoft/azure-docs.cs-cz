---
title: Osvědčené postupy hodnocení v Azure Migrate posouzení serveru
description: Tipy pro vytváření hodnocení pomocí Azure Migrate posouzení serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 1a3735180d72496d58cdd22d0aa34c8a6f88a6a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559859"
---
# <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento článek shrnuje osvědčené postupy při vytváření posouzení pomocí nástroje Azure Migrate Server Assessment Tool.

## <a name="about-assessments"></a>O posouzení

Posouzení, které vytvoříte pomocí Azure Migrate posouzení serveru, jsou snímkem dat v určitém časovém okamžiku. Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru:

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení migrace vašich místních serverů do virtuálních počítačů Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. [Další informace](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení migrace místních serverů do [Řešení Azure VMware (AVS)](https://docs.microsoft.com/azure/azure-vmware/introduction). <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md) pro migraci do řešení Azure VMware (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Kritéria změny velikosti
Posouzení serveru poskytuje dvě možnosti pro kritéria změny velikosti:

**Kritéria změny velikosti** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která vytvářejí doporučení na základě shromážděných údajů o výkonu | **Posouzení virtuálního počítače Azure**: doporučení velikosti virtuálního počítače vychází z dat využití procesoru a paměti.<br/><br/> Doporučení pro typ disku (standardní disková jednotka/SSD nebo Premium – spravované disky) vychází z IOPS a propustnosti místních disků.<br/><br/> **Posouzení řešení Azure VMware (AVS)**: doporučení pro služby AVS jsou založená na datech využití procesoru a paměti.
**V místním prostředí** | Posouzení, které nepoužívají údaje o výkonu k vytváření doporučení. | **Posouzení virtuálního počítače Azure**: doporučení velikosti virtuálního počítače je založené na velikosti místního virtuálního počítače.<br/><br> Doporučený typ disku je založený na tom, co jste vybrali v nastavení typ úložiště pro posouzení.<br/><br/> **Posouzení řešení Azure VMware (AVS)**: doporučení pro služby AVS jsou založená na velikosti místního virtuálního počítače.

#### <a name="example"></a>Příklad
Pokud máte například místní virtuální počítač se čtyřmi jádry na 20% využití a paměť 8 GB s 10% využitím, vyhodnocení virtuálního počítače Azure bude následující:

- **Posouzení na základě výkonu**:
    - Identifikuje efektivní jádra a paměť na základě jádra (4 x 0,20 = 0,8) a paměti (8 GB × 0,10 = 0,8).
    - Použije faktor pohodlí určený ve vlastnostech posouzení (řekněme 1,3 x) a získá hodnoty, které se použijí pro určení velikosti. 
    - Doporučuje nejbližší velikost virtuálního počítače v Azure, která může podporovat ~ 1,04 jader (0,8 × 1,3) a ~ 1,04 GB (0,8 x 1,3).

- **Vyhodnocení, jak je (jako místní) posouzení**:
    -  Doporučuje virtuální počítač se čtyřmi jádry. 8 GB paměti.


## <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

Zařízení Azure Migrate průběžně profiluje vaše místní prostředí a odesílá data o metadatech a výkonu do Azure. Dodržujte tyto osvědčené postupy pro posouzení serverů zjištěných pomocí zařízení:

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení počítačů na portálu Azure Migrate.
- **Vytvořit vyhodnocení na základě výkonu**: po nastavení zjišťování doporučujeme, abyste před spuštěním posouzení na základě výkonu čekali aspoň jeden den:
    - Shromažďování dat o výkonu trvá déle. Čekání alespoň na den zajistí dostatek datových bodů výkonu před spuštěním posouzení.
    - Pokud používáte posouzení na základě výkonu, ujistěte se, že jste profilaci prostředí pro dobu hodnocení. Pokud například vytvoříte posouzení s dobou trvání výkonu nastavenou na jeden týden, budete muset po zahájení zjišťování počkat aspoň týden, aby se všechny datové body shromáždily. Pokud to neuděláte, hodnocení nebude mít hodnocení s pěti hvězdičkami.
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími daty, je nutné jej přepočítat.

Dodržujte tyto osvědčené postupy pro posouzení serverů importovaných do Azure Migrate prostřednictvím. Soubor CSV:

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení počítačů na portálu Azure Migrate.
- **Vytvoření vyhodnocení na základě výkonu**: pomáhá získat lepší odhad nákladů, zejména pokud máte nadřízenou kapacitu serveru v místním prostředí. Přesnost posouzení na základě výkonu však závisí na datech výkonu určených pro tyto servery. 
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími importovanými daty, je nutné je přepočítat.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT parametry změny velikosti pro posouzení služby AVS

Modul úložiště používaný v rozhraní AVS je síti vSAN. zásady úložiště síti vSAN definují požadavky na úložiště pro virtuální počítače. Tyto zásady zaručují potřebnou úroveň služby pro vaše virtuální počítače, protože určují, jak je úložiště přidělené k virtuálnímu počítači. Toto jsou dostupné kombinace FTT-RAID: 

**Neúspěšné tolerování (FTT)** | **Konfigurace RAID** | **Minimální požadovaná hostitelé** | **Aspekt velikosti**
--- | --- | --- | --- 
1 | RAID-1 (zrcadlení) | 3 | 100 GB virtuální počítač spotřebuje 200 GB.
1 | RAID-5 (mazání kódu) | 4 | Virtuální počítač 100 GB spotřebuje 133.33 GB.
2 | RAID-1 (zrcadlení) | 5 | Virtuální počítač 100 GB spotřebuje 300 GB.
2 | RAID-6 (mazání kódu) | 6 | 100 GB virtuální počítač spotřebuje 150 GB.
3 | RAID-1 (zrcadlení) | 7 | Virtuální počítač s 100 GB by spotřebuje 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Osvědčené postupy pro hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, hodnocení se od 1 hvězdičky (nejnižší) až 5 hvězdiček (nejvyšší) přiřadí k posouzení. Efektivní použití hodnocení spolehlivosti:
- Azure Migrate posouzení serveru potřebuje data o využití pro procesor nebo paměť virtuálních počítačů.
- Pro každý disk připojený k místnímu virtuálnímu počítači potřebuje data pro čtení a zápis IOPS/propustnost.
- Pro každý síťový adaptér připojený k virtuálnímu počítači potřebuje data v/v síti.

V závislosti na procentu datových bodů dostupných pro vybranou dobu trvání je hodnocení spolehlivosti pro posouzení uvedené shrnuté v následující tabulce.

   **Dostupnost datového bodu** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček


## <a name="common-assessment-issues"></a>Běžné problémy s posouzením

Tady je postup, jak řešit některé běžné problémy s prostředím, které mají vliv na posouzení.

###  <a name="out-of-sync-assessments"></a>Posouzení nesynchronizovaných

Pokud po vytvoření posouzení přidáte nebo odeberete počítače ze skupiny, bude hodnocení, které jste vytvořili, označeno jako **nesynchronizované**. Spusťte posouzení znovu (**Přepočítat**), aby odráželo změny skupiny.

### <a name="outdated-assessments"></a>Zastaralá posouzení

Pokud existují místní změny virtuálních počítačů, které jsou ve skupině, která je vyhodnocena, je posouzení označeno jako **zastaralé**. Posouzení může být označeno jako zastaralé v důsledku jedné nebo více změn v níže uvedených vlastnostech:

- Počet jader procesoru
- Přidělená paměť
- Typ spuštění nebo firmware
- Název operačního systému, verze a architektura
- Počet disků
- Počet síťových adaptérů
- Změna velikosti disku (přidělené GB)
- Aktualizují se vlastnosti síťové karty. Příklad: změny adres MAC, přidání IP adresy atd.

Spusťte posouzení znovu (**přepočítejte**), aby se změny projevily.

### <a name="low-confidence-rating"></a>Hodnocení nízké spolehlivosti

Posouzení nemusí mít všechny datové body z několika důvodů:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Pokud například vytváříte *hodnocení založené na výkonu* s dobou trvání trvání nastavenou na jeden týden, budete muset po zahájení zjišťování všech datových bodů, které se mají shromáždit, počkat aspoň na týden. Kdykoli můžete kliknout na **Přepočítat** a zobrazit nejnovější použitelné hodnocení spolehlivosti. Hodnocení spolehlivosti se dá použít jenom v případě, že vytvoříte hodnocení *založené na výkonu* .

- Během období, pro které se posouzení počítá, se několik virtuálních počítačů vypnulo. Pokud po nějakou dobu byly některé virtuální počítače vypnuté, nástroj Hodnocení serverů nebude schopen za toto období shromáždit data o výkonu.

- Po zahájení zjišťování v nástroji Hodnocení serverů se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takovém případě nebudou k dispozici data o výkonu nových virtuálních počítačů za celou dobu trvání a míra spolehlivosti bude nízká.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Pokyny k nástroji pro migraci pro posouzení služby AVS

V sestavě připravenosti Azure pro posouzení řešení Azure VMware (AVS) můžete vidět následující navrhované nástroje: 
- **VMware HCX nebo Enterprise**: pro počítače VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). [Další informace](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)
- **Neznámé**: u počítačů importovaných prostřednictvím souboru CSV není výchozí nástroj pro migraci známý. Ale u počítačů VMware se doporučuje použít řešení VMWare Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Další kroky

- [Přečtěte si](concepts-assessment-calculation.md) , jak se počítají vyhodnocení.
- [Přečtěte si](how-to-modify-assessment.md) , jak přizpůsobit posouzení.
