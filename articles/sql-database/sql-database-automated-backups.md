---
title: Automatické, geograficky redundantní zálohy
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
ms.date: 12/13/2019
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461764"
---
# <a name="automated-backups"></a>Automatizované zálohy

SQL Database automaticky vytvoří zálohy databáze uchovávané po dobu trvání nakonfigurované doby uchování a pomocí [geograficky redundantního úložiště Azure s přístupem pro čtení (RA-GRS)](../storage/common/storage-redundancy.md) zajistí, že jsou zachovány i v případě, že datové centrum není k dispozici. Tyto zálohy jsou vytvořeny automaticky. Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Pokud vaše pravidla zabezpečení vyžadují, aby byly zálohy dostupné po delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](sql-database-long-term-retention.md) pro databáze typu Singleton a elastické fondy.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je zálohování SQL Database

SQL Database používá technologii SQL Server k vytváření [úplných záloh](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) každý týden, [rozdílové zálohování](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) každých 12 hodin a [zálohování protokolů transakcí](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) každých 5-10 minut. Zálohy se ukládají v objektech [BLOB úložiště RA-GRS](../storage/common/storage-redundancy.md) , které se replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md) kvůli ochraně před výpadkem datového centra. Při obnovení databáze vyřadí služba vyčíslení úplného, rozdílového a zálohy protokolu transakcí, které je třeba obnovit.

Tyto zálohy můžete použít k těmto akcím:

- **Obnovte stávající databázi k určitému bodu v čase v minulosti** v rámci doby uchování pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API. V izolovaných databázích a elastických fondech Tato operace vytvoří novou databázi na stejném serveru jako původní databázi. V rámci spravované instance Tato operace může vytvořit kopii databáze nebo stejné nebo jiné spravované instance v rámci stejného předplatného.
- **Obnovení odstraněné databáze na čas, kdy byla odstraněna** nebo kdykoli v rámci doby uchování. Odstraněnou databázi lze obnovit pouze na stejném logickém serveru nebo ve spravované instanci, kde byla vytvořena původní databáze.
- **Obnovte databázi do jiné geografické oblasti**. Geografické obnovení umožňuje obnovení z geografické havárie, když nemůžete získat přístup k serveru a databázi. Vytvoří novou databázi na jakémkoli existujícím serveru kdekoli na světě.
- **Obnovte databázi z určité dlouhodobé zálohy** na Izolovaná databáze nebo elastický fond, pokud byla databáze nakonfigurovaná s použitím dlouhodobých zásad uchovávání informací (LTR). LTR umožňuje obnovit starou verzi databáze pomocí [Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) nebo [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) , aby splňovala požadavek na dodržování předpisů nebo spustila starou verzi aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).
- Chcete-li provést obnovení, přečtěte si téma [obnovení databáze ze zálohy](sql-database-recovery-using-backups.md).

> [!NOTE]
> Termín *replikace* ve službě Azure Storage označuje kopírování souborů z jednoho umístění do druhého. *Replikace databáze* SQL odkazuje na udržování více sekundárních databází synchronizovaných s primární databází.

