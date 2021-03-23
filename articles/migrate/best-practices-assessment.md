---
title: Osvědčené postupy hodnocení v nástroji Azure Migrate Discovery and Assessment Tool
description: Tipy pro vytváření hodnocení pomocí nástroje pro zjišťování a vyhodnocení Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 1bf844dafe450e90213db2e447bb5392064eb245
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786766"
---
# <a name="best-practices-for-creating-assessments"></a>Osvědčené postupy pro vytváření hodnocení

[Azure Migrate](./migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento článek shrnuje osvědčené postupy při vytváření posouzení pomocí nástroje Azure Migrate Discovery and Assessment Tool.

Vyhodnocení vytvořená pomocí Azure Migrate: Nástroj pro zjišťování a hodnocení jsou snímkem dat v určitém časovém okamžiku. Existují tři typy hodnocení, které můžete vytvořit pomocí Azure Migrate: zjišťování a posouzení:

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní servery v prostředí [VMware](how-to-set-up-appliance-vmware.md) a prostředí [Hyper-V](how-to-set-up-appliance-hyper-v.md) a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. [Další informace](concepts-assessment-calculation.md)
**Azure SQL** | Vyhodnotí migraci místních SQL serverů z prostředí VMware do Azure SQL Database nebo spravované instance Azure SQL. [Další informace](concepts-azure-sql-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md) pro migraci do řešení Azure VMware (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Kritéria stanovení velikosti
Možnosti pro kritéria změny velikosti v Azure Migrate posouzení:

**Kritéria stanovení velikosti** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která poskytují doporučení na základě shromážděných údajů o výkonu | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z dat o využití procesoru a paměti.<br/><br/> Doporučení typu disků (disky HDD nebo SSD úrovně Standard nebo spravované disky úrovně Premium) vychází z IOPS a propustnosti místních disků.<br/><br/>**Posouzení Azure SQL**: konfigurace Azure SQL je založená na datech výkonu instancí SQL a databází, což zahrnuje: využití CPU, využití paměti, IOPS (data a soubory protokolů), propustnost a latence v/v operacích.<br/><br/>**Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z dat o využití procesoru a paměti.
**Jako v místním prostředí** | Posouzení, která k poskytování doporučení nepoužívají údaje o výkonu. | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z velikosti místních virtuálních počítačů.<br/><br> Doporučení typu disků vychází z výběru nastavení typu úložiště pro posouzení.<br/><br/> **Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z velikosti místních virtuálních počítačů.

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

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení serverů na portálu Azure Migrate. Nemůžete vytvořit posouzení Azure SQL s kritérii změny velikosti jako v místním prostředí.
- **Vytvořit vyhodnocení na základě výkonu**: po nastavení zjišťování doporučujeme, abyste před spuštěním posouzení na základě výkonu čekali aspoň jeden den:
    - Shromažďování dat o výkonu trvá déle. Čekání alespoň na den zajistí dostatek datových bodů výkonu před spuštěním posouzení.
    - Pokud používáte posouzení na základě výkonu, ujistěte se, že jste profilaci prostředí pro dobu hodnocení. Pokud například vytvoříte posouzení s dobou trvání výkonu nastavenou na jeden týden, budete muset po zahájení zjišťování počkat aspoň týden, aby se všechny datové body shromáždily. Pokud to neuděláte, hodnocení nebude mít hodnocení s pěti hvězdičkami.
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími daty, je nutné jej přepočítat.

Dodržujte tyto osvědčené postupy pro posouzení serverů importovaných do Azure Migrate prostřednictvím. Soubor CSV:

- **Vytváření hodnocení,** která jsou vyhodnocena jako – jsou vyhodnocena ihned po zobrazení serverů na portálu Azure Migrate.
- **Vytvoření vyhodnocení na základě výkonu**: pomáhá získat lepší odhad nákladů, zejména pokud máte nadřízenou kapacitu serveru v místním prostředí. Přesnost posouzení na základě výkonu však závisí na datech výkonu určených pro tyto servery. 
- **Přepočítat vyhodnocení**: vzhledem k tomu, že posouzení jsou snímky v čase, nejsou automaticky aktualizovány pomocí nejnovějších dat. Chcete-li aktualizovat posouzení s nejnovějšími importovanými daty, je nutné je přepočítat.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT parametry změny velikosti pro posouzení služby AVS

Modul úložiště používaný v rozhraní AVS je síti vSAN. Zásady úložiště vSAN definují požadavky na úložiště pro vaše virtuální počítače. Tyto zásady pro vaše virtuální počítače zaručují požadovanou úroveň služeb, protože určují, jak se virtuálním počítačům přiděluje úložiště. Tady jsou dostupné kombinace FTT a RAID: 

**Tolerované chyby (FTT)** | **Konfigurace RAID** | **Minimální požadovaný počet hostitelů** | **Důležité informace o nastavení velikosti**
--- | --- | --- | --- 
1 | RAID-1 (zrcadlení) | 3 | Virtuální počítač o velikosti 100 GB spotřebuje 200 GB.
1 | RAID-5 (kódování pro případ vymazaní) | 4 | Virtuální počítač o velikosti 100 GB spotřebuje 133,33 GB.
2 | RAID-1 (zrcadlení) | 5 | Virtuální počítač o velikosti 100 GB spotřebuje 300 GB.
2 | RAID-6 (kódování pro případ vymazaní) | 6 | Virtuální počítač o velikosti 100 GB spotřebuje 150 GB.
3 | RAID-1 (zrcadlení) | 7 | Virtuální počítač o velikosti 100 GB spotřebuje 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Osvědčené postupy pro hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, hodnocení se od 1 hvězdičky (nejnižší) až 5 hvězdiček (nejvyšší) přiřadí k posouzení. Efektivní použití hodnocení spolehlivosti:

- Virtuální počítače Azure a vyhodnocení pro službu AVS vyžadují:
    - Data o využití procesoru a paměti pro každý ze serverů
    - Data pro čtení a zápis IOPS/propustnosti pro každý disk připojený k místnímu serveru
    - Data v/v sítě pro každý síťový adaptér připojený k serveru.
     
- Posouzení Azure SQL vyžaduje odhad dat výkonu instancí SQL a databází, které zahrnují:
    - Data o využití procesoru a paměti
    - Data a soubory protokolu pro čtení a zápis IOPS/propustnosti
    - Latence vstupně-výstupních operací

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

Pokud po vytvoření posouzení přidáte nebo odeberete servery ze skupiny, bude hodnocení, které jste vytvořili, označeno jako **nesynchronizované**. Spusťte posouzení znovu (**Přepočítat**), aby odráželo změny skupiny.

### <a name="outdated-assessments"></a>Zastaralá posouzení

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Posouzení virtuálních počítačů Azure a posouzení služby AVS
Pokud existují změny na místních serverech, které jsou ve skupině, která je vyhodnocena, je posouzení označeno jako **zastaralé**. Posouzení může být označeno jako zastaralé v důsledku jedné nebo více změn v níže uvedených vlastnostech:
- Počet jader procesoru
- Přidělená paměť
- Typ spuštění nebo firmware
- Název operačního systému, verze a architektura
- Počet disků
- Počet síťových adaptérů
- Změna velikosti disku (přidělené GB)
- Aktualizují se vlastnosti síťové karty. Příklad: změny adres MAC, přidání IP adresy atd.
    
Spusťte posouzení znovu (**přepočítejte**), aby se změny projevily.
    
#### <a name="azure-sql-assessment"></a>Hodnocení Azure SQL
Pokud dojde ke změnám místních instancí SQL a databází, které jsou ve skupině, která je vyhodnocena, je posouzení označeno jako **zastaralé**. Posouzení může být označeno jako zastaralé z důvodu jednoho nebo více důvodů níže:
- Na serveru se přidala nebo odebrala instance SQL.
- V instanci SQL se přidala nebo odebrala databáze SQL.
- Celková velikost databáze v instanci SQL se změnila o více než 20 %.
- Změna počtu jader procesoru
- Změna v přidělené paměti        
  
    Spusťte posouzení znovu (**přepočítejte**), aby se změny projevily.

### <a name="low-confidence-rating"></a>Hodnocení nízké spolehlivosti

Posouzení nemusí mít všechny datové body z několika důvodů:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte posouzení s dobou výkonu nastavenou na jeden týden, budete muset počkat alespoň jeden týden po spuštění zjišťování, aby se shromáždily všechny datové body. Pokud tuto dobu nemůžete počkat, snižte dobu výkonu a přepočítejte posouzení.
 
- Hodnocení nemůže shromáždit údaje o výkonu některých nebo všech serverů v daném období hodnocení. Pro hodnocení s vysokou mírou jistoty Prosím zajistěte: 
    - Servery jsou napájené po dobu trvání posouzení.
    - Odchozí připojení na portech 443 jsou povolená.
    - Pro servery Hyper-V je dynamická paměť povolená. 
    - Stav připojení agentů v Azure Migrate jsou "připojené" a zkontrolováno poslední prezenční signál
    - Pro vyhodnocení Azure SQL je stav připojení Azure Migrate pro všechny instance SQL "připojené" v okně zjištěná instance SQL.

    Přepočítejte posouzení, aby se projevily poslední změny míry spolehlivosti.

- Pro vyhodnocení virtuálních počítačů a funkce AVS bylo po spuštění zjišťování vytvořeno několik serverů. Pokud například vytváříte vyhodnocení pro historii výkonu za poslední měsíc, ale několik serverů bylo vytvořeno v prostředí pouze před týdnem. V takovém případě nebudou údaje o výkonu pro nové servery k dispozici po celou dobu trvání a hodnocení spolehlivosti bude nízké.

- Pro hodnocení Azure SQL se od spuštění zjišťování vytvořilo málo instancí nebo databází SQL. Pokud například vytváříte posouzení historie výkonu za poslední měsíc, ale v prostředí bylo vytvořeno několik instancí SQL nebo databází pouze před týdnem. V takovém případě nebudou údaje o výkonu pro nové servery k dispozici po celou dobu trvání a hodnocení spolehlivosti bude nízké.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Pokyny k nástroji pro migraci pro posouzení služby AVS

V sestavě připravenosti pro Azure pro hodnocení služby Azure VMware Solution (AVS) uvidíte následující navrhované nástroje: 
- **VMware HCX nebo Enterprise**: pro servery VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)
- **Neznámé**: u serverů importovaných prostřednictvím souboru CSV není výchozí nástroj pro migraci známý. U serverů v prostředí VMware se ale doporučuje použít řešení VMware Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Další kroky

- [Přečtěte si](concepts-assessment-calculation.md) , jak se počítají vyhodnocení.
- [Přečtěte si](how-to-modify-assessment.md) , jak přizpůsobit posouzení.
