---
title: Azure SQL Database automatické, geograficky redundantní zálohy | Microsoft Docs
description: SQL Database automaticky vytvoří zálohování místní databáze každých několik minut a používá geograficky redundantní úložiště s přístupem pro čtení z Azure pro geografickou redundanci.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: a43783110f625dd5faef13c83228a2659155ead0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492234"
---
# <a name="automated-backups"></a>Automatizované zálohy

SQL Database automaticky vytvoří zálohy databáze udržované mezi 7 a 35 dny a pomocí [geograficky redundantního úložiště Azure s přístupem pro čtení (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) zajistí, že jsou zachovány i v případě, že datové centrum není k dispozici. Tyto zálohy jsou vytvořeny automaticky. Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Pokud vaše pravidla zabezpečení vyžadují, aby byly zálohy dostupné po delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](sql-database-long-term-retention.md) pro databáze typu Singleton a elastické fondy.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je zálohování SQL Database

SQL Database používá technologii SQL Server k vytváření [úplných záloh](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) každý týden, [rozdílové zálohování](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) každých 12 hodin a [zálohování protokolů transakcí](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) každých 5-10 minut. Zálohy se ukládají v objektech [BLOB úložiště RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) , které se replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md) kvůli ochraně před výpadkem datového centra. Při obnovení databáze vyřadí služba vyčíslení úplného, rozdílového a zálohy protokolu transakcí, které je třeba obnovit.

Tyto zálohy můžete použít k těmto akcím:

