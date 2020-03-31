---
title: Matice podpory zálohování Azure pro zálohování serveru SQL Server ve virtuálních počítačích Azure
description: Obsahuje souhrn nastavení a omezení podpory při zálohování SQL Serveru ve virtuálních počítačích Azure se službou Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409996"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matice podpory pro zálohování serveru SQL Server ve virtuálních počítačích Azure

Azure Backup můžete použít k zálohování databází SQL Serveru ve virtuálních počítačích Azure hostovaných na cloudové platformě Microsoft Azure. Tento článek shrnuje obecné nastavení podpory a omezení pro scénáře a nasazení SQL Server Backup ve virtuálních počítačích Azure.

## <a name="scenario-support"></a>Podpora scénářů

**Podpora** | **Podrobnosti**
--- | ---
**Podporovaná nasazení** | Virtuální počítače AZURE azure a virtuální počítače jiného než Marketplace (SQL Server s ruční instalací) jsou podporované.
**Podporované geografické** | Austrálie jihovýchod (ASE), východní Austrálie (AE), Austrálie Central (AC), Austrálie Central 2 (AC) <br> Brazílie – jih (BRS)<br> Canada Central (CNC), Kanada východ (CE)<br> Jihovýchodní Asie (SEA), Východní Asie (EA) <br> východní USA (EUS), východní USA 2 (EUS2), západní centrální USA (WCUS), západní USA (WUS); Západní USA 2 (WUS 2) Severní centrální USA (NCUS) Centrální USA (CUS) Jižní centrální USA (SCUS) <br> India Central (INC), India South (INS), India West <br> Japonsko východ (JPE), Japonsko západ (JPW) <br> Korea Central (KRC), Korea South (KRS) <br> Severní Evropa (NE), Severní Evropa <br> Spojené království Jih (UKS), Spojené království Západ (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Německo – sever, Německo – západ – střed <br> Švýcarsko Sever, Švýcarsko Západ <br> Francie – střed <br> Čína východ, Čína východ 2, Čína Sever, Čína Sever 2
**Podporované operační systémy** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux není v současné době podporován.
**Podporované verze serveru SQL Server** | SQL Server 2019, SQL Server 2017 jak je podrobně popsáno na [stránce životního cyklu vyhledávacího produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 a SPs, jak je podrobně popsáno na stránce životního cyklu [vyhledávacího produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Vývojář, Express.
**Podporované verze rozhraní .NET** | Rozhraní .NET Framework 4.5.2 nebo novější nainstalované ve virtuálním počítači

## <a name="feature-consideration-and-limitations"></a>Zvažování a omezení funkcí

* Zálohu SQL Serveru lze nakonfigurovat na webu Azure Portal nebo **PowerShellu**. Nepodporujeme CLI.
* Řešení se podporuje na obou typech [nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – virtuální počítače Azure Resource Manager a klasické virtuální počítače.
* Virtuální počítač se systémem SQL Server vyžaduje připojení k internetu pro přístup k veřejným IP adresám Azure.
* Instance **clusteru sql server s podporou převzetí služeb při selhání (FCI)** není podporována.
* Operace zálohování a obnovení pro zrcadlové databáze a snímky databáze nejsou podporovány.
* Použití více než jednoho řešení zálohování k zálohování samostatné instance serveru SQL Server nebo SQL Always na skupinu dostupnosti může vést k selhání zálohování; zdržet se tak.
* Zálohování dvou uzlů skupiny dostupnosti jednotlivě se stejnými nebo různými řešeními může také vést k selhání zálohování.
* Azure Backup podporuje jenom úplné a úplné typy zálohování jen pro kopírování pro databáze **jen pro čtení.**
* Databáze s velkým počtem souborů nelze chránit. Maximální počet podporovaných souborů je **~1000**.  
* V úschovně můžete zálohovat až do databáze serveru SQL Server **~2000.** Můžete vytvořit více trezorů v případě, že máte větší počet databází.
* Zálohování můžete nakonfigurovat až pro **50** databází najednou. toto omezení pomáhá optimalizovat zatížení zálohování.
* Podporujeme databáze o velikosti až **2 TB;** pro velikosti větší než, že problémy s výkonem může přijít.
* Chcete-li mít představu o tom, kolik databází lze chránit na server, zvažte faktory, jako je šířka pásma, velikost virtuálního počítače, frekvence zálohování, velikost databáze a tak dále. [Stáhněte si](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) plánovač prostředků a vypočítejte přibližný počet databází, které můžete mít na server na základě prostředků virtuálního počítače a zásad zálohování.
* Při konfiguraci skupiny dostupnosti jsou zálohy převzaty z různých uzlů na základě několika faktorů. Chování zálohování pro skupinu dostupnosti je shrnuto níže.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Zálohování chování pomocí vždy na skupinách dostupnosti

Doporučujeme, aby záloha byla nakonfigurována pouze na jednom uzlu skupiny dostupnosti (AG). Vždy nakonfigurujte zálohování ve stejné oblasti jako primární uzel. Jinými slovy vždy potřebujete primární uzel, aby byl přítomen v oblasti, kde konfigurujete zálohu. Pokud jsou všechny uzly AG ve stejné oblasti, kde je nakonfigurována záloha, není žádné obavy.

#### <a name="for-cross-region-ag"></a>Pro meziregionální AG

* Bez ohledu na předvolbu zálohování budou zálohy spuštěny pouze z uzlů, které jsou ve stejné oblasti, kde je záloha nakonfigurována. Důvodem je, že zálohy mezi oblastmi nejsou podporovány. Pokud máte pouze dva uzly a sekundární uzel je v jiné oblasti, zálohy budou nadále spouštět z primárního uzlu (pokud vaše předvolba zálohování je "pouze sekundární").
* Pokud uzel převezme služby při selhání do oblasti jiné, než je oblast, kde je záloha nakonfigurována, zálohování se nezdaří na uzlech v oblasti převzetí služeb při selhání.

V závislosti na předvolbách zálohování a typech záloh (úplné/diferenciální/log/copy-only full) jsou zálohy převzaty z určitého uzlu (primární/sekundární).

#### <a name="backup-preference-primary"></a>Předvolba zálohování: Primární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciální | Primární
Protokol |  Primární
Pouze kopie úplné |  Primární

#### <a name="backup-preference-secondary-only"></a>Záložní předvolba: Pouze sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciální | Primární
Protokol |  Sekundární
Pouze kopie úplné |  Sekundární

#### <a name="backup-preference-secondary"></a>Záložní předvolba: Sekundární

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciální | Primární
Protokol |  Sekundární
Pouze kopie úplné |  Sekundární

#### <a name="no-backup-preference"></a>Bez předvolby zálohování

**Typ zálohování** | **Node**
--- | ---
Do bloku | Primární
Diferenciální | Primární
Protokol |  Sekundární
Pouze kopie úplné |  Sekundární

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [zálohovat databázi SQL Serveru,](backup-azure-sql-database.md) která běží na virtuálním počítači Azure.
