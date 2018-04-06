---
title: Azure SQL Database automatické geograficky redundantní zálohy | Microsoft Docs
description: SQL Database automaticky vytvoří zálohu místní databáze každých několik minut a používá Azure geograficky redundantní úložiště s přístupem pro čtení pro geografická redundance.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ab1793621950fd57d3f0be545772d85b32f5d7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>Další informace o automatické zálohování databáze SQL

SQL Database automaticky vytvoří zálohy databáze a k poskytování geografická redundance používá Azure přístup pro čtení geograficky redundantní úložiště (RA-GRS). Tyto zálohy se vytvoří automaticky a bez dalších poplatků. Nemusíte dělat nic, aby byly dojít. Zálohování databáze jsou nedílnou součást vámi vyžádaných jakékoli obchodní strategie pro obnovení kontinuity a po havárii, protože se data chránit před náhodnou poškození nebo odstranění. Pokud chcete zachovat zálohy v vlastní kontejner úložiště budete konfigurovat zásadu dlouhodobé uchovávání záloh. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Co je zálohování SQL Database?

SQL Database využívá technologii SQL Server k vytvoření [úplné](https://msdn.microsoft.com/library/ms186289.aspx), [rozdílové](https://msdn.microsoft.com/library/ms175526.aspx), a [transakčního protokolu](https://msdn.microsoft.com/library/ms191429.aspx) zálohy pro účely v okamžiku obnovení (Možnosti PITR). Zálohování transakčního protokolu obecně dojít každých 5 až 10 minut, s četností na základě úroveň výkonu a množství databázové aktivitě. Zálohování transakčního protokolu v případě zálohování úplné a rozdílové umožňují obnovení databáze do konkrétní v daném okamžiku na stejný server, který je hostitelem databáze. Při obnovování databáze, na které úplné rozdíl a transakce protokolu zálohování je nutné obnovit hodnoty službu.


Můžete použít tyto zálohy na:

* Obnovení databáze do v daném okamžiku v rámci dobu uchování. Tato operace vytvoří novou databázi ve stejném serveru jako původní databázi.
* Obnovte odstraněnou databázi na čas, kdy byla odstraněna nebo kdykoli v rámci dobu uchování. Odstraněné databáze lze obnovit pouze na stejném serveru, kde byl vytvořen původní databázi.
* Obnovení databáze do jiné zeměpisné oblasti. Umožňuje provést obnovení po havárii geografické, když nelze získat přístup k serveru a databáze. Vytvoří novou databázi v jakékoli existující server kdekoli v celém světě. 
* Obnovení databáze z konkrétní dlouhodobé zálohování v případě, že databáze je nakonfigurovaná s dlouhodobé zásady uchovávání informací. Tímto způsobem lze obnovit původní verzi databáze vyhovovaly žádosti o dodržování předpisů nebo spuštění starší verzi aplikace. V tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).
* Chcete-li provést obnovení, přečtěte si téma [obnovit databázi ze zálohy](sql-database-recovery-using-backups.md).

> [!NOTE]
> V úložišti Azure termín *replikace* odkazuje na kopírování souborů z jednoho umístění do druhého. SQL *replikace databáze* odkazuje na zadáte-li více sekundárních databází synchronizovány s primární databáze. 
> 

## <a name="how-often-do-backups-happen"></a>Jak často zálohám?
Úplná databáze zálohám týdně, rozdílové databáze obvykle zálohám každých několik hodin a transakčního protokolu, které se obecně zálohám každých 5 až 10 minut. První úplné zálohování je naplánováno ihned po vytvoření databáze. Obvykle dokončení do 30 minut, ale může trvat déle, pokud je databáze značné velikosti. Například prvotní zálohování může trvat déle na obnovené databáze nebo kopii databáze. Po první úplné zálohování jsou všechny další zálohy naplánované automaticky a bezobslužně spravovat na pozadí. Služba SQL Database je určena přesné načasování všechny zálohy databáze při vyvažování celkové zatížení systému. 

Geografická replikace úložiště záloh dochází na základě plánu replikace Azure Storage.

## <a name="how-long-do-you-keep-my-backups"></a>Jak dlouho necháte Moje zálohování?
Doba uchování, která je založená na má každá záloha databáze SQL [vrstvy služeb](sql-database-service-tiers.md) databáze. Doba uchování pro databázi v:


* Úroveň služby na úrovni Basic je 7 dní.
* Úroveň služby na úrovni Standard je 35 dní.
* Úroveň služeb Premium je 35 dnů.
* Obecné účely vrstvy je možné konfigurovat pomocí maximální 35 dní (7 dnů ve výchozím nastavení) *
* Kritické obchodní vrstvy (preview) je možné konfigurovat pomocí maximální 35 dní (7 dnů ve výchozím nastavení) *

