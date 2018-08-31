---
title: Azure SQL Database automatické, geograficky redundantní zálohy | Dokumentace Microsoftu
description: SQL Database automaticky vytvoří zálohu místní databáze každých několik minut a používá Azure geograficky redundantní úložiště jen pro čtení pro geografickou redundancí.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ce7c41730bec4e014225fb8c744d029493f5ec2c
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246782"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Další informace o automatických zálohách databáze SQL

SQL Database automaticky vytvoří zálohy databáze a používá Azure geograficky redundantní úložiště jen pro čtení (RA-GRS) pro zajištění geografickou redundancí. Tyto zálohy se vytvoří automaticky a bez dalších poplatků. Nemusíte dělat nic, abyste je provedli. Zálohování databáze jsou nedílnou součást každé strategie obchodní kontinuity podnikových procesů a po havárii pro obnovení, protože jejich Chraňte svoje data před náhodným poškozením nebo odstranění. Pokud vaše pravidla zabezpečení vyžadovat, že zálohy, které jsou k dispozici pro delší dobu, můžete nakonfigurovat zásadu dlouhodobého uchovávání záloh. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je SQL Database zálohy?

SQL Database využívá technologii SQL Server k vytvoření [úplné](https://msdn.microsoft.com/library/ms186289.aspx), [rozdílové](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), a [transakční protokol](https://msdn.microsoft.com/library/ms191429.aspx) obnovení zálohy pro účely bodu v čase (PITR). Zálohy protokolu transakce obvykle k dojde každých 5 až 10 minut a rozdílové zálohování obvykle každých 12 hodin, s frekvencí podle úrovně výkonu a objem databázové aktivity. Zálohy transakčního protokolu, s úplné a rozdílové zálohy, bylo možné obnovit databázi konkrétního bodu za běhu na stejný server, který je hostitelem databáze. Zálohy jsou uložené v objektech BLOB úložiště RA-GRS, které se replikují do [spárovaném datovém centru](../best-practices-availability-paired-regions.md) pro ochranu před výpadku datového centra. Při obnovování databáze služby přijde na to, které úplného, rozdílového a transakce protokolu zálohy je nutné obnovit.


Můžete použít tyto zálohy na:

* Obnovte databázi do doby uchování v daném okamžiku. Tato operace vytvoří novou databázi na stejném serveru jako je původní databáze.
* Obnovení odstraněné databáze na čas, kdy byla odstraněna nebo libovolný čas v rámci doby uchování. Odstraněnou databázi můžete obnovit jen na stejném serveru, kde původní databáze byla vytvořena.
* Obnovení databáze do jiné geografické oblasti. Umožňuje provést obnovení po havárii zeměpisné, když nelze přistupovat k serveru a databáze. Vytvoří novou databázi v jakékoli existující server kdekoli v celém světě. 
* Obnovení databáze z konkrétní dlouhodobé zálohování v případě databáze má nakonfigurované zásady dlouhodobého uchovávání informací (LTR). To umožňuje obnovit starší verzi databáze, které by vyhovovaly žádosti o dodržování předpisů nebo spuštění starší verze aplikace. Zobrazit [dlouhodobé uchovávání](sql-database-long-term-retention.md).
* Pokud chcete provést obnovení, najdete v článku [obnovit databázi ze zálohy](sql-database-recovery-using-backups.md).

> [!NOTE]
> Ve službě Azure storage termín *replikace* odkazuje na kopírování souborů z jednoho umístění do druhého. SQL *replikace databáze* odkazuje na udržování více sekundární databází, které jsou synchronizovány s primární databáze. 
> 

## <a name="how-long-are-backups-kept"></a>Jak dlouho se zálohy uchovávat?
Každá záloha databáze SQL má výchozí dobu uchování, která je založená na úrovni služeb databáze a liší [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Můžete aktualizovat období uchování zálohy pro databázi. Zobrazit [období uchování zálohy změnu](#how-to-change-backup-retention-period) další podrobnosti.

Když odstraníte databáze, databáze SQL zachovají zálohy stejným způsobem, který by tomu bylo online databáze. Například pokud odstraníte databázi Basic, který má dobu uchování o délce sedm dní, zálohy, která je starší čtyř dní uložená pro další tři dny.

Pokud chcete zachovat zálohy po dobu delší než maximální doba uchovávání PITR, můžete upravit zálohování vlastnosti, které chcete přidat jeden nebo více dlouhodobé období uchovávání dat do databáze. Zobrazit [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md) další podrobnosti.

> [!IMPORTANT]
> Při odstranění serveru Azure SQL server, který je hostitelem databází SQL, odstraní se také všechny elastických fondů a databází, které patří k serveru a nelze ji obnovit. Nelze obnovit server odstranil. Ale pokud jste nakonfigurovali dlouhodobé uchovávání, zálohování databází s LTR nebudou odstraněny, a dají se obnovit tyto databáze.

### <a name="pitr-retention-period"></a>Doba uchování PITR
Výchozí době uchování databáze vytvořené využitím nákupní model založený na DTU, závisí na úrovni služby:

* Úroveň služeb Basic je 1 týden.
* Úrovně služeb Standard je 5 týdnů.
* Úroveň Premium služby je 5 týdnů.

Pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), uchovávání záloh je možností konfigurace až na 35 dní. 

Pokud aktuální dobu uchovávání PITR snížíte, všechny existující zálohy, které jsou starší než novým obdobím uchovávání nadále již nebudou dostupné. 

Pokud zvýšíte aktuální dobu uchovávání PITR, SQL Database budete mít existující zálohy, dokud nebude dosaženo delší doba uchovávání dat.

## <a name="how-often-do-backups-happen"></a>Jak často k dochází zálohy?
### <a name="backups-for-point-in-time-restore"></a>Zálohy pro obnovení k určitému bodu v čase
SQL Database podporuje samoobslužné funkce pro obnovení k určitému bodu v čase (PITR) automaticky vytváří úplné zálohy, rozdílové zálohování a zálohování protokolů transakcí. Úplné zálohy databáze se vytvoří každý týden, rozdílovými zálohami prováděnými obecně vytvářejí každých 12 hodin a zálohy protokolu transakce obvykle vytvářejí každých 5 až 10 minut, s frekvencí podle úrovně výkonu a objem databázové aktivity. Bude první úplná záloha je naplánováno ihned po vytvoření databáze. Obvykle hotové během 30 minut, ale může trvat déle, když je databáze významnou velikostí. Například prvotní zálohování může trvat déle v obnovené databáze nebo kopie databáze. Po dokončení první úplné zálohování všechny další zálohy jsou automaticky naplánované a spravované tiše na pozadí. Služba SQL Database určuje přesné načasování všechny zálohy databáze jako vyrovnává celkové zatížení systému.

Zálohy PITR jsou geograficky redundantní a chráněný [mezi zónami replikace Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Další informace najdete v tématu [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Pro dlouhodobé uchovávání záloh
SQL Database nabízí možnost konfigurace dlouhodobého uchovávání dat (LTR) z úplné zálohy po dobu až 10 let ve službě Azure blob storage. Pokud je povolené zásady LTR, týdenními úplnými zálohami se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Aby splnila požadavek na dodržení jiný, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Spotřeba úložiště závisí na vybrané četnosti zálohování a období uchovávání. Můžete použít [LTR pomocí cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/?service=sql-database) odhadnout náklady na úložiště zleva doprava. 

Podobně jako PITR, zálohy zleva doprava jsou geograficky redundantní a chráněný [replikace Azure Storage mezi zónami](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Další informace najdete v tématu [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Jsou šifrované zálohování

Pokud vaše databáze je zašifrovaný pomocí šifrování TDE, záloh se šifrují automaticky v klidovém stavu, včetně záloh zleva doprava. Pokud pro službu Azure SQL database je povoleno TDE, je také šifrovaný zálohy. Všechny nové databáze Azure SQL jsou konfigurované tak transparentní šifrování dat ve výchozím nastavení povolená. Další informace o transparentní šifrování dat, naleznete v tématu [transparentního šifrování dat s využitím Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Vliv automatizovaných záloh Moje dodržování předpisů

Při migraci databáze z úrovně služeb na základě DTU se výchozí PITR uchovávání po dobu 35 dní, do vrstvy služeb založený na virtuálních jádrech PITR uchovávání se zachovají zajistit, že není ohrožena zásad pro obnovení dat vaší aplikace. Pokud výchozí uchování nesplňuje vaše požadavky na dodržování předpisů, můžete změnit dobu uchování PITR pomocí Powershellu nebo rozhraní REST API. Zobrazit [období uchování zálohy změnu](#how-to-change-backup-retention-period) další podrobnosti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Změna doby uchovávání záloh
Můžete změnit výchozí uchování pomocí rozhraní REST API nebo Powershellu. Podporované hodnoty jsou: 7, 14, 21, 28 nebo 35 dnů. Následující příklady ukazují, jak změnit PITR uchovávání informací na 28 dnů. 

> [!NOTE]
> Rozhraní API tez ovlivní pouze PITR dobu uchování. Pokud jste nakonfigurovali zleva doprava pro vaši databázi, nebude mít vliv. Zobrazit [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md) podrobnosti o tom, jak změnit období uchování zleva doprava.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Změnit PITR období uchování zálohy pomocí Powershellu
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Toto rozhraní API je součástí modulu Powershellu AzureRM.Sql od verze [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Změnit dobu uchování PITR pomocí rozhraní REST API
**Ukázková žádost**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Text žádosti**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Ukázková odpověď**

Stavový kód: 200
```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```
Zobrazit [rozhraní REST API pro uchování zálohy](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) další podrobnosti.

## <a name="next-steps"></a>Další postup

- Zálohování databáze jsou nedílnou součást každé strategie obchodní kontinuity podnikových procesů a po havárii pro obnovení, protože jejich Chraňte svoje data před náhodným poškozením nebo odstranění. Další informace o jiná řešení kontinuity podnikových procesů Azure SQL Database business najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
- Obnovení do bodu v čase pomocí webu Azure portal, najdete v článku [obnovit databázi do bodu v čase pomocí webu Azure portal](sql-database-recovery-using-backups.md).
- Pokud chcete obnovit do bodu v čase s použitím prostředí PowerShell, naleznete v tématu [obnovit databázi do bodu v čase s použitím prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Můžete konfigurovat, spravovat a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů blob v Azure pomocí webu Azure portal najdete v tématu [správa dlouhodobého uchovávání záloh pomocí webu Azure portal](sql-database-long-term-backup-retention-configure.md).
- Konfigurovat, spravovat a obnovení z dlouhodobě uchovávaných z automatických záloh na blogu Azure storage pomocí Powershellu, přečtěte si téma [správa dlouhodobého uchovávání záloh pomocí Powershellu](sql-database-long-term-backup-retention-configure.md).
