---
title: Obnovení databáze ze zálohy
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si o obnovení k určitému bodu v čase, které vám umožní vrátit databázi v Azure SQL Database nebo instanci ve spravované instanci Azure SQL až do 35 dnů.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 415c9fdcbf0e8bfecaa48b8199702d4159bc32d9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629185"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Obnovení pomocí automatických záloh databáze – Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

K dispozici jsou následující možnosti pro obnovení databáze pomocí [automatických záloh databáze](automated-backups-overview.md). Můžete:

- Vytvoří novou databázi na stejném serveru, která se obnovila k určitému bodu v čase v rámci doby uchování.
- Vytvoří databázi na stejném serveru, která se obnovila do doby odstraňování odstraněné databáze.
- Vytvoří novou databázi na jakémkoli serveru ve stejné oblasti, která se obnovila do bodu posledních záloh.
- Vytvoří novou databázi na jakémkoli serveru v jakékoli jiné oblasti, která se obnoví na místo nejaktuálnějších replikovaných záloh.

Pokud jste nakonfigurovali [dlouhodobě dlouhodobé uchovávání záloh](long-term-retention-overview.md), můžete také vytvořit novou databázi z jakékoli dlouhodobé zálohy uchovávání na jakémkoli serveru.

> [!IMPORTANT]
> Během obnovování nelze přepsat existující databázi.