- **Obnovte stávající databázi k určitému bodu v čase v minulosti** v rámci doby uchování pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API. V izolovaných databázích a elastických fondech Tato operace vytvoří novou databázi na stejném serveru jako původní databázi. V rámci spravované instance Tato operace může vytvořit kopii databáze nebo stejné nebo jiné spravované instance v rámci stejného předplatného.
  - **[Změňte dobu uchování zálohy](#how-to-change-the-pitr-backup-retention-period)** mezi 7 až 35 dny a nakonfigurujte zásady zálohování.
  - **Změňte dlouhodobé zásady uchovávání** na izolovaná databáze a elastické fondy po dobu až 10 let pomocí [Azure Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) nebo [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Obnovení odstraněné databáze na čas, kdy byla odstraněna** nebo kdykoli v rámci doby uchování. Odstraněnou databázi lze obnovit pouze na stejném logickém serveru nebo ve spravované instanci, kde byla vytvořena původní databáze.
- **Obnovte databázi do jiné geografické oblasti**. Geografické obnovení umožňuje obnovení z geografické havárie, když nemůžete získat přístup k serveru a databázi. Vytvoří novou databázi na jakémkoli existujícím serveru kdekoli na světě.
- **Obnovte databázi z určité dlouhodobé zálohy** na Izolovaná databáze nebo elastický fond, pokud byla databáze nakonfigurovaná s použitím dlouhodobých zásad uchovávání informací (LTR). LTR umožňuje obnovit starou verzi databáze pomocí [Azure Portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) nebo [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) , aby splňovala požadavek na dodržování předpisů nebo spustila starou verzi aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).
- Chcete-li provést obnovení, přečtěte si téma [obnovení databáze ze zálohy](sql-database-recovery-using-backups.md).

> [!NOTE]
> Termín *replikace* ve službě Azure Storage označuje kopírování souborů z jednoho umístění do druhého. *Replikace databáze* SQL odkazuje na udržování více sekundárních databází synchronizovaných s primární databází.

Některé z těchto operací můžete vyzkoušet v následujících příkladech:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Změna uchovávání záloh | [Izolovaná databáze](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-azure-portal) <br/> [Spravovaná instance](sql-database-automated-backups.md#managed-instance-database) | [Izolovaná databáze](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Změna dlouhodobého uchovávání záloh | [Samostatná databáze](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Spravovaná instance – není k dispozici  | [Izolovaná databáze](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Spravovaná instance – není k dispozici  |
| Obnovit databázi z bodu v čase | [Samostatná databáze](sql-database-recovery-using-backups.md#point-in-time-restore) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Obnovení odstraněné databáze | [Samostatná databáze](sql-database-recovery-using-backups.md) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Obnovení databáze z Azure Blob Storage | Izolovaná databáze – není k dispozici <br/>Spravovaná instance – není k dispozici  | Izolovaná databáze – není k dispozici <br/>[Spravovaná instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Jak dlouho jsou zálohy uchovávány

Všechny databáze Azure SQL (jedna, sdružená a databáze spravované instance) mají výchozí dobu uchovávání záloh po dobu **sedmi** dnů. [Dobu uchování zálohy můžete změnit až na 35 dnů](#how-to-change-the-pitr-backup-retention-period).

Pokud databázi odstraníte, SQL Database zachová zálohy stejným způsobem jako u online databáze. Pokud například odstraníte databázi Basic, která má dobu uchovávání 7 dní, záloha, která je starší než 4 dny, se uloží na tři dny.

Pokud potřebujete uchovat zálohy po dobu delší, než je maximální doba uchovávání, můžete upravit vlastnosti zálohy a přidat jednu nebo více dlouhodobých dob uchovávání do databáze. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Pokud odstraníte server SQL Azure hostující databáze SQL, odstraní se také všechny elastické fondy a databáze patřící do serveru a nelze je obnovit. Odstraněný Server nelze obnovit. Pokud jste ale nakonfigurovali dlouhodobé uchovávání, zálohy pro databáze s LTR nebudou odstraněny a tyto databáze je možné obnovit.

## <a name="how-often-do-backups-happen"></a>Jak často dochází k zálohování

### <a name="backups-for-point-in-time-restore"></a>Zálohy pro obnovení k bodu v čase

SQL Database podporuje samoobslužné obnovení (PITR) pomocí automatického vytváření úplných záloh, rozdílových záloh a záloh protokolů transakcí. Úplné zálohy databáze jsou vytvářeny týdně, rozdílové zálohy databáze jsou obvykle vytvářeny každých 12 hodin a zálohy protokolu transakcí jsou obvykle vytvářeny každých 5-10 minut, přičemž četnost je založena na výpočetní velikosti a množství aktivity databáze. První úplné zálohování je naplánováno ihned po vytvoření databáze. Obvykle se dokončí do 30 minut, ale může trvat déle, než databáze bude mít značnou velikost. Například počáteční záloha může trvat déle na obnovenou databázi nebo kopii databáze. Po prvním úplném zálohování se všechna další zálohování naplánují automaticky a budou spravovaná na pozadí. Přesné časování všech záloh databáze určuje služba SQL Database, protože vyrovnává celkovou úlohu systému. Úlohy zálohování nemůžete změnit ani zakázat. 

Zálohy PITR jsou geograficky redundantní a chráněné [Azure Storage replikace mezi různými oblastmi](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) .

Další informace najdete v tématu [obnovení k bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) .

### <a name="backups-for-long-term-retention"></a>Zálohy pro dlouhodobou dobu uchovávání

Databáze typu Single a Pool nabízí možnost konfigurace dlouhodobého uchovávání (LTR) úplných záloh po dobu až 10 let v úložišti objektů BLOB v Azure. Pokud je povolená zásada LTR, týdenní úplné zálohy se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Pokud chcete splnit jiný požadavek na dodržování předpisů, můžete pro týdenní, měsíční nebo roční zálohy vybrat jinou dobu uchování. Spotřeba úložiště závisí na zvolené četnosti zálohování a na dobu uchování (e). Pomocí [cenové kalkulačky ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) můžete odhadnout náklady na úložiště ltr.

Podobně jako PITR, zálohy LTR jsou geograficky redundantní a chráněné [Azure Storage replikace mezi různými oblastmi](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Další informace najdete v tématu [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Náklady na úložiště
U izolovaných databází a spravovaných instancí se minimální velikost záložního úložiště rovná 100% velikosti databáze poskytuje bez dalších poplatků. V případě elastických fondů se minimální hodnota úložiště zálohy rovná 100% přiděleného úložiště dat pro fond, a to bez dalších poplatků. Využití úložiště zálohování nad tuto mez bude zpoplatněno v jednotkách GB/měsíc. Tato další spotřeba bude záviset na zatížení a velikosti jednotlivých databází.

Další informace o cenách za úložiště najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/sql-database/single/) . 

## <a name="are-backups-encrypted"></a>Jsou zálohy zašifrované

Pokud je vaše databáze zašifrovaná pomocí TDE, zálohy se automaticky zašifrují v klidovém stavu, včetně záloh LTR. Když je u databáze SQL Azure povolené TDE, zálohují se taky zálohy. Ve výchozím nastavení jsou všechny nové databáze SQL Azure nakonfigurované s povoleným TDE. Další informace o TDE naleznete v tématu [transparentní šifrování dat with Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Jak Microsoft zajišťuje integritu zálohování

V nepřetržitém případě Azure SQL Database technický tým automaticky testuje obnovení automatizovaných záloh databáze databází umístěných na logických serverech a elastických fondech (není k dispozici ve spravované instanci). Při obnovení k bodu v čase získávají databáze také kontroly integrity pomocí příkazu DBCC CHECKDB.

Spravovaná instance provádí automatické počáteční zálohování s `CHECKSUM` databází obnovených pomocí nativního příkazu `RESTORE` nebo služby migrace dat po dokončení migrace.

Všechny problémy zjištěné během kontroly integrity budou mít za následek upozornění technickému týmu. Další informace o integritě dat v Azure SQL Database najdete v tématu [Integrita dat v Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Jak automatizované zálohování ovlivňuje dodržování předpisů

Při migraci databáze z úrovně služby založené na DTU s výchozím PITR uchování 35 dnů do úrovně služby založené na vCore se uchování PITR zachová, aby se zajistilo ohrožení zásad obnovení dat vaší aplikace. Pokud výchozí doba uchovávání nesplňuje požadavky na dodržování předpisů, můžete změnit dobu uchování PITR pomocí PowerShellu nebo REST API. Další informace najdete v tématu [Změna doby uchování zálohy](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Jak změnit dobu uchovávání záloh PITR

Výchozí dobu uchovávání záloh PITR můžete změnit pomocí Azure Portal, PowerShellu nebo REST API. Podporované hodnoty jsou: 7, 14, 21, 28 nebo 35 dnů. Následující příklady ukazují, jak změnit PITR uchování na 28 dní.

> [!WARNING]
> Pokud zmenšíte aktuální dobu uchovávání, nebudou už všechny existující zálohy starší než nová doba uchování k dispozici. Pokud zvýšíte aktuální dobu uchovávání, SQL Database zachová stávající zálohy, dokud nedosáhnete delší doby uchovávání.

> [!NOTE]
> Tato rozhraní API budou mít vliv jenom na dobu uchovávání PITR. Pokud jste nakonfigurovali LTR pro vaši databázi, nebude to mít vliv na. Další informace o tom, jak změnit dobu uchování LTR, najdete v tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Změna doby uchovávání záloh PITR pomocí Azure Portal

Pokud chcete změnit dobu uchovávání záloh PITR pomocí Azure Portal, přejděte na objekt serveru, jehož doba uchovávání dat chcete změnit na portálu, a pak vyberte vhodnou možnost podle toho, který objekt serveru upravujete.

#### <a name="single-azure-sql-database"></a>Jeden Azure SQL Database

Změna uchovávání PITR zálohování pro jednu databázi Azure SQL se provádí na úrovni serveru. Změny provedené na úrovni serveru se vztahují na databáze na tomto serveru. Chcete-li změnit PITR pro Azure SQL Database Server z Azure Portal, přejděte na okno Přehled serveru, klikněte na možnost spravovat zálohy v navigační nabídce a pak na navigačním panelu klikněte na možnost konfigurace uchovávání.

![Změnit Azure Portal PITR](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance-database"></a>Databáze spravované instance

Změna uchovávání záloh PITR pro SQL Database spravovanou instanci se provádí na úrovni jednotlivých databází. Chcete-li změnit uchovávání záloh PITR pro databázi instance z Azure Portal, přejděte do okna Přehled individuální databáze a pak klikněte na možnost konfigurace uchovávání záloh na navigačním panelu.

![Změnit Azure Portal PITR](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Změna doby uchovávání záloh PITR pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Změna doby uchování PITR pomocí REST API

#### <a name="sample-request"></a>Ukázková žádost

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

Další informace najdete v tématu [REST API uchovávání záloh](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Další kroky

- Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Další informace o dalších Azure SQL Database řešení pro provozní kontinuitu najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
- Obnovení k určitému bodu v čase pomocí Azure Portal najdete v tématu [obnovení databáze k určitému bodu v čase pomocí Azure Portal](sql-database-recovery-using-backups.md).
- Postup obnovení k určitému bodu v čase pomocí prostředí PowerShell najdete v tématu [obnovení databáze k určitému bodu v čase pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurace, Správa a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí Azure Portal najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Informace o konfiguraci, správě a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí PowerShellu najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí PowerShellu](sql-database-long-term-backup-retention-configure.md).