Některé z těchto operací můžete vyzkoušet v následujících příkladech:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Změna uchovávání záloh | [Izolovaná databáze](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Spravovaná instance](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Izolovaná databáze](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Změna dlouhodobého uchovávání záloh | [Samostatná databáze](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Spravovaná instance – není k dispozici  | [Izolovaná databáze](sql-database-long-term-backup-retention-configure.md)<br/>Spravovaná instance – není k dispozici  |
| Obnovit databázi z bodu v čase | [Samostatná databáze](sql-database-recovery-using-backups.md#point-in-time-restore) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Obnovení odstraněné databáze | [Samostatná databáze](sql-database-recovery-using-backups.md) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Obnovení databáze z Azure Blob Storage | Izolovaná databáze – není k dispozici <br/>Spravovaná instance – není k dispozici  | Izolovaná databáze – není k dispozici <br/>[Spravovaná instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Četnost zálohování

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

SQL Database podporuje samoobslužné obnovení (PITR) pomocí automatického vytváření úplných záloh, rozdílových záloh a záloh protokolů transakcí. Úplné zálohy databáze jsou vytvářeny týdně, rozdílové zálohy databáze jsou obvykle vytvářeny každých 12 hodin a zálohy protokolu transakcí jsou obvykle vytvářeny každých 5-10 minut, přičemž četnost je založena na výpočetní velikosti a množství aktivity databáze. První úplné zálohování je naplánováno ihned po vytvoření databáze. Obvykle se dokončí do 30 minut, ale může trvat déle, než databáze bude mít značnou velikost. Například počáteční záloha může trvat déle na obnovenou databázi nebo kopii databáze. Po prvním úplném zálohování se všechna další zálohování naplánují automaticky a budou spravovaná na pozadí. Přesné časování všech záloh databáze určuje služba SQL Database, protože vyrovnává celkovou úlohu systému. Úlohy zálohování nemůžete změnit ani zakázat.

Zálohy PITR jsou chráněné pomocí geograficky redundantního úložiště. Další informace najdete v tématu [Azure Storage redundance](../storage/common/storage-redundancy.md).

Další informace najdete v tématu [obnovení k bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) .

### <a name="long-term-retention"></a>Dlouhodobé uchovávání

Databáze typu Single a Pool nabízí možnost konfigurace dlouhodobého uchovávání (LTR) úplných záloh po dobu až 10 let v úložišti objektů BLOB v Azure. Pokud je povolená zásada LTR, týdenní úplné zálohy se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Pokud chcete splnit jiný požadavek na dodržování předpisů, můžete pro týdenní, měsíční nebo roční zálohy vybrat jinou dobu uchování. Spotřeba úložiště závisí na zvolené četnosti zálohování a na dobu uchování (e). Pomocí [cenové kalkulačky ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) můžete odhadnout náklady na úložiště ltr.

Podobně jako PITR jsou zálohy LTR chráněné pomocí geograficky redundantního úložiště. Další informace najdete v tématu [Azure Storage redundance](../storage/common/storage-redundancy.md).

Další informace najdete v tématu [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Spotřeba úložiště záloh 

U izolovaných databází se celkové využití úložiště záloh počítá takto:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

U elastických fondů je celková velikost úložiště zálohování agregovaná na úrovni fondu a počítá se takto:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

Zálohy, které jsou starší než doba uchovávání, se automaticky vyprázdní podle jejich časového razítka. Vzhledem k tomu, že rozdílové zálohy a zálohy protokolů vyžadují, aby bylo předchozí úplné zálohování užitečné, vyčistí se společně v týdenních blocích. 

Azure SQL Database vypočítá celkové úložiště záloh v rámci uchování jako kumulativní hodnotu. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který zodpovídá za agregaci tohoto hodinového využití za účelem výpočtu spotřeby na konci každého měsíce. Po vyřazení databáze se spotřeba sníží jako stáří zálohování. Jakmile budou zálohy starší než doba uchovávání, fakturace se zastaví. 

   > [!IMPORTANT]
   > Zálohy databáze se uchovávají po určenou dobu uchování, a to i v případě, že databáze byla vyřazena. Při vyřazování a opětovném vytváření databáze se často můžou ušetřit náklady na úložiště a výpočetní výkon, což může zvýšit náklady na úložiště záloh, protože uchováváme zálohu pro zadanou dobu uchování (což je 7 dní) pro každou vyřazenou databázi, pokaždé, když se vynechá. 



### <a name="monitor-consumption"></a>Monitorovat spotřebu

Každý typ zálohy (úplný, rozdíl a protokol) je hlášen v okně monitorování databáze jako samostatná metrika. Následující diagram ukazuje, jak monitorovat spotřebu úložiště záloh pro jednu databázi. Tato funkce není pro spravované instance aktuálně k dispozici.

![Monitorovat spotřebu zálohy databáze v okně monitorování databáze Azure Portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Doladit spotřebu úložiště zálohování

Nadměrná spotřeba úložiště záloh bude záviset na zatížení a velikosti jednotlivých databází. Můžete zvážit implementaci některých z následujících postupů optimalizace pro další snížení spotřeby úložiště záloh:

* Snižte [dobu uchovávání záloh](#change-pitr-backup-retention-period-using-azure-portal) na minimum, co potřebujete.
* Vyhněte se provádění rozsáhlých operací zápisu častěji, než je potřeba, jako je například opětovné sestavení indexu.
* Při operacích s velkým objemem dat zvažte použití [clusterovaných indexů columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snižte počet neclusterovaných indexů a zvažte také operace hromadného načítání s počtem řádků přibližně 1 000 000.
* V Pro obecné účely úrovni služby je zřízené úložiště dat levnější než cena za nadměrné úložiště záloh, protože zákazníci s průběžnými vysokými náklady na úložiště záloh můžou zvážit zvýšení úložiště dat, aby se uložily na úložiště zálohování.
* Použijte databázi TempDB v logice ETL k ukládání dočasných výsledků místo trvalých tabulek (platí pouze pro spravovanou instanci).
* Zvažte vypnutí šifrování TDE pro databáze, které neobsahují citlivá data (např. vývojové nebo testovací databáze). Zálohy pro nešifrované databáze jsou obvykle komprimovány s vyšším kompresním poměrem.

> [!IMPORTANT]
> Pro analytická Datová tržiště \ úlohy datového skladu se důrazně doporučuje použít [clusterované indexy columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snížit počet neclusterovaných indexů a také zvážit operace hromadného načítání s počtem řádků přibližně 1 000 000, abyste snížili nadměrné využití úložiště záloh.


## <a name="storage-costs"></a>Cena za uložení

Cena za úložiště se liší v případě, že používáte model DTU nebo model vCore. 

### <a name="dtu-model"></a>Model DTU

Pro úložiště zálohování databází a elastických fondů pomocí modelu DTU se neúčtují žádné další poplatky. 

### <a name="vcore-model"></a>Model virtuálních jader

U izolovaných databází se minimální hodnota záložního úložiště rovná 100% velikosti databáze je k dispozici bez dalších poplatků. U elastických fondů a spravovaných instancí se minimální hodnota úložiště zálohy rovná 100% přiděleného úložiště dat pro fond nebo velikost instance, v uvedeném pořadí, je k dispozici bez dalších poplatků. Další spotřeba úložiště zálohování se bude účtovat v GB za měsíc. Tato další spotřeba bude záviset na zatížení a velikosti jednotlivých databází.

Azure SQL DB bude počítat celkové úložiště záloh v rámci uchovávání jako kumulativní hodnotu. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který zodpovídá za agregaci tohoto hodinového využití za účelem získání spotřeby na konci každého měsíce. Po vyřazení databáze snížíme spotřebu jako stáří zálohy. Jakmile budou starší než doba uchovávání, fakturace se zastaví. Vzhledem k tomu, že se všechny zálohy protokolů a rozdílové zálohy uchovávají pro celou dobu uchovávání dat, budou vysoce upravované databáze mít vyšší poplatky za zálohování. 

Předpokládejme, že databáze shromáždila 744 GB úložiště zálohování a tato částka zůstane v celém měsíci konstantní. Pokud chcete tuto kumulativní spotřebu úložiště převést na hodinové použití, rozdělíme ji na 744,0 (31 dnů měsíčně * 24 hodin za den). Proto databáze SQL DB každou hodinu využívala 1 GB PITR zálohování. Faktura za Azure agreguje toto a ukáže využití 744 GB po celý měsíc a náklady na základě sazby $/GB/mo ve vaší oblasti. 

Teď je to složitější příklad. Předpokládejme, že se v databázi zakázalo zvýšení na 14 dní uprostřed měsíce a tato (hypoteticky) má za následek celkové úložiště zálohování od zdvojnásobení až 1488 GB. SQL DB by nahlásilo 1 GB využití v hodinách 1-372 a pak vykazovat využití jako 2 GB po dobu 373-744. Tato částka se agreguje jako finální faktura za 1116 GB/měsíc. 

### <a name="monitor-costs"></a>Sledovat náklady

Pokud chcete pochopit náklady na úložiště zálohování, v Azure Portal klikněte na **cost management + fakturace** , vyberte **cost management**a pak vyberte **Analýza nákladů**. Vyberte požadované předplatné jako **obor**a potom vyfiltrujte časové období a službu, které vás zajímají. 

Přidejte filtr pro **název služby**a pak v rozevíracím seznamu vyberte **SQL Database** . Filtr **podkategorie měřiče** použijte k výběru čítače fakturace pro vaši službu. Pro izolovanou databázi nebo elastický fond vyberte **úložiště záloh Pitr (Single/elastický fond**). V případě spravované instance vyberte možnost **mi Pitr úložiště zálohování**. **Úložiště** a podkategorie **výpočtů** můžou zajímat i v případě, že nejsou spojené s náklady na úložiště zálohování. 

![Analýza nákladů na úložiště zálohování](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Uchování záloh

Všechny databáze Azure SQL (jedna, sdružená a databáze spravované instance) mají výchozí dobu uchovávání záloh po dobu **sedmi** dnů. [Dobu uchování zálohy můžete změnit až na 35 dnů](#change-pitr-backup-retention-period).

Pokud databázi odstraníte, SQL Database zachová zálohy stejným způsobem jako u online databáze. Pokud například odstraníte databázi Basic, která má dobu uchovávání 7 dní, záloha, která je starší než 4 dny, se uloží na tři dny.

Pokud potřebujete uchovat zálohy po dobu delší, než je maximální doba uchovávání, můžete upravit vlastnosti zálohy a přidat jednu nebo více dlouhodobých dob uchovávání do databáze. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Pokud odstraníte server SQL Azure hostující databáze SQL, odstraní se také všechny elastické fondy a databáze patřící do serveru a nelze je obnovit. Odstraněný Server nelze obnovit. Pokud jste ale nakonfigurovali dlouhodobé uchovávání, zálohy pro databáze s LTR nebudou odstraněny a tyto databáze je možné obnovit.

## <a name="encrypted-backups"></a>Šifrovaná zálohování

Pokud je vaše databáze zašifrovaná pomocí TDE, zálohy se automaticky zašifrují v klidovém stavu, včetně záloh LTR. Když je u databáze SQL Azure povolené TDE, zálohují se taky zálohy. Ve výchozím nastavení jsou všechny nové databáze SQL Azure nakonfigurované s povoleným TDE. Další informace o TDE naleznete v tématu [transparentní šifrování dat with Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrita zálohy

V nepřetržitém případě Azure SQL Database technický tým automaticky testuje obnovení automatizovaných záloh databáze databází umístěných na logických serverech a elastických fondech (není k dispozici ve spravované instanci). Při obnovení k bodu v čase získávají databáze také kontroly integrity pomocí příkazu DBCC CHECKDB.

Spravovaná instance provádí automatické počáteční zálohování s `CHECKSUM` databází obnovených pomocí nativního příkazu `RESTORE` nebo služby migrace dat po dokončení migrace.

Všechny problémy zjištěné během kontroly integrity budou mít za následek upozornění technickému týmu. Další informace o integritě dat v Azure SQL Database najdete v tématu [Integrita dat v Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Dodržování předpisů

Při migraci databáze z úrovně služby založené na DTU na úroveň služby založené na vCore se uchování PITR zachová, aby se zajistilo ohrožení zásad obnovení dat vaší aplikace. Pokud výchozí doba uchovávání nesplňuje požadavky na dodržování předpisů, můžete změnit dobu uchování PITR pomocí PowerShellu nebo REST API. Další informace najdete v tématu [Změna doby uchování zálohy](#change-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Změnit dobu uchování zálohy PITR

Výchozí dobu uchovávání záloh PITR můžete změnit pomocí Azure Portal, PowerShellu nebo REST API. Následující příklady ukazují, jak změnit PITR uchování na 28 dní.

> [!WARNING]
> Pokud zmenšíte aktuální dobu uchovávání, nebudou už všechny existující zálohy starší než nová doba uchování k dispozici. Pokud zvýšíte aktuální dobu uchovávání, SQL Database zachová stávající zálohy, dokud nedosáhnete delší doby uchovávání.

> [!NOTE]
> Tato rozhraní API budou mít vliv jenom na dobu uchovávání PITR. Pokud jste nakonfigurovali LTR pro vaši databázi, nebude to mít vliv na. Další informace o tom, jak změnit dobu uchování LTR, najdete v tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Změna doby uchovávání záloh PITR pomocí Azure Portal

Pokud chcete změnit dobu uchovávání záloh PITR pomocí Azure Portal, přejděte na objekt serveru, jehož doba uchovávání dat chcete změnit na portálu, a pak vyberte vhodnou možnost podle toho, který objekt serveru upravujete.

#### <a name="single-database--elastic-pools"></a>[Elastické fondy & jedné databáze](#tab/single-database)

Změna uchovávání PITR zálohování pro jednu databázi Azure SQL se provádí na úrovni serveru. Změny provedené na úrovni serveru se vztahují na databáze na tomto serveru. Chcete-li změnit PITR pro Azure SQL Database Server z Azure Portal, přejděte na okno Přehled serveru, klikněte na možnost spravovat zálohy v navigační nabídce a pak na navigačním panelu klikněte na možnost konfigurace uchovávání.

![Změnit Azure Portal PITR](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Změna uchovávání záloh PITR pro SQL Database spravovanou instanci se provádí na úrovni jednotlivých databází. Chcete-li změnit uchovávání záloh PITR pro databázi instance z Azure Portal, přejděte do okna Přehled individuální databáze a pak klikněte na možnost konfigurace uchovávání záloh na navigačním panelu.

![Změnit Azure Portal PITR](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Změna doby uchovávání záloh PITR pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Změna doby uchování PITR pomocí REST API

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

Další informace najdete v tématu [REST API uchovávání záloh](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Další kroky

- Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Další informace o dalších Azure SQL Database řešení pro provozní kontinuitu najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
- Obnovení k určitému bodu v čase pomocí Azure Portal najdete v tématu [obnovení databáze k určitému bodu v čase pomocí Azure Portal](sql-database-recovery-using-backups.md).
- Postup obnovení k určitému bodu v čase pomocí prostředí PowerShell najdete v tématu [obnovení databáze k určitému bodu v čase pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurace, Správa a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí Azure Portal najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Informace o konfiguraci, správě a obnovení z dlouhodobého uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí PowerShellu najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí PowerShellu](sql-database-long-term-backup-retention-configure.md).
