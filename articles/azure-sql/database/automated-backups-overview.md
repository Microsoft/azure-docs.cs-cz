---
title: Automatické, geograficky redundantní zálohy
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database a Azure SQL Managed instance automaticky vytvoří zálohování místní databáze každých pár minut a pro geografickou redundanci použije geograficky redundantní úložiště s přístupem pro čtení z Azure.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 0d6ab6152d7025098006c580673848fe0268346b
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141836"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatizované zálohování – Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Jak Azure SQL Database, tak Azure SQL Managed instance vytvoří zálohy databáze, které se uchovávají po dobu trvání nakonfigurované doby uchování. Používají [geograficky redundantní úložiště Azure s přístupem pro čtení (RA-GRS)](../../storage/common/storage-redundancy.md) k zajištění, že se zálohy uchovávají i v případě, že datové centrum není k dispozici.

Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Pokud vaše pravidla zabezpečení vyžadují, aby byly zálohy dostupné po delší dobu (až 10 let), můžete nakonfigurovat [dlouhodobé uchovávání](long-term-retention-overview.md) pro databáze s jednou i ve fondu.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co je zálohování SQL Database?

SQL Database i spravované instance SQL používají SQL Server technologie k vytváření [úplných záloh](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) každý týden, [rozdílové zálohování](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) každých 12 hodin a [zálohování protokolů transakcí](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) každých 5 až 10 minut. Zálohy se ukládají v objektech [BLOB úložiště RA-GRS](../../storage/common/storage-redundancy.md) , které se replikují do [spárovaného datového](../../best-practices-availability-paired-regions.md) centra a chrání se před výpadkem datového centra. Při obnovení databáze služba Určuje, které zálohy úplného, rozdílového a transakčního protokolu se musí obnovit.

Tyto zálohy rovněž umožňují:

- **Obnovte stávající databázi k určitému bodu v čase v minulosti** v době uchování pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API. Pro databáze s jednou a ve fondu vytvoří tato operace novou databázi na stejném serveru jako původní databázi. Ve spravované instanci může tato operace vytvořit kopii databáze nebo stejnou nebo jinou spravovanou instanci v rámci stejného předplatného.
- **Obnovení odstraněné databáze na čas odstranění** nebo kdykoli v rámci doby uchování. Odstraněnou databázi lze obnovit pouze na stejném serveru nebo ve spravované instanci, kde byla vytvořena původní databáze.
- **Obnovte databázi do jiné geografické oblasti**. Geografické obnovení umožňuje obnovení z geografické havárie, když nemůžete získat přístup k serveru a databázi. Vytvoří novou databázi na jakémkoli existujícím serveru, kdekoli na světě.
- **Obnovte databázi z konkrétní dlouhodobé zálohy** na izolovanou databázi nebo databázi ve fondu, pokud je databáze nakonfigurovaná s použitím dlouhodobých zásad uchovávání informací (LTR). LTR umožňuje obnovit starou verzi databáze pomocí [Azure Portal](long-term-backup-retention-configure.md#using-azure-portal) nebo [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) , aby splňovala požadavek na dodržování předpisů nebo spustil starou verzi aplikace. Další informace najdete v tématu [Dlouhodobé uchovávání](long-term-retention-overview.md).

Chcete-li provést obnovení, přečtěte si téma [obnovení databáze ze zálohy](recovery-using-backups.md).

> [!NOTE]
> V Azure Storage pojem *replikace* označuje kopírování souborů z jednoho umístění do druhého. V jazyce SQL *replikace databáze* odkazuje na udržování více sekundárních databází synchronizovaných s primární databází.

Některé z těchto operací můžete vyzkoušet pomocí následujících příkladů:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Změna uchovávání záloh | [Samostatná databáze](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Spravovaná instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Samostatná databáze](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Změna dlouhodobého uchovávání záloh | [Samostatná databáze](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Spravovaná instance – není k dispozici  | [Samostatná databáze](long-term-backup-retention-configure.md)<br/>Spravovaná instance – není k dispozici  |
| Obnovení databáze z určitého bodu v čase | [Samostatná databáze](recovery-using-backups.md#point-in-time-restore) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Obnovení odstraněné databáze | [Samostatná databáze](recovery-using-backups.md) | [Samostatná databáze](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Spravovaná instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Obnovení databáze ze služby Azure Blob Storage | Izolovaná databáze – není k dispozici <br/>Spravovaná instance – není k dispozici  | Izolovaná databáze – není k dispozici <br/>[Spravovaná instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>Frekvence zálohování

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

SQL Database a SQL Managed instance podporují samoobslužnou službu pro obnovení PITR (Point-in-time) tím, že automaticky vytvoří úplné zálohy, rozdílové zálohy a zálohy protokolu transakcí. Úplné zálohy databáze jsou vytvářeny týdně a rozdílové zálohy databáze se většinou vytvářejí každých 12 hodin. Zálohy protokolu transakcí se většinou vytvářejí každých 5 až 10 minut. Frekvence zálohování protokolu transakcí je založena na výpočetní velikosti a množství aktivity databáze.

První úplné zálohování je naplánováno ihned po vytvoření databáze. Tato záloha se obvykle dokončí do 30 minut, ale pokud je databáze velká, může trvat déle. Například počáteční záloha může trvat déle na obnovenou databázi nebo kopii databáze. Po první úplné záloze se všechny další zálohy naplánují automaticky a budou se bezobslužně spravovat na pozadí. Přesné časování všech záloh databáze určuje služba SQL Database nebo služba SQL Managed instance, protože vyrovnává celkovou úlohu systému. Úlohy zálohování nemůžete změnit ani zakázat.

### <a name="default-backup-retention-period"></a>Výchozí doba uchovávání záloh

Zálohy PITR jsou chráněné pomocí geograficky redundantního úložiště. Další informace najdete v článku [Možnosti redundance Azure Storage](../../storage/common/storage-redundancy.md).

Další informace o PITR najdete v tématu [obnovení k bodu v čase](recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Dlouhodobé uchovávání

Pro databáze typu Single a Pool můžete v úložišti objektů BLOB v Azure nakonfigurovat dlouhodobé uchovávání (LTR) úplných záloh po dobu až 10 let. Pokud povolíte zásadu LTR, týdenní úplné zálohy se automaticky zkopírují do jiného kontejneru úložiště RA-GRS. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jinou dobu uchování. Spotřeba úložiště závisí na zvolené četnosti zálohování a období uchování nebo období. Pomocí [cenové kalkulačky ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) můžete odhadnout náklady na úložiště ltr.

Podobně jako zálohy PITR jsou zálohy LTR chráněné pomocí geograficky redundantního úložiště. Další informace najdete v článku [Možnosti redundance Azure Storage](../../storage/common/storage-redundancy.md).

Další informace o LTR najdete v tématu [dlouhodobé uchovávání záloh](long-term-retention-overview.md).

## <a name="backup-storage-consumption"></a>Spotřeba úložiště záloh

Pro jednotlivé databáze a spravované instance se tato rovnice používá k výpočtu celkového využití úložiště záloh:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Pro databáze ve fondu je celková velikost úložiště zálohování agregovaná na úrovni fondu a počítá se takto:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Zálohy, ke kterým dojde před dobou uchovávání, se automaticky vyprázdní na základě jejich časového razítka. Vzhledem k tomu, že rozdílové zálohy a zálohy protokolů vyžadují, aby bylo předchozí úplné zálohování užitečné, vyčistí se společně v týdenních blocích.

Služba SQL Database a SQL Managed instance počítají celkové úložiště záloh v rámci uchování jako kumulativní hodnotu. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který zodpovídá za agregaci tohoto hodinového využití za účelem výpočtu spotřeby na konci každého měsíce. Po vyřazení databáze se spotřeba sníží jako stáří zálohování. Až budou zálohy starší než doba uchovávání, fakturace se zastaví.
   
   > [!IMPORTANT]
   > Zálohy databáze se uchovávají po určenou dobu uchování, a to i v případě, že databáze byla vyřazena. Při vyřazování a opětovném vytváření databáze se může často ušetřit náklady na úložiště a výpočetní výkon, protože společnost Microsoft uchovává zálohu pro zadanou dobu uchování pro každou vyřazenou databázi pokaždé, když je zahozena. 

### <a name="monitor-consumption"></a>Monitorovat spotřebu

Každý typ zálohy (úplný, rozdíl a protokol) je hlášen v okně monitorování databáze jako samostatná metrika. Následující diagram ukazuje, jak monitorovat spotřebu úložiště zálohování pro jednu databázi. Tato funkce není pro spravované instance aktuálně k dispozici.

![Monitorovat spotřebu zálohy databáze v Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Doladit spotřebu úložiště zálohování

Nadlimitní využití úložiště záloh bude záviset na zatížení a velikosti jednotlivých databází. Vezměte v úvahu některé z následujících technik optimalizace, abyste snížili spotřebu úložiště záloh:

- Snižte [dobu uchovávání záloh](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) na minimum, co potřebujete.
- Vyhněte se provádění rozsáhlých operací zápisu, jako je například opětovné sestavení indexu, častěji než potřebujete.
- Při operacích s velkým objemem dat zvažte použití [clusterovaných indexů columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snižte počet neclusterovaných indexů a zvažte možnost hromadně načíst operace s počtem řádků přibližně 1 000 000.
- V úrovni služby pro obecné účely je zřízené úložiště dat levnější než cena za nadměrné úložiště záloh. Pokud máte průběžné vysoké náklady na úložiště záloh, můžete zvážit zvýšení úložiště dat, které chcete uložit do úložiště zálohování.
- Použijte databázi TempDB místo trvalých tabulek v logice ETL pro ukládání dočasných výsledků. (Platí pouze pro spravovanou instanci SQL.)
- Zvažte vypnutí šifrování TDE pro databáze, které neobsahují citlivá data (například databáze pro vývoj nebo testování). Zálohy pro nešifrované databáze jsou obvykle komprimovány s vyšším kompresním poměrem.

> [!IMPORTANT]
> Pro analytické datové tržiště \ úlohy datového skladu důrazně doporučujeme použít [clusterované indexy columnstore](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), snížit počet neclusterovaných indexů a zvážit operace hromadného načítání s počtem řádků přibližně 1 000 000, abyste snížili nadměrné využití úložiště záloh.

## <a name="storage-costs"></a>Cena za uložení

Cena za úložiště se liší v závislosti na tom, jestli používáte model DTU nebo model vCore.

### <a name="dtu-model"></a>Model DTU

Pro úložiště zálohování databází a fondů elastické databáze se neúčtují žádné další poplatky, pokud používáte model DTU.

### <a name="vcore-model"></a>Model virtuálních jader

U izolovaných databází v SQL Database je minimální hodnota úložiště zálohy rovná 100 procent velikosti databáze, a to bez dalších poplatků. Pro elastické fondy v SQL Database a v jednotlivých instancích a fondech instancí ve spravované instanci SQL se minimální hodnota úložiště zálohy rovná 100 procent přiděleného úložiště dat pro fond nebo velikost instance se poskytuje bez dalších poplatků. Další využití úložiště zálohování se bude účtovat v GB/měsíc. Tato další spotřeba bude záviset na zatížení a velikosti jednotlivých databází.

SQL Database a SQL Managed instance budou počítat celkové úložiště záloh v rámci uchování jako kumulativní hodnotu. Každou hodinu se tato hodnota oznamuje fakturačnímu kanálu Azure, který zodpovídá za agregaci tohoto hodinového využití za účelem získání spotřeby na konci každého měsíce. Po vyřazení databáze sníží společnost Microsoft spotřebu jako stáří zálohy. Až budou zálohy starší než doba uchovávání, fakturace se zastaví. Vzhledem k tomu, že se všechny zálohy protokolů a rozdílové zálohy uchovávají pro celou dobu uchovávání dat, budou mít silně upravované databáze vyšší poplatky za zálohování.

Předpokládejme, že databáze nashromáždila 744 GB úložiště zálohování a tato částka zůstane v celém měsíci konstantní. Pokud chcete tuto kumulativní spotřebu úložiště převést na hodinové použití, rozdělte ji o 744,0 (31 dnů za měsíc × 24 hodin denně). Takže SQL Database bude hlásit, že databáze využila 1 GB zálohování PITR každou hodinu. Fakturace Azure agreguje tuto spotřebu a za celý měsíc ukáže využití 744 GB. Náklady budou založené na sazbě $/GB/month ve vaší oblasti.

Teď je to složitější příklad. Předpokládejme, že se v databázi zavedlo zvýšení jejich uchování na 14 dní uprostřed měsíce. Předpokládat toto zvýšení (hypoteticky) má za následek celkové úložiště zálohování od zdvojnásobení až 1 488 GB. SQL Database by nahlásil 1 GB využití v hodinách 1 až 372. Využití oznamuje 2 GB na hodiny 373 až 744. Toto použití by bylo agregované do konečné faktury 1 116 GB/měsíc.

### <a name="monitor-costs"></a>Sledovat náklady

Pokud chcete pochopit náklady na úložiště zálohování, v Azure Portal klikněte na **cost management + fakturace** , vyberte **cost management**a pak vyberte **Analýza nákladů**. Vyberte požadované předplatné jako **obor**a potom vyfiltrujte časový interval a službu, které vás zajímají.

Přidejte filtr pro **název služby**a potom v rozevíracím seznamu vyberte **SQL Database** . Filtr **podkategorie měřiče** použijte k výběru čítače fakturace pro vaši službu. Pro izolovanou databázi nebo fond elastické databáze vyberte **Single/elastický fond Pitr úložiště zálohování**. V případě spravované instance vyberte **mi Pitr úložiště zálohování**. Podkategorie **úložišť** a **výpočtů** vám můžou zajímat i to, že nejsou spojené s náklady na úložiště zálohování.

![Analýza nákladů na úložiště zálohování](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Uchování záloh

Všechny databáze v Microsoft Azure SQL mají výchozí dobu uchovávání záloh 7 dní. [Dobu uchovávání záloh můžete změnit](#change-the-pitr-backup-retention-period) na odkudkoli mezi 1-35 dny.

Pokud databázi odstraníte, Azure bude zálohy uchovávat stejným způsobem jako online databáze. Pokud například odstraníte databázi Basic, která má dobu uchování sedm dní, uloží se záloha se čtyřmi dny do stáří po dobu tří dalších dnů.

Pokud potřebujete uchovat zálohy po dobu delší, než je maximální doba uchovávání, můžete upravit vlastnosti zálohy a přidat jednu nebo více dlouhodobých dob uchovávání do databáze. Další informace najdete v tématu [Dlouhodobé uchovávání](long-term-retention-overview.md).

> [!IMPORTANT]
> Nastavení doby uchování zálohy na 1 den (nebo na libovolnou hodnotu v rozsahu 1-7) je podporováno pouze prostřednictvím PowerShellu nebo REST API v tuto chvíli. Minimální požadovaná verze modulu AZ. SQL je v 2.6.0 nebo se dá spustit prostřednictvím Cloudshellu, který má vždycky nejnovější verzi AZ. SQL.

> [!IMPORTANT]
> Při odstranění serveru nebo spravované instance se odstraní také všechny databáze spravované tímto serverem nebo spravovanou instancí. Nelze je obnovit. Odstraněný Server nebo spravovanou instanci nelze obnovit. Pokud jste ale nakonfigurovali dlouhodobou dobu uchovávání SQL Database nebo spravovaných instance, zálohy databází s LTR se neodstraní a tyto databáze je možné obnovit.

## <a name="encrypted-backups"></a>Šifrovaná zálohování

Pokud je databáze zašifrovaná pomocí TDE, zálohy se automaticky zašifrují v klidovém stavu, včetně záloh LTR. Pokud je TDE povolená pro SQL Database nebo SQL Managed instance, zálohy se taky šifrují. Všechny nové databáze v Azure SQL jsou ve výchozím nastavení nakonfigurované s povoleným TDE. Další informace o TDE najdete v tématu [transparentní šifrování dat s SQL Database & spravované instance SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrita zálohy

V nepřetržitém případě technický tým Azure SQL automaticky testuje obnovu automatických záloh databáze databází v SQL Database. (Toto testování není k dispozici ve spravované instanci SQL.) Při obnovení k bodu v čase získají databáze také kontroly integrity DBCC CHECKDB.

Spravovaná instance SQL má po dokončení migrace automatické prvotní zálohování s `CHECKSUM` databázemi obnovenými pomocí nativního `RESTORE` příkazu nebo se službou Azure Data Migration Service.

Všechny problémy zjištěné během kontroly integrity budou mít za následek upozornění technickému týmu. Další informace najdete v tématu [Integrita dat v SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Dodržování předpisů

Při migraci databáze z úrovně služby založené na DTU na úroveň služby založené na vCore se uchování PITR zachová, aby se zajistilo ohrožení zásad obnovení dat vaší aplikace. Pokud výchozí doba uchovávání nevyhovuje vašim požadavkům na dodržování předpisů, můžete změnit dobu uchování PITR pomocí PowerShellu nebo REST API. Další informace najdete v tématu [Změna doby uchování zálohy PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Změna doby uchování zálohy PITR

Výchozí dobu uchování zálohy PITR můžete změnit pomocí Azure Portal, PowerShellu nebo REST API. Následující příklady ukazují, jak změnit PITR uchování na 28 dní.

> [!WARNING]
> Pokud zmenšíte aktuální dobu uchovávání, nebudou už všechny stávající zálohy, které jsou starší než nová doba uchovávání, dostupné. Pokud zvýšíte aktuální dobu uchovávání, Azure udržuje stávající zálohy, dokud nedosáhne konce delší doby uchovávání.

> [!NOTE]
> Tato rozhraní API budou mít vliv jenom na dobu uchovávání PITR. Pokud jste nakonfigurovali LTR pro vaši databázi, nebude to mít vliv na. Informace o tom, jak změnit dobu uchování LTR, najdete v tématu [dlouhodobé uchovávání](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Změňte dobu uchování zálohy PITR pomocí Azure Portal

Pokud chcete změnit dobu uchovávání záloh PITR pomocí Azure Portal, přejděte na objekt serveru, jehož doba uchovávání dat má být na portálu změněna. Pak vyberte odpovídající možnost v závislosti na objektu serveru, který upravujete.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Změny uchovávání PITR zálohování pro SQL Database se provádí na úrovni serveru. Změny provedené na úrovni serveru platí pro databáze na serveru. Pokud chcete změnit PITR uchovávání pro server z Azure Portal, přejděte na okno Přehled serveru. V levém podokně vyberte **Spravovat zálohy** a pak v horní části obrazovky vyberte **Konfigurovat uchování** :

![Změna uchovávání PITR, úrovně serveru](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Spravovaná instance SQL](#tab/managed-instance)

Změny uchovávání záloh PITR pro spravovanou instanci SQL se provádí na úrovni jednotlivých databází. Chcete-li změnit uchovávání záloh PITR pro databázi instance z Azure Portal, přejděte do okna Přehled jednotlivé databáze. Pak vyberte **Konfigurovat uchovávání záloh** v horní části obrazovky:

![Změnit uchovávání PITR, spravovaná instance](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Změna doby uchovávání záloh PITR pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell AzureRM je stále podporován SQL Database a SQL Managed instance, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Další informace naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty příkazů v modulu AZ jsou v podstatě stejné jako v modulech AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Změňte dobu uchování zálohy PITR pomocí REST API

#### <a name="sample-request"></a>Ukázková žádost

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

Další informace najdete v tématu [REST API uchovávání záloh](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Další kroky

- Zálohy databází jsou důležitou součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože chrání vaše data před náhodným poškozením nebo odstraněním. Další informace o dalších SQL Database řešení pro provozní kontinuitu najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Získejte další informace o tom, jak [obnovit databázi k určitému bodu v čase pomocí Azure Portal](recovery-using-backups.md).
- Získejte další informace o tom, jak [obnovit databázi k určitému bodu v čase pomocí prostředí PowerShell](scripts/restore-database-powershell.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovit dlouhodobé uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí Azure Portal, najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí Azure Portal](long-term-backup-retention-configure.md).
- Informace o tom, jak nakonfigurovat, spravovat a obnovit dlouhodobé uchovávání automatizovaných záloh v úložišti objektů BLOB v Azure pomocí PowerShellu, najdete v tématu [Správa dlouhodobého uchovávání záloh pomocí PowerShellu](long-term-backup-retention-configure.md).
