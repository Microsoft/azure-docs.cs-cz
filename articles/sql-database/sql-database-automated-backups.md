---
title: Automatické, geograficky redundantní zálohy
description: SQL Database automaticky vytvoří zálohu místní databáze každých pár minut a používá azure pro čtení geograficky redundantní úložiště pro geografickou redundanci.
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
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061765"
---
# <a name="automated-backups"></a>Automatizované zálohy

Azure SQL Database automaticky vytvoří zálohy databáze, které jsou uchovávány po dobu trvání nakonfigurované doby uchování. Používá [azure přístup pro čtení geograficky redundantní úložiště (RA-GRS)](../storage/common/storage-redundancy.md) k zajištění zálohy jsou zachovány i v případě, že datové centrum není k dispozici.

Zálohy databází jsou nezbytnou součástí každé strategie kontinuity podnikání a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Pokud vaše pravidla zabezpečení vyžadují, aby vaše zálohy byly k dispozici delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](sql-database-long-term-retention.md) v databázích singleton a elastických databázových fondech.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je záloha databáze SQL?

SQL Database používá technologii SQL Server k vytvoření [úplné zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) každý týden, [rozdílové zálohy každých](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12 hodin a [zálohy transakční protokol každých](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 5 až 10 minut. Zálohy jsou uloženy v [objektech BLOB úložiště RA-GRS,](../storage/common/storage-redundancy.md) které jsou replikovány do [spárovaného datového centra](../best-practices-availability-paired-regions.md) pro ochranu proti výpadku datového centra. Při obnovení databáze služba určuje, které úplné, rozdílové a transakční protokol zálohy je třeba obnovit.

Tyto zálohy rovněž umožňují:

- **Obnovení existující databáze do bodu v čase v minulosti** v rámci období uchovávání pomocí webu Azure Portal, Azure PowerShell, Azure CLI nebo rozhraní REST API. Pro jednotlivé databáze a elastické databázové fondy tato operace vytvoří novou databázi na stejném serveru jako původní databáze. Ve spravované instanci může tato operace vytvořit kopii databáze nebo stejnou nebo jinou spravovanou instanci v rámci stejného předplatného.
- **Obnovit odstraněnou databázi na čas odstranění** nebo kdykoli v rámci doby uchování. Odstraněnou databázi lze obnovit pouze na stejném logickém serveru nebo spravované instanci, kde byla vytvořena původní databáze.
- **Obnovení databáze do jiné geografické oblasti**. Geografické obnovení umožňuje zotavit se z geografické katastrofy, když nemáte přístup k serveru a databázi. Vytvoří novou databázi na libovolném existujícím serveru, kdekoli na světě.
- **Obnovení databáze z konkrétní dlouhodobé zálohy** v jedné databázi nebo elastickém databázovém fondu, pokud je databáze nakonfigurována s dlouhodobou zásadou uchovávání informací (LTR). LTR umožňuje obnovit starou verzi databáze pomocí [portálu Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) nebo [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) u splnění požadavku na dodržování předpisů nebo ke spuštění staré verze aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

Obnovení najdete v tématu [Obnovení databáze ze záloh](sql-database-recovery-using-backups.md).

> [!NOTE]
> Ve službě Azure Storage termín *replikace* odkazuje na kopírování souborů z jednoho umístění do druhého. *Replikace databáze* serveru SQL Server označuje synchronizaci více sekundárních databází s primární databází.

Některé z těchto operací můžete vyzkoušet pomocí následujících příkladů:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Změna uchovávání záloh | [Izolovaná databáze](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Spravovaná instance](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Izolovaná databáze](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Změna dlouhodobého uchovávání záloh | [Izolovaná databáze](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Spravovaná instance – není k můe  | [Izolovaná databáze](sql-database-long-term-backup-retention-configure.md)<br/>Spravovaná instance – není k můe  |
| Obnovení databáze z bodu v čase | [Izolovaná databáze](sql-database-recovery-using-backups.md#point-in-time-restore) | [Izolovaná databáze](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Obnovení odstraněné databáze | [Izolovaná databáze](sql-database-recovery-using-backups.md) | [Izolovaná databáze](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Obnovení databáze z úložiště objektů blob Azure | Jednotná databáze - Není k ono <br/>Spravovaná instance – není k můe  | Jednotná databáze - Není k ono <br/>[Spravovaná instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frekvence zálohování

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

SQL Database podporuje samoobslužné pro obnovení bodu v čase (PITR) automatickým vytvářením úplných záloh, rozdílových záloh a záloh protokolu transakcí. Úplné zálohy databáze jsou vytvářeny týdně a rozdílové zálohy databáze jsou obvykle vytvářeny každých 12 hodin. Zálohy transakční protokol jsou obvykle vytvářeny každých 5 až 10 minut. Frekvence záloh protokolu transakcí je založena na velikosti výpočetních prostředků a množství aktivity databáze. 

První úplné zálohování je naplánováno ihned po vytvoření databáze. Tato záloha obvykle dokončí do 30 minut, ale může trvat déle, pokud je databáze velká. Počáteční záloha může například trvat déle v obnovené databázi nebo v kopii databáze. Po první úplné záloze se všechny další zálohy naplánují automaticky a budou se bezobslužně spravovat na pozadí. Přesné načasování všech záloh databáze určuje služba SQL Database, protože musí vyrovnat celkové zatížení systému. Úlohy zálohování nemůžete změnit ani zakázat.

Zálohy PITR jsou chráněny geograficky redundantním úložištěm. Další informace najdete v článku [Možnosti redundance Azure Storage](../storage/common/storage-redundancy.md).

Další informace o PITR naleznete [v tématu Point-in-time restore](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Dlouhodobé uchovávání

Pro jednu a sdruženou databázi můžete nakonfigurovat dlouhodobé uchovávání (LTR) úplné zálohy až na 10 let v úložišti objektů Blob Azure. Pokud povolíte zásady LTR, týdenní úplné zálohy se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Chcete-li splnit různé požadavky na dodržování předpisů, můžete vybrat různé doby uchovávání pro týdenní, měsíční nebo roční zálohy. Spotřeba úložiště závisí na zvolené frekvenci zálohování a dobu uchování nebo období. Cenovou [kalkulačku LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) můžete použít k odhadu nákladů na úložiště LTR.

Stejně jako zálohy PITR jsou zálohy LTR chráněny geograficky redundantním úložištěm. Další informace najdete v článku [Možnosti redundance Azure Storage](../storage/common/storage-redundancy.md).

Další informace o LTR naleznete [v tématu Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Spotřeba úložiště záloh

Pro jednotlivé databáze se tato rovnice používá k výpočtu celkového využití úložiště záloh:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Pro fondy elastických databází se celková velikost úložiště záloh agreguje na úrovni fondu a vypočítá se takto:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Zálohy, ke kterým dochází před retenční období jsou automaticky vymazány na základě jejich časové razítko. Vzhledem k tomu, že rozdílové zálohy a zálohprotokolu vyžadují dřívější úplné zálohování, které mají být užitečné, jsou vymazány společně v týdenní bloky dat.

Azure SQL Database vypočítá vaše celkové úložiště záloh jako kumulativní hodnotu. Každou hodinu se tato hodnota hlásí do fakturačního kanálu Azure, který je zodpovědný za agregaci tohoto hodinového využití k výpočtu spotřeby na konci každého měsíce. Po vynechání databáze se spotřeba snižuje s věkem záloh. Po zálohování starší než období uchování se fakturace zastaví.

   > [!IMPORTANT]
   > Zálohy databáze jsou zachovány po zadanou dobu uchování, i v případě, že databáze byla vynechána. Zatímco přetažení a opětovné vytvoření databáze může často ušetřit na nákladech na úložiště a výpočetní náklady, může to zvýšit náklady na úložiště záloh, protože Microsoft uchovává zálohu po stanovenou dobu uchování (což je minimálně 7 dní) pro každou vypuštěnou databázi, každou čas, kdy je to klesl.

### <a name="monitor-consumption"></a>Sledování spotřeby

Každý typ zálohy (úplné, rozdílové a protokolu) je hlášena na okno monitorování databáze jako samostatná metrika. Následující diagram ukazuje, jak sledovat spotřebu úložiště záloh pro jednu databázi. Tato funkce je momentálně nedostupná pro spravované instance.

![Monitorování spotřeby záloh databáze na webu Azure Portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Jemné doladění spotřeby úložiště záloh

Přebytek spotřeba úložiště záloh bude záviset na zatížení a velikost jednotlivých databází. Zvažte některé z následujících technik ladění, abyste snížili spotřebu úložiště záloh:

* Zkraťte [dobu uchovávání záloh](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) na minimum, které je vašim potřebám možné.
* Vyhněte se provádět velké operace zápisu, jako je obnovení indexu, častěji, než je nutné.
* U operací načítání velkých objemů dat zvažte použití [clusterovaných indexů columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snižte počet neseskupených indexů a zvažte operace hromadného zatížení s počtem řádků přibližně 1 milion.
* Ve vrstvě služby pro obecné účely je zřízené úložiště dat levnější než cena úložiště nadměrnézálohy. Pokud máte neustále vysoké náklady na úložiště záloh, můžete zvážit zvýšení úložiště dat ušetřit na úložiště záloh.
* Místo trvalých tabulek v logice ETL použijte funkce TempDB pro ukládání dočasných výsledků. (Platí pouze pro spravovanou instanci.)
* Zvažte vypnutí šifrování TDE pro databáze, které neobsahují citlivá data (vývoj nebo testování databází, například). Zálohy pro nešifrované databáze jsou obvykle komprimovány s vyšším kompresním poměrem.

> [!IMPORTANT]
> Pro analytické data mart \ úlohy datového skladu důrazně doporučujeme použít [clusterované indexy columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snížit počet indexů bez clusteru a zvážit operace hromadného zatížení s počtem řádků přibližně 1 milion, aby se snížila nadměrná spotřeba úložiště záloh.

## <a name="storage-costs"></a>Cena za uložení

Cena za úložiště se liší v závislosti na tom, zda používáte model DTU nebo model virtuálních jader.

### <a name="dtu-model"></a>Model DTU

Pokud používáte model DTU, neplatí se za úložiště záloh pro databáze a elastické databázové fondy žádné další poplatky.

### <a name="vcore-model"></a>Model virtuálních jader

Pro jednotlivé databáze je bez příplatku poskytována minimální velikost úložiště záloh rovnající se 100 procentům velikosti databáze. Pro fondy elastických databází a spravované instance je bez příplatku k dispozici minimální velikost úložiště záloh rovnající se 100 procentům přiděleného úložiště dat pro fond nebo velikost instance. Další využití úložiště zálohování se bude účtovat v GB/měsíc. Tato dodatečná spotřeba bude záviset na zatížení a velikosti jednotlivých databází.

Azure SQL Database vypočítá vaše celkové úložiště záloh jako kumulativní hodnotu. Každou hodinu se tato hodnota hlásí do fakturačního kanálu Azure, který je zodpovědný za agregaci tohoto hodinového využití, aby získal vaši spotřebu na konci každého měsíce. Po vynechání databáze, Microsoft snižuje spotřebu jako stáří záloh. Po zálohování starší než období uchování se fakturace zastaví. Vzhledem k tomu, že všechny zálohy protokolu a rozdílové zálohy jsou zachovány po dobu uchovávání, silně ovlivněné databáze budou mít vyšší poplatky za zálohování.

Předpokládejme, že databáze nashromáždila 744 GB úložiště záloh a že tato částka zůstane konstantní po celý měsíc. Chcete-li převést tuto kumulativní spotřebu úložiště na hodinovou spotřebu, vydělte ji 744,0 (31 dní v měsíci * 24 hodin denně). Takže SQL Database bude hlásit, že databáze spotřebovává 1 GB zálohování PITR každou hodinu. Azure fakturace bude agregovat tuto spotřebu a zobrazit využití 744 GB za celý měsíc. Náklady budou založeny na sazbě $/GB/měsíc ve vaší oblasti.

Nyní složitější příklad. Předpokládejme, že databáze má jeho uchovávání zvýšena na 14 dní v polovině měsíce. Předpokládejme, že toto zvýšení (hypoteticky) má za následek celkové zdvojnásobení úložiště záloh na 1 488 GB. SQL Database by zpráva 1 GB využití pro hodiny 1 až 372. To by hlásit využití jako 2 GB pro hodiny 373 až 744. Toto využití by bylo agregováno do konečného vyúčtování 1 116 GB/měsíc.

### <a name="monitor-costs"></a>Sledování nákladů

Pokud chcete porozumět nákladům na úložiště záloh, přejděte na **Cost Management + Billing** na webu Azure Portal, vyberte Správa **nákladů**a pak vyberte **Analýza nákladů**. Vyberte požadované předplatné jako **obor**a potom filtrujte časové období a službu, která vás zajímá.

Přidejte filtr pro **název služby**a v rozevíracím seznamu vyberte **databázi SQL.** Pomocí filtru **podkategorie měřiče** zvolte fakturační čítač pro vaši službu. Pro jednu databázi nebo elastický databázový fond vyberte **úložiště zálohování single/elastic pool pitr**backup . Pro spravovanou instanci vyberte **úložiště zálohování mi pitr**. Podkategorie **Úložiště** a **výpočetní prostředky** vás také mohou zajímat, ale nejsou spojeny s náklady na úložiště záloh.

![Analýza nákladů na úložiště zálohování](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Uchování záloh

Všechny databáze Azure SQL (databáze s jedním, sdružených a spravovaných instancí) mají výchozí dobu uchovávání záloh 7 dní. Dobu [uchování zálohy](#change-the-pitr-backup-retention-period) můžete změnit na 35 dní.

Pokud odstraníte databázi, SQL Database bude udržovat zálohy stejným způsobem jako pro online databáze. Pokud například odstraníte základní databázi, která má dobu uchování sedm dní, záloha, která je stará čtyři dny je uložena po dobu dalších tří dnů.

Pokud potřebujete zachovat zálohy déle než maximální dobu uchování, můžete upravit vlastnosti zálohování a přidat do databáze jednu nebo více dlouhodobých období uchovávání. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Pokud odstraníte server Azure SQL, který je hostitelem databází SQL, odstraní se také všechny fondy elastických databází a databáze, které patří k serveru. Nedají se je získat. Odstraněný server nelze obnovit. Ale pokud jste nakonfigurovali dlouhodobé uchovávání, zálohy pro databáze s LTR nebudou odstraněny a tyto databáze lze obnovit.

## <a name="encrypted-backups"></a>Šifrované zálohy

Pokud je databáze šifrována pomocí TDE, zálohy jsou automaticky šifrovány v klidovém stavu, včetně záloh LTR. Když je TDE povolená pro databázi Azure SQL, zálohy jsou také šifrované. Všechny nové databáze Azure SQL jsou nakonfigurované s TDE povoleno ve výchozím nastavení. Další informace o TDE najdete v [tématu Transparentní šifrování dat pomocí Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrita zálohování

Inženýrský tým Azure SQL Database průběžně testuje obnovení automatického databázového zálohování databází umístěných na logických serverech a elastických databázových fondech. (Toto testování není k dispozici ve spravované instanci.) Při obnovení v čase v čase databáze také přijímat kontroly integrity DBCC CHECKDB.

Spravovaná instance `CHECKSUM` provádí automatické počáteční zálohování `RESTORE` s databázemi obnovenými pomocí nativního příkazu nebo se službou Migrace dat Azure po dokončení migrace.

Jakékoli problémy zjištěné během kontroly integrity budou mít za následek upozornění pro technický tým. Další informace najdete [v tématu Integrity dat v Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Dodržování předpisů

Když migrujete databázi z úrovně služeb založených na DTU na úroveň služeb založených na virtuálních jádrech, zachování PITR je zachováno, aby se zajistilo, že nebudou ohroženy zásady obnovení dat vaší aplikace. Pokud výchozí uchovávání informací nesplňuje vaše požadavky na dodržování předpisů, můžete změnit dobu uchování PITR pomocí prostředí PowerShell nebo rozhraní REST API. Další informace naleznete [v tématu Změna doby uchování zálohování PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Změna doby uchovávání záloh PITR

Výchozí dobu uchovávání záloh PITR můžete změnit pomocí portálu Azure, Prostředí PowerShell nebo rozhraní REST API. Následující příklady ilustrují, jak změnit uchovávání PITR na 28 dní.

> [!WARNING]
> Pokud zkrátíte aktuální dobu uchovávání, nebudou již k dispozici všechny existující zálohy, které jsou starší než nové období uchovávání. Pokud zvýšíte aktuální dobu uchovávání, SQL Database bude udržovat existující zálohy až do konce delší dobu uchování je dosaženo.

> [!NOTE]
> Tato api ovlivní pouze dobu uchovávání PITR. Pokud jste nakonfigurovali LTR pro databázi, nebude to ovlivněno. Informace o tom, jak změnit dobu uchovávání LTR, naleznete [v tématu Dlouhodobé uchovávání](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Změna doby uchovávání záloh PITR pomocí webu Azure Portal

Pokud chcete změnit dobu uchovávání záloh PITR pomocí portálu Azure, přejděte na serverový objekt, jehož dobu uchování chcete na portálu změnit. Pak vyberte příslušnou možnost na základě objektu serveru, který upravujete.

#### <a name="single-database-and-elastic-database-pools"></a>[Jednotné databáze a elastické databázové fondy](#tab/single-database)

Změny uchovávání záloh PITR pro jednotlivé databáze Azure SQL se provádějí na úrovni serveru. Změny provedené na úrovni serveru platí pro databáze na serveru. Pokud chcete změnit uchovávání PITR pro server Azure SQL Database z webu Azure Portal, přejděte na okno přehledu serveru. V levém podokně vyberte **Spravovat zálohování** a v horní části obrazovky vyberte Konfigurovat **uchovávání informací:**

![Změna uchovávání PITR, úroveň serveru](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Změny uchovávání záloh PITR pro instance spravované databází SQL database se provádějí na úrovni jednotlivých databází. Pokud chcete změnit uchovávání záloh PITR pro databázi instancí z portálu Azure, přejděte na okno přehledu jednotlivých databází. Pak v horní části obrazovky vyberte **Konfigurovat uchovávání záloh:**

![Změna uchovávání PITR, spravované instance](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Změna doby uchovávání záloh PITR pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell AzureRM je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Další informace naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az jsou v podstatě totožné s argumenty v modulech AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Změna doby uchovávání záloh PITR pomocí rozhraní REST API

#### <a name="sample-request"></a>Požadavek na ukázku

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Text požadavku

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Ukázková odpověď

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

Další informace naleznete v [tématu Backup Retention REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Další kroky

- Zálohy databází jsou nezbytnou součástí každé strategie kontinuity podnikání a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Další informace o dalších řešeních kontinuity podnikání Azure SQL Database najdete v [tématu Přehled kontinuity podnikání](sql-database-business-continuity.md).
- Další informace o tom, jak [obnovit databázi do bodu v čase pomocí portálu Azure](sql-database-recovery-using-backups.md).
- Další informace o [tom, jak obnovit databázi do bodu v čase pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovovat dlouhodobé uchovávání automatizovaných záloh v úložišti objektů Blob Azure pomocí webu Azure Portal, najdete v [tématu Správa dlouhodobého uchovávání záloh pomocí portálu Azure](sql-database-long-term-backup-retention-configure.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovovat dlouhodobé uchovávání automatizovaných záloh v úložišti objektů Blob Azure pomocí PowerShellu, najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí PowerShellu](sql-database-long-term-backup-retention-configure.md).