\* Během preview doba uchovávání záloh není Konfigurovatelný a je nastaven na 7 dní.

Pokud převedete databázi s delší dobou uchování záloh na databázi s kratší dobou uchování, všechny stávající zálohy starší než doba uchování vrstvy cíl již nejsou k dispozici.

Pokud provádíte upgrade databáze s kratší dobu uchovávání dat do databáze s delší dobou, SQL Database udržuje existující zálohy, dokud nebude dosaženo delší dobu uchování. 

Pokud odstraníte databázi, SQL Database udržuje zálohování stejným způsobem, který by tomu bylo v databázi online. Předpokládejme například, že odstraníte databáze Basic, který má dobu uchování o délce sedm dní. Zálohy, která je čtyři dny je uložit pro další tři dny.

> [!IMPORTANT]
> Pokud odstraníte serveru Azure SQL, který je hostitelem databází SQL, všechny databáze, které patří k serveru budou také odstraněny a nelze jej obnovit. Nelze obnovit odstraněné serveru.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Jak prodloužit dobu uchovávání záloh?

Pokud vaše aplikace vyžaduje, aby zálohování jsou k dispozici dobu delší než maximální doba uchovávání záloh možnosti PITR, můžete nakonfigurovat zásadu dlouhodobé uchovávání záloh pro jednotlivé databáze (zleva doprava zásady). To umožňuje rozšířit dobu uchování vytvořené it z maximální 35 dní až 10 let. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

Jakmile přidáte zásad zleva doprava k databázi pomocí portálu Azure nebo rozhraní API, týdenní zálohy úplné databáze se automaticky zkopírují na samostatné RA-GRS kontejner úložiště pro dlouhodobé uchovávání (zleva doprava úložiště). Pokud vaše databáze je šifrován TDE zálohy se šifrují automaticky v klidovém stavu. Databáze SQL se automaticky odstraní vypršela platnost záloh na základě jejich časové razítko a zásad zleva doprava. Poté, co nastavíte zásady, nemusíte spravovat plán zálohování nebo starat o vyčištění staré soubory. Portál Azure nebo prostředí PowerShell slouží k zobrazení, obnovení nebo odstranění tyto zálohy.

## <a name="are-backups-encrypted"></a>Jsou zálohy šifrovat?

Když je povolené šifrování TDE pro Azure SQL database, jsou šifrované zálohování. Ve výchozím nastavení povolené šifrování TDE nastaveny všechny nové databáze Azure SQL. Další informace o šifrování TDE najdete v tématu [transparentní šifrování dat s Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Automatické zálohování jsou kompatibilní s GDPR?
Pokud záloha obsahuje osobní data, která je předmětem obecné Data Protection nařízení (GDPR), je nutné použít rozšířené bezpečnostní opatření k ochraně dat před neoprávněným přístupem. Pro dosažení souladu s GDPR, potřebujete způsob, jak spravovat data žádosti vlastníků dat bez nutnosti pro přístup k zálohování.  Pro krátkodobé zálohování může být jedno řešení tak, aby zkrátil zálohování povolené okna v části 30 dní, což je čas na dokončení žádosti o přístup data.  Pokud delší období zálohy, se doporučuje jenom "pseudonymních" data ukládat v zálohování. Například pokud data o osoby je nutné odstranit nebo aktualizovat, nebude vyžadovat odstranění nebo aktualizaci existující zálohy. Můžete najít další informace o osvědčených postupech GDPR v [řízení dat pro dodržování předpisů GDPR](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html).

## <a name="next-steps"></a>Další postup

- Zálohování databáze jsou nedílnou součást vámi vyžádaných jakékoli obchodní strategie pro obnovení kontinuity a po havárii, protože se data chránit před náhodnou poškození nebo odstranění. Další informace o jiných Azure SQL Database obchodní kontinuity řešení najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).
- Obnovit k určitému bodu v čase pomocí portálu Azure, najdete v tématu [obnovit databázi k určitému bodu v čase pomocí portálu Azure](sql-database-recovery-using-backups.md).
- Chcete-li obnovit k určitému bodu v čase pomocí prostředí PowerShell, přečtěte si téma [obnovit databázi k určitému bodu v čase pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Pokud chcete konfigurovat, spravovat a obnovit ze dlouhodobé uchovávání automatické zálohy v trezoru služeb zotavení Azure pomocí portálu Azure najdete v tématu [spravovat pomocí portálu Azure dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).
- Pokud chcete konfigurovat, spravovat a obnovit ze dlouhodobé uchovávání automatické zálohování v trezoru služeb zotavení Azure pomocí prostředí PowerShell najdete v tématu [spravovat pomocí prostředí PowerShell dlouhodobé uchovávání záloh](sql-database-long-term-backup-retention-configure.md).
