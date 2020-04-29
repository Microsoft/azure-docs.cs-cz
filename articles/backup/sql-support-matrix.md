---
title: Azure Backupová matice podpory pro zálohování SQL Server na virtuálních počítačích Azure
description: Poskytuje souhrn nastavení podpory a omezení při zálohování SQL Server ve virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409996"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matice podpory pro zálohování SQL Server ve virtuálních počítačích Azure

Pomocí Azure Backup můžete zálohovat databáze SQL Server ve virtuálních počítačích Azure hostovaných na cloudové platformě Microsoft Azure. Tento článek shrnuje obecná nastavení podpory a omezení pro scénáře a nasazení služby SQL Server Backup na virtuálních počítačích Azure.

## <a name="scenario-support"></a>Podpora scénářů

**Podpora** | **Zobrazí**
--- | ---
**Podporovaná nasazení** | Virtuální počítače Azure Marketplace a virtuální počítače mimo Marketplace (SQL Server ručně nainstalované) jsou podporované.
**Podporované zeměpisných oblastech** | Austrálie – jihovýchod (pomocného mechanismu), východní Austrálie (AE), Austrálie – střed (AC), Austrálie – střed 2 (AC) <br> Brazílie – jih (BRS)<br> Kanada – střed (CNC), Kanada – východ (CE)<br> Jižní Východní Asie (moře), Východní Asie (EA) <br> Východní USA (EUS), Východní USA 2 (EUS2), Středozápadní USA (WCUS), Západní USA (WUS); Západní USA 2 (WUS 2) Střed USA – sever (NCUS) Střed USA (kapacitní jednotky) Střed USA – jih (SCUS) <br> Indie – střed (INC), Indie – jih (in), Indie – západ <br> Japonsko – východ (JPE), Japonsko – západ (JPW) <br> Korea – střed (KRC), Korea – jih (KRS) <br> Severní Evropa (NE), Západní Evropa <br> Velká Británie – jih (UKS), Velká Británie – západ (UKW) <br> US Gov – Arizona, US Gov – Virginie, US Gov – Texas, US DoD – střed US DoD – východ <br> Německo – sever Německo – středozápad <br> Švýcarsko – sever Švýcarsko – západ <br> Francie – střed <br> Čína – východ Čína – východ 2 Čína – sever Čína – sever 2
**Podporované operační systémy** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux není aktuálně podporován.
**Podporované verze SQL Server** | SQL Server 2019 SQL Server 2017 na [stránce prohledat životní cyklus produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 a SPS, jak je podrobně popsán na [stránce hledání životního cyklu produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, web, Developer, Express.
**Podporované verze rozhraní .NET** | Na virtuálním počítači je nainstalovaný .NET Framework 4.5.2 nebo novější.

## <a name="feature-consideration-and-limitations"></a>Aspekty a omezení funkcí

* Zálohování SQL Server můžete nakonfigurovat v Azure Portal nebo **PowerShellu**. Rozhraní příkazového řádku nepodporujeme.
* Řešení je podporované v obou druzích [nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – Azure Resource Manager virtuálních počítačů a klasických virtuálních počítačů.
* Virtuální počítač se spuštěným SQL Server vyžaduje připojení k Internetu pro přístup k veřejným IP adresám Azure.
* SQL Server **instance clusteru s podporou převzetí služeb při selhání (FCI)** se nepodporuje.
* Operace zálohování a obnovení pro databáze zrcadlení a snímky databáze nejsou podporovány.
* Použití více než jednoho řešení zálohování k zálohování samostatné instance SQL Server nebo skupiny dostupnosti Always On SQL může způsobit selhání zálohování; upustí od tohoto postupu.
* Zálohování dvou uzlů skupiny dostupnosti jednotlivě se stejnými nebo různými řešeními může také vést k chybě zálohování.
* Pro databáze **jen pro čtení** podporuje Azure Backup jenom úplné typy úplné zálohy a jenom pro kopírování.
* Databáze s velkým počtem souborů není možné chránit. Maximální podporovaný počet souborů je **~ 1000**.  
* V trezoru můžete zálohovat až **~ 2000** SQL Server databází. Pro případ, že máte větší počet databází, můžete vytvořit více trezorů.
* Zálohu můžete nakonfigurovat až na **50** databází v jednom přechodu; Toto omezení pomáhá optimalizovat zatížení zálohování.
* Podporujeme databáze o velikosti až **2 TB** . velikost, která je větší, než může dojít k problémům s výkonem.
* Aby bylo vhodné, aby bylo možné chránit mnoho databází na jeden server, zvažte faktory, jako je šířka pásma, velikost virtuálního počítače, četnost zálohování, velikost databáze a tak dále. [Stáhněte](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) si Plánovač prostředků, abyste mohli vypočítat přibližný počet databází, které můžete mít na serveru na základě prostředků virtuálních počítačů a zásad zálohování.
* Po nakonfigurování skupin dostupnosti jsou zálohy z různých uzlů odebírány na základě několika faktorů. Chování zálohování skupiny dostupnosti je shrnuto níže.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Chování při zálohování pomocí skupin dostupnosti Always On

Doporučujeme, aby záloha byla nakonfigurovaná jenom v jednom uzlu skupiny dostupnosti (AG). Vždy nakonfigurujte zálohu ve stejné oblasti jako primární uzel. Jinými slovy, vždy potřebujete, aby byl primární uzel přítomen v oblasti, kde provádíte konfiguraci zálohování. Pokud jsou všechny uzly AG ve stejné oblasti, ve které je nakonfigurované zálohování, nezáleží na tom.

#### <a name="for-cross-region-ag"></a>Pro křížovou oblast AG

* Bez ohledu na předvolbu zálohování se zálohy spustí jenom z uzlů, které jsou ve stejné oblasti, ve které je nakonfigurované zálohování. Je to proto, že zálohování mezi oblastmi není podporováno. Pokud máte jenom dva uzly a sekundární uzel je v jiné oblasti, zálohy se budou dál spouštět z primárního uzlu (Pokud vaše preference zálohování není sekundární jenom).
* Pokud uzel převezme služby při selhání do jiné oblasti než z toho, kde je záloha nakonfigurovaná, zálohování se na uzlech v oblasti převzetí služeb při selhání nezdaří.

V závislosti na předvolbách zálohování a typech zálohování (úplné/rozdílové/protokolované/kopie jsou úplné) se zálohují z konkrétního uzlu (primární/sekundární).

#### <a name="backup-preference-primary"></a>Předvolby zálohování: primární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Primární
Pouze kopírování je úplné |  Primární

#### <a name="backup-preference-secondary-only"></a>Předvolby zálohování: jenom sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

#### <a name="backup-preference-secondary"></a>Předvolby zálohování: sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

#### <a name="no-backup-preference"></a>Žádná předvolba zálohování

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Pouze kopírování je úplné |  Sekundární

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat databázi SQL Server](backup-azure-sql-database.md) , která běží na virtuálním počítači Azure.
