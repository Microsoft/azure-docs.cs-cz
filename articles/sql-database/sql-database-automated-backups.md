---
title: Azure SQL Database automatické, geograficky redundantní zálohy | Dokumentace Microsoftu
description: SQL Database automaticky vytvoří zálohu místní databáze každých několik minut a používá Azure geograficky redundantní úložiště jen pro čtení pro geografickou redundancí.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b8c7abacbf99bfbf68b7dd76a01011c8220bf9f2
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608456"
---
# <a name="automated-backups"></a>Automatizované zálohy

SQL Database automaticky vytvoří zálohy databáze, které jsou zachovány mezi 7 až 35 dnů a využívají Azure geograficky redundantní úložiště jen pro čtení (RA-GRS) k zajištění toho, že jsou zachovány i v případě, že není k dispozici datové centrum. Tyto zálohy se vytvoří automaticky a bez dalších poplatků. Nemusíte dělat nic, abyste je provedli a můžete je [změnit období uchování zálohy](#how-to-change-the-pitr-backup-retention-period). Zálohování databáze jsou nedílnou součást každé strategie obchodní kontinuity podnikových procesů a po havárii pro obnovení, protože jejich Chraňte svoje data před náhodným poškozením nebo odstranění. Pokud vaše pravidla zabezpečení vyžadovat, že zálohy, které jsou k dispozici pro delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je SQL Database zálohy

SQL Database využívá technologii SQL Server k vytvoření [úplné](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [rozdílové](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), a [transakční protokol](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) obnovení zálohy pro účely bodu v čase (PITR). Zálohy protokolu transakce obvykle k dojde každých 5 až 10 minut a rozdílové zálohování obvykle každých 12 hodin, s frekvencí podle výpočetního prostředí a objem databázové aktivity. Úplné a rozdílové zálohování a transakce zálohy protokolů umožňují obnovit databázi konkrétního bodu za běhu na stejný server, který je hostitelem databáze. Zálohy jsou uložené v objektech BLOB úložiště RA-GRS, které se replikují do [spárovaném datovém centru](../best-practices-availability-paired-regions.md) pro ochranu před výpadku datového centra. Při obnovování databáze služby přijde na to, které úplného, rozdílového a transakce protokolu zálohy je nutné obnovit.

Můžete použít tyto zálohy na:

- Obnovte databázi do doby uchování v daném okamžiku. Tato operace vytvoří novou databázi na stejném serveru jako je původní databáze.
- Obnovení odstraněné databáze na čas, kdy byla odstraněna nebo libovolný čas v rámci doby uchování. Odstraněnou databázi můžete obnovit jen na stejném serveru, kde původní databáze byla vytvořena.
- Obnovení databáze do jiné geografické oblasti. Geografické obnovení umožňuje zotavení po havárii zeměpisné, když nelze přistupovat k serveru a databáze. Vytvoří novou databázi v jakékoli existující server kdekoli v celém světě.
- Obnovení databáze z konkrétní dlouhodobé zálohování v případě databáze má nakonfigurované zásady dlouhodobého uchovávání informací (LTR). Zleva doprava umožňuje obnovit starší verzi databáze, které by vyhovovaly žádosti o dodržování předpisů nebo spuštění starší verze aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).
- Pokud chcete provést obnovení, najdete v článku [obnovit databázi ze zálohy](sql-database-recovery-using-backups.md).

> [!NOTE]
> Ve službě Azure storage termín *replikace* odkazuje na kopírování souborů z jednoho umístění do druhého. SQL *replikace databáze* odkazuje na udržování více sekundární databází, které jsou synchronizovány s primární databáze.

## <a name="how-long-are-backups-kept"></a>Jak dlouho se zálohy uchovávat

Každá databáze SQL má výchozí období uchovávání záloh mezi 7 až 35 dnů, na kterých závisí [nákupní model a úroveň služeb](#pitr-retention-period). Můžete aktualizovat období uchování zálohy pro databázi na logickém serveru Azure. Další informace najdete v tématu [období uchování zálohy změnu](#how-to-change-the-pitr-backup-retention-period).

Když odstraníte databáze, databáze SQL zachovají zálohy stejným způsobem, který by tomu bylo online databáze. Například pokud odstraníte databázi Basic, který má dobu uchování o délce sedm dní, zálohy, která je starší čtyř dní uložená pro další tři dny.

Pokud chcete zachovat zálohy po dobu delší než maximální doba uchovávání, můžete upravit záložní vlastnosti, které chcete přidat jeden nebo více dlouhodobé období uchovávání dat do databáze. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Při odstranění serveru Azure SQL server, který je hostitelem databází SQL, odstraní se také všechny elastických fondů a databází, které patří k serveru a nelze ji obnovit. Nelze obnovit server odstranil. Ale pokud jste nakonfigurovali dlouhodobé uchovávání, zálohování databází s LTR nebudou odstraněny, a dají se obnovit tyto databáze.

### <a name="default-backup-retention-period"></a>Výchozí období uchování zálohy

#### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Výchozí době uchování databáze vytvořené využitím nákupní model založený na DTU, závisí na úrovni služby:

- Úroveň služeb Basic je 1 týden.
- Úrovně služeb Standard je 5 týdnů.
- Úroveň Premium služby je 5 týdnů.

#### <a name="vcore-based-purchasing-model"></a>Model nákupu na základě virtuálních jader

Pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), je výchozí období uchování zálohy 7 dní (pro jeden, ve fondu a spravované instanci databáze). Pro všechny databáze Azure SQL (jednoduché, ve fondu, a databáze Managed Instance můžete [změnit období uchování zálohy 35 dnů](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Pokud aktuální dobu uchovávání snížíte, všechny existující zálohy, které jsou starší než novou míru uchování období se již nebude k dispozici. Když aktuální dobu uchovávání prodloužit, SQL Database zachovají existující zálohy, dokud nebude dosaženo delší doba uchovávání dat.

## <a name="how-often-do-backups-happen"></a>Jak často k dochází zálohy

### <a name="backups-for-point-in-time-restore"></a>Zálohy pro obnovení k určitému bodu v čase

SQL Database podporuje samoobslužné funkce pro obnovení k určitému bodu v čase (PITR) automaticky vytváří úplné zálohy, rozdílové zálohování a zálohování protokolů transakcí. Úplné zálohy databáze se vytvoří každý týden, rozdílovými zálohami prováděnými obecně vytvářejí každých 12 hodin a zálohy protokolu transakce obvykle vytvářejí každých 5 až 10 minut, s frekvencí podle výpočetního prostředí a objem databázové aktivity. Bude první úplná záloha je naplánováno ihned po vytvoření databáze. Obvykle hotové během 30 minut, ale může trvat déle, když je databáze významnou velikostí. Například prvotní zálohování může trvat déle v obnovené databáze nebo kopie databáze. Po dokončení první úplné zálohování všechny další zálohy jsou automaticky naplánované a spravované tiše na pozadí. Služba SQL Database určuje přesné načasování všechny zálohy databáze jako vyrovnává celkové zatížení systému.

Zálohy PITR jsou geograficky redundantní a chráněný [mezi zónami replikace Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Další informace najdete v tématu [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Pro dlouhodobé uchovávání záloh

Hostované v logický Server SQL Database nabízí možnost konfigurace dlouhodobého uchovávání dat (LTR) úplných záloh po dobu až 10 let ve službě Azure blob storage. Pokud je povolené zásady LTR, týdenními úplnými zálohami se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Aby splnila požadavek na dodržení jiný, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Spotřeba úložiště závisí na vybrané četnosti zálohování a období uchovávání. Můžete použít [LTR pomocí cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/?service=sql-database) odhadnout náklady na úložiště zleva doprava.

Podobně jako PITR, zálohy zleva doprava jsou geograficky redundantní a chráněný [replikace Azure Storage mezi zónami](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Další informace najdete v tématu [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Jsou šifrované zálohování

Pokud vaše databáze je zašifrovaný pomocí šifrování TDE, záloh se šifrují automaticky v klidovém stavu, včetně záloh zleva doprava. Pokud pro službu Azure SQL database je povoleno TDE, je také šifrovaný zálohy. Všechny nové databáze Azure SQL jsou konfigurované tak transparentní šifrování dat ve výchozím nastavení povolená. Další informace o transparentní šifrování dat, naleznete v tématu [transparentního šifrování dat s využitím Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Jak Microsoft zajišťují integritu zálohy

Průběžně technický tým Azure SQL Database automaticky ověřuje obnovení automatizovaných záloh databází ve službě. Při obnovení databáze také přijímat kontroly integrity pomocí příkazu DBCC CHECKDB. Žádné problémů zjištěných během kontroly integrity způsobí výstrahy technickému týmu. Další informace o integritě dat ve službě Azure SQL Database najdete v tématu [integritu dat ve službě Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Vliv automatizovaných záloh dodržování předpisů

Při migraci databáze z úrovně služeb na základě DTU se výchozí PITR uchovávání po dobu 35 dní, do vrstvy služeb založený na virtuálních jádrech PITR uchovávání se zachovají zajistit, že není ohrožena zásad pro obnovení dat vaší aplikace. Pokud výchozí uchování nesplňuje vaše požadavky na dodržování předpisů, můžete změnit dobu uchování PITR pomocí Powershellu nebo rozhraní REST API. Zobrazit [období uchování zálohy změnu](#how-to-change-the-pitr-backup-retention-period) další podrobnosti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Jak změnit PITR období uchování zálohy

Můžete změnit období uchování zálohy PITR výchozí pomocí webu Azure Portal, Powershellu nebo rozhraní REST API. Podporované hodnoty jsou: 7, 14, 21, 28 nebo 35 dnů. Následující příklady ukazují, jak změnit PITR uchovávání informací na 28 dnů.

> [!NOTE]
> Rozhraní API tez ovlivní pouze PITR dobu uchování. Pokud jste nakonfigurovali zleva doprava pro vaši databázi, nebude mít vliv. Další informace o tom, jak změnit období uchování zleva doprava, najdete v části [dlouhodobé uchovávání](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Změnit PITR období uchování zálohy pomocí webu Azure portal

Chcete-li změnit PITR období uchovávání záloh pomocí webu Azure portal, přejděte do databáze jehož doba uchování, kterou chcete změnit a pak klikněte na tlačítko **přehled**.

![Portál pro změnu PITR Azure](./media/sql-database-automated-backup/configure-backup-retention.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Změnit PITR období uchování zálohy pomocí Powershellu

```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

> [!IMPORTANT]
> Toto rozhraní API je součástí modulu Powershellu AzureRM.Sql od verze [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview).

### <a name="change-pitr-retention-period-using-rest-api"></a>Změnit dobu uchování PITR pomocí rozhraní REST API

#### <a name="sample-request"></a>Ukázkový požadavek

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Text žádosti

```json
{
  "properties":{  
      "retentionDays":28
   }
}
```

#### <a name="sample-response"></a>Ukázková odezva

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

Další informace najdete v tématu [rozhraní REST API pro uchování zálohy](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Další postup

- Zálohování databáze jsou nedílnou součást každé strategie obchodní kontinuity podnikových procesů a po havárii pro obnovení, protože jejich Chraňte svoje data před náhodným poškozením nebo odstranění. Další informace o jiná řešení kontinuity podnikových procesů Azure SQL Database business najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
- Obnovení do bodu v čase pomocí webu Azure portal, najdete v článku [obnovit databázi do bodu v čase pomocí webu Azure portal](sql-database-recovery-using-backups.md).
- Pokud chcete obnovit do bodu v čase s použitím prostředí PowerShell, naleznete v tématu [obnovit databázi do bodu v čase s použitím prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Můžete konfigurovat, spravovat a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů blob v Azure pomocí webu Azure portal najdete v tématu [správa dlouhodobého uchovávání záloh pomocí webu Azure portal](sql-database-long-term-backup-retention-configure.md).
- Konfigurovat, spravovat a obnovení z dlouhodobě uchovávaných z automatických záloh na blogu Azure storage pomocí Powershellu, přečtěte si téma [správa dlouhodobého uchovávání záloh pomocí Powershellu](sql-database-long-term-backup-retention-configure.md).
