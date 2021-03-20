---
title: Azure Backupová matice podpory pro zálohování SQL Server na virtuálních počítačích Azure
description: Poskytuje souhrn nastavení podpory a omezení při zálohování SQL Server ve virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 78436981c515b95ccda763d8ac916738b4364953
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734789"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matice podpory pro zálohování SQL Server ve virtuálních počítačích Azure

Pomocí Azure Backup můžete zálohovat databáze SQL Server ve virtuálních počítačích Azure hostovaných na cloudové platformě Microsoft Azure. Tento článek shrnuje obecná nastavení podpory a omezení pro scénáře a nasazení služby SQL Server Backup na virtuálních počítačích Azure.

## <a name="scenario-support"></a>Podpora scénářů

**Podpora** | **Podrobnosti**
--- | ---
**Podporovaná nasazení** | Virtuální počítače Azure Marketplace a virtuální počítače mimo Marketplace (SQL Server ručně nainstalované) jsou podporované.
**Podporované oblasti** | Austrálie – jihovýchod (pomocného mechanismu), východní Austrálie (AE), Austrálie – střed (AC), Austrálie – střed 2 (AC) <br> Brazílie – jih (BRS)<br> Kanada – střed (CNC), Kanada – východ (CE)<br> Jižní Východní Asie (moře), Východní Asie (EA) <br> Východní USA (EUS), Východní USA 2 (EUS2), Středozápadní USA (WCUS), Západní USA (WUS); Západní USA 2 (WUS 2) Střed USA – sever (NCUS) Střed USA (kapacitní jednotky) Střed USA – jih (SCUS) <br> Indie – střed (INC), Indie – jih (in), Indie – západ <br> Japonsko – východ (JPE), Japonsko – západ (JPW) <br> Korea – střed (KRC), Korea – jih (KRS) <br> Severní Evropa (NE), Západní Evropa <br> Velká Británie – jih (UKS), Velká Británie – západ (UKW) <br> US Gov – Arizona, US Gov – Virginie, US Gov – Texas, US DoD – střed US DoD – východ <br> Německo – sever Německo – středozápad <br> Švýcarsko – sever Švýcarsko – západ <br> Francie – střed <br> Čína – východ Čína – východ 2 Čína – sever Čína – sever 2
**Podporované operační systémy** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux není aktuálně podporován.
**Podporované verze SQL Server** | SQL Server 2019 SQL Server 2017 na [stránce prohledat životní cyklus produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 a SPS, jak je podrobně popsán na [stránce hledání životního cyklu produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, web, Developer, Express.<br><br>Verze Express Local DB se nepodporují.
**Podporované verze rozhraní .NET** | Na virtuálním počítači je nainstalovaný .NET Framework 4.5.2 nebo novější.

## <a name="feature-considerations-and-limitations"></a>Hlediska a omezení funkcí

|Nastavení  |Maximální omezení |
|---------|---------|
|Počet databází, které lze chránit na serveru (a v trezoru)    |   2000      |
|Podporovaná velikost databáze (nad rámec této velikosti může dojít k problémům s výkonem)   |   2 TB      |
|Počet souborů podporovaných v databázi    |   1000      |

>[!NOTE]
> [Stáhněte si podrobný Plánovač prostředků](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) a vypočítejte přibližný počet chráněných databází doporučených pro jednotlivé servery na základě prostředků virtuálních počítačů, šířky pásma a zásad zálohování.

* Zálohování SQL Server můžete nakonfigurovat v Azure Portal nebo **PowerShellu**. Rozhraní příkazového řádku se nepodporuje.
* Řešení je podporované v obou druzích [nasazení](../azure-resource-manager/management/deployment-models.md) – Azure Resource Manager virtuálních počítačů a klasických virtuálních počítačů.
* Podporují se všechny typy zálohování (úplný/rozdíl/protokol) a modely obnovení (jednoduché/úplné/hromadné protokolování).
* Pro databáze **jen pro čtení** : úplné a pouze úplné zálohování je jediným podporovaným typem zálohování.
* Nativní komprese SQL je podporována, pokud je explicitně povolena uživatelem v zásadách zálohování. Azure Backup přepisuje výchozí hodnoty na úrovni instance pomocí klauzule COMPRESSION/NO_COMPRESSION v závislosti na hodnotě tohoto ovládacího prvku nastavené uživatelem.
* Zálohování databáze s podporou TDE je podporováno. Chcete-li obnovit TDE šifrovanou databázi do jiné SQL Server, je nutné nejprve [obnovit certifikát na cílový server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). Komprese záloh pro databáze s povoleným TDE pro SQL Server 2016 a novější verze je dostupná, ale v nižší velikosti přenosu, jak je popsáno [zde](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Operace zálohování a obnovení pro databáze zrcadlení a snímky databáze nejsou podporovány.
* SQL Server **instance clusteru s podporou převzetí služeb při selhání (FCI)** se nepodporuje.
* Použití více než jednoho řešení zálohování k zálohování samostatné instance SQL Server nebo skupiny dostupnosti Always On SQL může způsobit selhání zálohování. Upustí od tohoto postupu. Zálohování dvou uzlů skupiny dostupnosti jednotlivě se stejnými nebo různými řešeními může také vést k chybě zálohování.
* Po nakonfigurování skupin dostupnosti jsou zálohy z různých uzlů odebírány na základě několika faktorů. Chování zálohování skupiny dostupnosti je shrnuto níže.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Chování zálohování v případě skupin dostupnosti AlwaysOn

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
Copy-Only úplné |  Primární

#### <a name="backup-preference-secondary-only"></a>Předvolby zálohování: jenom sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Copy-Only úplné |  Sekundární

#### <a name="backup-preference-secondary"></a>Předvolby zálohování: sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Copy-Only úplné |  Sekundární

#### <a name="no-backup-preference"></a>Žádná předvolba zálohování

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciál | Primární
Protokol |  Sekundární
Copy-Only úplné |  Sekundární

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat databázi SQL Server](backup-azure-sql-database.md) , která běží na virtuálním počítači Azure.