---
title: "Uložte zálohy databáze SQL Azure pro až 10 let | Microsoft Docs"
description: "Zjistěte, jak Azure SQL Database podporuje ukládání záloh až 10 let."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 2f31e89fce2746e57d6a670aef949d0d534af4c1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Uložte zálohy databáze SQL Azure až 10 let.
Mnoho aplikací mít regulačních, dodržování předpisů nebo jiné obchodní účely, které vyžadují, abyste uchování záloh databáze nad rámec 7-35 dní, poskytuje Azure SQL Database [automatické zálohování](sql-database-automated-backups.md). Pomocí funkce dlouhodobé uchovávání záloh, můžete uložit zálohování databáze SQL v trezoru služeb zotavení Azure až 10 let. Můžete uložit až 1 000 databází na jeden trezor. Pak můžete vybrat jakékoli zálohy v trezoru obnovit jako novou databázi.

> [!IMPORTANT]
> Dlouhodobé uchovávání záloh je momentálně ve verzi preview a je k dispozici v následujících oblastech: Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, střed USA, východní Asie, východní USA, Východ USA 2, Indie – střed, Indie – Jih, Japonsko – východ, Japonsko – Západ, Sever střední USA, severní Evropa, střed USA – Jih, jihovýchodní Asie, západní Evropa a západní USA.
>

> [!NOTE]
> Až 200 databáze jednomu trezoru můžete povolit v období 24 hodin. Doporučujeme použít samostatné úložiště pro každý server pro minimalizaci dopadů toto omezení. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Jak funguje dlouhodobé uchovávání záloh databáze SQL

S dlouhodobé uchovávání záloh můžete databázový server SQL přidružit trezoru služeb zotavení Azure. 

* Ve stejném předplatném Azure, který vytvořili systému SQL server a ve stejné zeměpisné oblasti a skupina prostředků je třeba vytvořit trezor. 
* Nakonfigurujete zásady uchovávání informací pro všechny databáze. Zásady způsobí, že týdenní zálohy databáze úplné zkopírován do trezoru služeb zotavení a uchovávají po dobu uchovávání (až 10 let). 
* Potom můžete obnovit databázi z jakéhokoli z těchto zálohování pro novou databázi v libovolném serveru v odběru. Úložiště Azure vytvoří kopii z existující zálohy a o kopírování nemá žádný vliv výkon na existující databázi.