Pokud používáte úroveň služeb Standard nebo Premium, vaše obnovení databáze může znamenat dodatečné náklady na úložiště. Dodatečné náklady se účtují, když je maximální velikost obnovené databáze větší než velikost úložiště zahrnutá do úrovně služby a úrovně výkonu cílové databáze. Podrobnosti o cenách dodatečného úložiště najdete na [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečná velikost využitého místa menší než velikost zahrnutého úložiště, můžete této dodatečné ceně zabránit nastavením maximální velikosti databáze na zahrnuté množství.

## <a name="recovery-time"></a>Čas obnovení

Čas obnovení pro obnovení databáze pomocí automatických záloh databáze je ovlivněn několika faktory:

- Velikost databáze.
- Velikost výpočetní databáze.
- Počet zahrnutých protokolů transakcí.
- Množství aktivity, které je třeba znovu přehrát pro obnovení do bodu obnovení.
- Šířka pásma sítě, pokud je obnovení do jiné oblasti.
- Počet souběžných požadavků na obnovení zpracovávaných v cílové oblasti.

Pro velkou nebo velmi aktivní databázi může obnovení trvat několik hodin. Pokud v oblasti dojde k dlouhodobému výpadku, je možné, že se iniciuje velký počet požadavků na geografickou obnovu pro zotavení po havárii. Pokud existuje mnoho požadavků, může se zvýšit doba obnovení pro jednotlivé databáze. Většina obnovení databáze se dokončí za méně než 12 hodin.

U jednoho předplatného platí omezení počtu souběžných požadavků na obnovení. Tato omezení se vztahují na jakoukoli kombinaci obnovení, geografického obnovení a obnovení k bodu v čase v dlouhodobém zálohování.

| **Možnost nasazení** | **Maximální počet souběžných požadavků zpracovávaných** | **Maximální počet souběžných požadavků, které jsou odesílány** |
| :--- | --: | --: |
|**Samostatná databáze (na předplatné)**|10|60|
|**Elastický fond (pro každý fond)**|4|200|


Neexistuje integrovaná metoda pro obnovení celého serveru. Příklad toho, jak tento úkol provést, najdete v tématu [Azure SQL Database: úplné obnovení serveru](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Chcete-li provést obnovení pomocí automatizovaných záloh, musíte být členem role přispěvatele SQL Server nebo role Přispěvatel spravované instance SQL (v závislosti na cíli obnovení) v předplatném nebo musíte být vlastníkem předplatného. Další informace naleznete v části [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí Azure Portal, PowerShellu nebo REST API. Nemůžete použít jazyk Transact-SQL.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

K dřívějšímu bodu v čase můžete pomocí Azure Portal, [PowerShellu](/powershell/module/az.sql/restore-azsqldatabase)nebo [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)obnovit samostatnou databázi ve fondu nebo instanci. Požadavek může pro obnovenou databázi zadat libovolnou úroveň služby nebo výpočetní velikost. Ujistěte se, že na serveru, na který obnovujete databázi, máte dostatečné prostředky. 

Po dokončení obnovení vytvoří novou databázi na stejném serveru jako původní databázi. Obnovená databáze se účtuje za normálních sazeb na základě její úrovně služeb a výpočetní velikosti. Dokud se obnovení databáze nedokončí, neúčtují se vám žádné poplatky.

Obecně obnovuje databázi do dřívějšího bodu pro účely obnovení. Obnovenou databázi můžete považovat za náhradu původní databáze nebo ji použít jako zdroj dat k aktualizaci původní databáze.

- **Nahrazení databáze**

  Pokud plánujete, že obnovená databáze bude náhradou původní databáze, měli byste zadat výpočetní velikost a úroveň služby původní databáze. Pak můžete původní databázi přejmenovat a pomocí příkazu [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) v T-SQL pojmenovat obnovenou databázi původní název.

- **Obnovení dat**

  Pokud plánujete načíst data z obnovené databáze pro obnovení z chyby uživatele nebo aplikace, je nutné napsat a spustit skript pro obnovení dat, který extrahuje data z obnovené databáze a platí pro původní databázi. I když operace obnovení může trvat dlouhou dobu, obnovovaná databáze se zobrazí v seznamu databáze během procesu obnovení. Pokud databázi odstraníte během obnovování, operace obnovení se zruší a nebude se vám účtovat databáze, která obnovení nedokončila.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Obnovení k bodu v čase pomocí Azure Portal

V okně Přehled databáze, kterou chcete obnovit v Azure Portal, můžete obnovit jednu databázi nebo instanci databáze k určitému bodu v čase.

#### <a name="sql-database"></a>SQL Database

Chcete-li obnovit databázi k určitému bodu v čase pomocí Azure Portal, otevřete stránku Přehled databáze a na panelu nástrojů vyberte možnost **obnovit** . Zvolte zdroj zálohy a vyberte bod zálohování bodu v čase, ze kterého bude vytvořena nová databáze.

  ![Snímek obrazovky s možnostmi obnovení databáze pro SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Chcete-li obnovit databázi spravované instance k určitému bodu v čase pomocí Azure Portal, otevřete stránku Přehled databáze a na panelu nástrojů vyberte možnost **obnovit** . Vyberte bod zálohování bodu v čase, ze kterého se vytvoří nová databáze.

  ![Snímek obrazovky s možnostmi obnovení databáze pro spravovanou instanci SQL](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Chcete-li programově obnovit databázi ze zálohy, přečtěte si téma [programové provádění obnovení pomocí automatických záloh](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Obnovení databáze se odstranilo

Odstraněnou databázi můžete obnovit na čas odstranění nebo předchozí bod v čase na stejném serveru nebo stejné spravované instanci. Můžete to provést pomocí Azure Portal, [PowerShellu](/powershell/module/az.sql/restore-azsqldatabase)nebo [Rest (CreateMode = Restore)](/rest/api/sql/databases/createorupdate). Odstraněnou databázi obnovíte vytvořením nové databáze ze zálohy.

> [!IMPORTANT]
> Odstraníte-li server nebo spravovanou instanci, všechny její databáze budou také odstraněny a nelze je obnovit. Odstraněný Server nebo spravovanou instanci nelze obnovit.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Odstranění obnovení databáze pomocí Azure Portal

Odstraněné databáze ze serveru nebo prostředku spravované instance obnovíte z Azure Portal.

> [!TIP]
> Může trvat několik minut, než se nedávno odstraněné databáze zobrazí na stránce **odstraněné databáze** v Azure Portal, nebo při [programovém](#programmatic-recovery-using-automated-backups)zobrazení odstraněných databází.

#### <a name="sql-database"></a>SQL Database

Chcete-li obnovit odstraněnou databázi na čas odstranění pomocí Azure Portal, otevřete stránku Přehled serveru a vyberte **odstraněné databáze**. Vyberte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky obnovení odstraněné databáze](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Chcete-li obnovit spravovanou databázi pomocí Azure Portal, otevřete stránku Přehled spravované instance a vyberte **odstraněné databáze**. Vyberte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky obnovení odstraněné databáze spravované instance Azure SQL](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Odstranění obnovení databáze pomocí PowerShellu

Pomocí následujících ukázkových skriptů můžete obnovit odstraněnou databázi pro SQL Database nebo SQL spravovanou instanci pomocí prostředí PowerShell.

#### <a name="sql-database"></a>SQL Database

Vzorový skript PowerShellu, který ukazuje, jak obnovit odstraněnou databázi v Azure SQL Database, najdete v tématu [obnovení databáze pomocí PowerShellu](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Vzorový skript PowerShellu ukazující, jak obnovit odstraněnou databázi instance, najdete v tématu [Obnovení odstraněné databáze instance pomocí PowerShellu](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) .

> [!TIP]
> Chcete-li programově obnovit odstraněnou databázi, přečtěte si téma [programové provádění obnovení pomocí automatických záloh](recovery-using-backups.md).

## <a name="geo-restore"></a>Geografické obnovení

> [!IMPORTANT]
> Geografické obnovení je dostupné jenom pro databáze SQL nebo spravované instance nakonfigurované s geograficky redundantním [úložištěm záloh](automated-backups-overview.md#backup-storage-redundancy).

Databázi můžete obnovit na jakémkoli serveru s SQL Database nebo v databázi instancí na jakékoli spravované instanci v libovolné oblasti Azure z nejnovějších geograficky replikovaných záloh. Geografické obnovení používá jako zdroj geograficky replikovanou zálohu. Můžete požadovat geografické obnovení i v případě, že je databáze nebo datacentrum nedostupné kvůli výpadku.

Geografické obnovení je výchozí možností obnovení v případě, že databáze není k dispozici z důvodu incidentu v oblasti hostování. Databázi můžete obnovit na server v jakékoli jiné oblasti. Doba, po kterou se zálohování provádí, a když se geograficky replikují do objektu blob Azure v jiné oblasti, nastane zpoždění. V důsledku toho může být obnovená databáze až o jednu hodinu za původní databází. Následující ilustrace znázorňuje obnovení databáze z poslední dostupné zálohy v jiné oblasti.

![Grafika geografického obnovení](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geografické obnovení pomocí Azure Portal

Z Azure Portal vytvoříte novou databázi jedné nebo spravované instance a vyberete dostupnou zálohu geografického obnovení. Nově vytvořená databáze obsahuje geograficky obnovená data zálohy.

#### <a name="sql-database"></a>SQL Database

Chcete-li geograficky obnovit izolovanou databázi z Azure Portal v oblasti a na serveru podle vašeho výběru, postupujte podle následujících kroků:

1. Z **řídicího panelu** vyberte **Přidat**  >  **vytvořit SQL Database**. Na kartě **základy** zadejte požadované informace.
2. Vyberte **Další nastavení**.
3. Pro možnost **použít existující data** vyberte **zálohování**.
4. V části **zálohování** vyberte zálohu ze seznamu dostupných záloh geografického obnovení.

    ![Snímek obrazovky s možnostmi vytvoření SQL Database](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Dokončete proces vytváření nové databáze ze zálohy. Když v Azure SQL Database vytvoříte databázi, obsahuje obnovenou zálohu geografického obnovení.

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Chcete-li geograficky obnovit databázi spravované instance z Azure Portal do existující spravované instance v oblasti podle vašeho výběru, vyberte spravovanou instanci, na které chcete databázi obnovit. Postupujte takto:

1. Vyberte **Nová databáze**.
2. Zadejte požadovaný název databáze.
3. V části **použít existující data** vyberte **zálohování**.
4. Vyberte zálohu ze seznamu dostupných záloh geografického obnovení.

    ![Snímek obrazovky s novými možnostmi databáze](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Dokončete proces vytváření nové databáze. Při vytváření databáze instance obsahuje obnovenou zálohu geografického obnovení.

### <a name="geo-restore-by-using-powershell"></a>Geografické obnovení pomocí PowerShellu

#### <a name="sql-database"></a>SQL Database

Skript PowerShellu, který ukazuje, jak provést geografickou obnovu pro izolovanou databázi, najdete v tématu [použití PowerShellu k obnovení izolované databáze k dřívějšímu bodu v čase](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Skript PowerShellu, který ukazuje, jak provést geografickou obnovu pro databázi spravované instance, najdete v tématu [použití PowerShellu k obnovení databáze spravované instance do jiné geografické oblasti](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Posouzení geografického obnovení

Obnovení k určitému bodu v čase nelze provést v geograficky sekundární databázi. To lze provést pouze v primární databázi. Podrobné informace o použití geografického obnovení k zotavení po výpadku najdete v tématu [obnovení při výpadku](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> Geografické obnovení je nejzákladnější řešení zotavení po havárii dostupné v SQL Database a spravované instanci SQL. Spoléhá se na automaticky vytvořená geograficky replikované zálohy s cílem bodu obnovení (RPO) až 1 hodinu a odhadovanou dobu obnovení až 12 hodin. Nezaručuje, že cílová oblast bude mít kapacitu pro obnovení vašich databází po oblastním výpadku, protože je pravděpodobný prudký nárůst poptávky. Pokud vaše aplikace používá relativně malé databáze a není důležitá pro firmu, geografické obnovení je vhodné řešení zotavení po havárii. 
>
> Pro důležité obchodní aplikace, které vyžadují velké databáze a které musí zajistit kontinuitu podnikových aplikací, použijte [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md). Nabízí mnohem nižší cíl RPO a doby obnovení a kapacita je vždycky zaručená. 
>
> Další informace o volbách kontinuity podnikových aplikací najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Programové obnovení pomocí automatizovaných záloh

Pro obnovení můžete použít také Azure PowerShell nebo REST API. V následujících tabulkách jsou popsány sady příkazů, které jsou k dispozici.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován SQL Database a spravovanou instancí SQL, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a in Azure Resource Manager jsou v dobrém rozsahu stejné.

#### <a name="sql-database"></a>SQL Database

Informace o obnovení samostatné databáze nebo databáze ve fondu najdete v tématu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Rutina | Popis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Získá geograficky redundantní zálohu databáze. |
  | [Obnovit – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Obnoví databázi. |

  > [!TIP]
  > Vzorový skript PowerShellu, který ukazuje, jak provést obnovení databáze k určitému bodu v čase, najdete v tématu [obnovení databáze pomocí PowerShellu](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Chcete-li obnovit databázi spravované instance, přečtěte si téma [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Rutina | Popis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Získá jednu nebo víc spravovaných instancí. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Načte databázi instance. |
  | [Obnovit – AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Obnoví databázi instance. |

### <a name="rest-api"></a>REST API

Postup obnovení databáze pomocí REST API:

| Rozhraní API | Popis |
| --- | --- |
| [REST (createMode = obnovení)](/rest/api/sql/databases) |Obnoví databázi. |
| [Získat stav databáze pro vytvoření nebo aktualizaci](/rest/api/sql/operations) |Vrátí stav během operace obnovení. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Chcete-li obnovit databázi pomocí rozhraní příkazového řádku Azure, přečtěte si téma [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Spravovaná instance SQL

Pokud chcete obnovit databázi spravované instance pomocí Azure CLI, přečtěte si téma [AZ SQL MIDB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Shrnutí

Automatické zálohování chrání vaše databáze před chybami uživatelů a aplikací, náhodným odstraněním databáze a prodlouženými výpadky. Tato integrovaná funkce je k dispozici pro všechny úrovně služeb a výpočetní velikosti.

## <a name="next-steps"></a>Další kroky

- [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database automatizované zálohy](automated-backups-overview.md)
- [Dlouhodobé uchovávání](long-term-retention-overview.md)
- Další informace o možnostech rychlejšího obnovení najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md) nebo [skupiny s automatickým převzetím služeb při selhání](auto-failover-group-overview.md).