> [!TIP]
> Postupy: informace najdete v tématu [konfigurace a obnovení z Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Povolit dlouhodobé uchovávání záloh

Postup konfigurace dlouhodobé uchovávání záloh pro databázi:

1. Vytvoření trezoru služeb zotavení Azure služby ve stejné oblasti, předplatné a skupina prostředků jako databázový server SQL. 
2. Registraci serveru do trezoru.
3. Vytvoření zásady ochrany služeb zotavení Azure.
4. Použijte zásady ochrany pro databáze, které vyžadují dlouhodobé uchovávání záloh.

Ke konfiguraci, správě a obnovit databázi z dlouhodobé uchovávání záloh automatizované zálohování v trezoru služeb zotavení Azure, proveďte jednu z následujících akcí:

* Pomocí portálu Azure: klikněte na tlačítko **dlouhodobé uchovávání záloh**, vyberte databázi a pak klikněte na tlačítko **konfigurace**. 

   ![Vyberte databázi pro dlouhodobé uchovávání záloh](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Pomocí prostředí PowerShell: Přejděte na [konfigurace a obnovení z Azure SQL Database dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Obnovit databázi, která je uložena s funkci dlouhodobé uchovávání záloh

Obnovení ze zálohy dlouhodobé uchovávání záloh:

1. Zobrazí seznam v úložišti, kde je uložena záloha.
2. Zobrazí seznam kontejneru, který je namapovaný k logickému serveru.
3. Zobrazí seznam zdroj dat v úložišti, který je namapovaný k vaší databázi.
4. Zobrazí seznam bodů obnovení, které jsou k dispozici pro obnovení.
5. Obnovte databázi z bodu obnovení na cílový server v rámci vašeho předplatného.

Ke konfiguraci, správě a obnovit databázi z dlouhodobé uchovávání záloh automatizované zálohování v trezoru služeb zotavení Azure, proveďte jednu z následujících akcí:

* Pomocí portálu Azure: přejděte na [spravovat pomocí portálu Azure dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md). 

* Pomocí prostředí PowerShell: Přejděte na [spravovat pomocí prostředí PowerShell dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Získat ceny pro dlouhodobé uchovávání záloh

Dlouhodobé uchovávání záloh databáze SQL je účtován podle požadavků [služby Azure backup ceny sazby](https://azure.microsoft.com/pricing/details/backup/).

Po databáze serveru SQL je registrovaný k úložišti, vám budou účtovat celkové úložiště, který je používán týdenní zálohy uložené v trezoru.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Zobrazit dostupné zálohy, které jsou uložené v dlouhodobé uchovávání záloh

Ke konfiguraci, správě a obnovit databázi z dlouhodobé uchovávání záloh automatizované zálohování v trezoru služeb zotavení Azure pomocí portálu Azure, proveďte jednu z následujících akcí:

* Pomocí portálu Azure: přejděte na [spravovat pomocí portálu Azure dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md). 

* Pomocí prostředí PowerShell: Přejděte na [spravovat pomocí prostředí PowerShell dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Zakázat dlouhodobé uchovávání

Služba obnovení automaticky zpracovává čištění na základě zásad zadané uchovávání záloh. 

Chcete-li zastavit odesílání zálohy pro konkrétní databázi do trezoru, odeberte zásady uchovávání informací pro tuto databázi.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> Zálohování, které jsou již v trezoru jsou poškozena. Jsou automaticky odstraněny službou obnovení po dobu uchování vyprší.

## <a name="long-term-backup-retention-faq"></a>Dlouhodobé uchovávání záloh – nejčastější dotazy

**Můžete ručně odstranit konkrétní zálohy v trezoru?**

Aktuálně nepodporuje. Trezor záloh automaticky vyčistí, pokud vypršela doba uchování.

**Můžete zaregistrovat svůj server a uložte zálohy do více než jednoho trezoru?**

Ne, můžete uložit aktuálně pouze jeden trezor záloh v čase.

**Může mít trezoru a server v různých předplatných?**

Ne, aktuálně trezoru a server musí být ve stejném předplatném a skupině prostředků.

**Můžete použít k trezoru, vytvořené v oblasti, která se liší od oblasti svému serveru?**

Ne, trezoru a server musí být ve stejné oblasti minimalizovat dobu kopírování a zamezit tak poplatky za provozu.

**Kolik databáze můžete ukládat do jednoho trezoru?**

V současné době podporujeme až 1 000 databází na jeden trezor. 

**Kolik trezorů můžete vytvořit na jedno předplatné?**

Můžete vytvořit až pro 25 trezorů na jedno předplatné.

**Kolik databází můžete nakonfigurovat za den za trezoru?**

Můžete nastavit 200 databáze za den za trezor.

**Funguje s elastické fondy dlouhodobé uchovávání záloh?**

Ano. Všechny databáze ve fondu můžete nakonfigurovat zásady uchovávání informací.

**Můžete vybrat v době, kdy je vytvoření zálohy?**

Ne, databáze SQL určuje plán zálohování pro minimalizaci vlivu na výkon vašich databází.

**Je nutné transparentní šifrování dat pro databázi povoleno. Můžete použít ho k trezoru?** 

Ano, je podporováno transparentní šifrování dat. Databázi můžete obnovit z trezoru i v případě, že původní databázi již existuje.

**Co se stane s zálohy v trezoru, pokud je pozastavená Moje předplatné?** 

Pokud je předplatné pozastavené, jsme zachovat stávající databáze a zálohování. Nových záloh nejsou zkopírovány do trezoru. Po předplatné znovu aktivujete, službu obnoví kopírování zálohování do trezoru. Svůj trezor bude přístupný pro operace obnovení pomocí zálohování, které byly zkopírovány existuje před pozastavením předplatné. 

**Můžete získat přístup k záložní soubory databáze SQL, tak I stáhnout nebo obnovit je do systému SQL server?**

Ne, aktuálně nepodporuje.

**Je možné, že více plánů (denně, týdně, měsíčně, ročně) v rámci zásady uchovávání informací SQL.**

Ne, víc plány jsou aktuálně dostupné jen pro zálohy virtuálních počítačů.

**Co když nastavíme dlouhodobé uchovávání záloh na databázi, která se nachází aktivní geografickou replikací sekundární databáze?**

Protože jsme nemáte trvat zálohy na replikách, je aktuálně žádná možnost pro dlouhodobé uchovávání zálohování na sekundární databáze. Je ale důležité pro uživatele nastavit dlouhodobé uchovávání záloh na sekundární databázi aktivní geografickou replikaci z těchto důvodů:
* Pokud dojde převzetí služeb při selhání a databáze se stane primární databázi, jsme trvat úplné zálohování, což je nahrán do trezoru.
* Existuje nejsou zpoplatněné zákazník pro nastavení dlouhodobé uchovávání záloh na sekundární databáze.

## <a name="next-steps"></a>Další postup
Protože zálohy databáze chránit data před náhodným poškození nebo odstranění, jsou nedílnou součást vámi vyžádaných žádné kontinuity podnikových procesů a strategie zotavení po havárii. Další informace o jiných řešení kontinuity podnikových procesů databáze SQL najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).
