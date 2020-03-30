---
title: Obnovení databáze ze zálohy
description: Další informace o obnovení v čase, které umožňuje vrátit zpět databázi Azure SQL až na 35 dní.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268740"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Obnovení databáze Azure SQL pomocí automatického zálohování databáze

Ve výchozím nastavení jsou zálohy azure sql database uloženy v geograficky replikovaném úložišti objektů blob (typ úložiště RA-GRS). Následující možnosti jsou k dispozici pro obnovení databáze pomocí [automatického zálohování databáze](sql-database-automated-backups.md). Můžete:

- Vytvořte novou databázi na stejném databázovém serveru SQL, obnovené do zadaného bodu v čase v rámci doby uchování.
- Vytvořte databázi na stejném databázovém serveru SQL, která bude obnovena do doby odstranění odstraněné databáze.
- Vytvořte novou databázi na libovolném serveru SQL Database ve stejné oblasti, obnovené do bodu nejnovější zálohy.
- Vytvořte novou databázi na libovolném serveru SQL Database v jakékoli jiné oblasti, obnovené do bodu nejnovější replikované zálohy.

Pokud jste nakonfigurovali [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md), můžete také vytvořit novou databázi z libovolné dlouhodobé zálohy uchovávání na libovolném serveru SQL Database.

> [!IMPORTANT]
> Během obnovení nelze přepsat existující databázi.

Pokud používáte úrovně služeb Standard nebo Premium, může obnovení databáze stát za dodatečné náklady na úložiště. Dodatečné náklady vznikají, když je maximální velikost obnovené databáze větší než velikost úložiště, která je součástí úrovně služeb cílové databáze a úrovně výkonu. Podrobnosti o cenách dalšího úložiště naleznete na [stránce s cenami databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnuté úložiště, můžete se tomuto dodatečnému nákladu vyhnout nastavením maximální velikosti databáze na zahrnutou částku.

## <a name="recovery-time"></a>Doba zotavení

Doba obnovení obnovení databáze pomocí automatického zálohování databáze je ovlivněna několika faktory:

- Velikost databáze.
- Výpočetní velikost databáze.
- Počet protokolů transakcí zapojených.
- Množství aktivity, které je třeba přehrát obnovit do bodu obnovení.
- Šířka pásma sítě, pokud je obnovení do jiné oblasti.
- Počet souběžných požadavků na obnovení, které jsou zpracovávány v cílové oblasti.

U velké nebo velmi aktivní databáze může obnovení trvat několik hodin. Pokud dojde k dlouhodobému výpadku v oblasti, je možné, že bude zahájen vysoký počet požadavků na geografické obnovení pro zotavení po havárii. Pokud existuje mnoho požadavků, může se zvýšit doba obnovení pro jednotlivé databáze. Většina obnovení databáze je dokončena za méně než 12 hodin.

Pro jedno předplatné existují omezení počtu souběžných požadavků na obnovení. Tato omezení platí pro libovolnou kombinaci obnovení bodu v čase, geografické obnovení a obnovení z dlouhodobé horečné zálohy.

| | **Maximální počet souběžných zpracovávaných požadavků** | **Maximální počet odeslaných souběžných požadavků** |
| :--- | --: | --: |
|Jednotná databáze (na jedno předplatné)|10|60|
|Elastický bazén (na bazén)|4|200|
||||

Neexistuje vestavěná metoda pro obnovení celého serveru. Příklad, jak provést tento úkol, najdete [v tématu Azure SQL Database: Úplné obnovení serveru](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Chcete-li obnovit pomocí automatické zálohy, musíte být členem role přispěvatele SQL Server v předplatném nebo být vlastníkem předplatného. Další informace naleznete v tématu [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md). Můžete obnovit pomocí portálu Azure, PowerShell nebo rozhraní REST API. Nemůžete použít Transact-SQL.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Můžete obnovit samostatnou, sdruženou nebo instanční databázi do dřívějšího bodu v čase pomocí portálu Azure, [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases). Požadavek můžete určit libovolnou úroveň služby nebo výpočetní velikost pro obnovenou databázi. Ujistěte se, že máte dostatek prostředků na serveru, na který obnovujete databázi. Po dokončení obnovení vytvoří novou databázi na stejném serveru jako původní databáze. Obnovená databáze se účtuje za normální sazby na základě úrovně služby a výpočetní velikosti. Poplatky se účtují až po dokončení obnovení databáze.

Obecně obnovit databázi do dřívějšího bodu pro účely obnovení. Obnovenou databázi můžete považovat za náhradu původní databáze nebo ji použít jako zdroj dat k aktualizaci původní databáze.

- **Nahrazení databáze**

  Pokud chcete, aby obnovená databáze byla náhradou za původní databázi, měli byste zadat výpočetní velikost původní databáze a úroveň služby. Potom můžete přejmenovat původní databázi a dát obnovené databázi původní název pomocí příkazu [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) v T-SQL.

- **Obnova dat**

  Pokud plánujete načíst data z obnovené databáze, abyste ji obnovili z chyby uživatele nebo aplikace, je třeba zapisovat a spouštět skript pro obnovu dat, který extrahuje data z obnovené databáze a vztahuje se na původní databázi. Přestože operace obnovení může trvat dlouhou dobu k dokončení, obnovení databáze je viditelná v seznamu databáze v průběhu procesu obnovení. Pokud odstraníte databázi během obnovení, operace obnovení bude zrušena a nebude vám účtován poplatek za databázi, která nedokončila obnovení.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Obnovení v čase pomocí portálu Azure

Můžete obnovit jednu databázi SQL nebo databázi instancí do bodu v čase z okna přehledu databáze, kterou chcete obnovit na webu Azure Portal.

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Pokud chcete obnovit jednu nebo sdruženou databázi do bodu v čase pomocí portálu Azure, otevřete stránku přehledu databáze a na panelu nástrojů vyberte **Obnovit.** Vyberte záložní zdroj a vyberte bod zálohování v čase, ze kterého bude vytvořena nová databáze. 

  ![Snímek obrazovky s možnostmi obnovení databáze](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Chcete-li obnovit databázi spravovaných instancí do bodu v čase pomocí portálu Azure, otevřete stránku přehledu databáze a na panelu nástrojů vyberte **Obnovit.** Zvolte bod zálohování v čase, ze kterého bude vytvořena nová databáze. 

  ![Snímek obrazovky s možnostmi obnovení databáze](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Programově obnovit databázi ze zálohy naleznete [v tématu Programově provádění obnovení pomocí automatického zálohování](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Obnovení odstraněné databáze

Odstraněnou databázi můžete obnovit do doby odstranění nebo dřívějšího bodu v čase na stejném databázovém serveru SQL nebo ve stejné spravované instanci. Toho lze provést prostřednictvím portálu Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)nebo [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Odstraněnou databázi obnovíte vytvořením nové databáze ze zálohy.

> [!IMPORTANT]
> Pokud odstraníte server Azure SQL Database nebo spravovanou instanci, odstraní se také všechny jeho databáze a nelze je obnovit. Odstraněný server nebo spravovanou instanci nelze obnovit.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Obnovení odstraněné databáze pomocí portálu Azure

Odstraněné databáze z portálu Azure obnovíte ze serveru a prostředku instance.

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Chcete-li obnovit jednu nebo sdruženou odstraněnou databázi do doby odstranění pomocí portálu Azure, otevřete stránku s přehledem serveru a vyberte **Odstraněná databáze**. Vyberte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky s obnovením odstraněné databáze Azure SQL](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Chcete-li obnovit spravovanou databázi pomocí portálu Azure, otevřete stránku přehledu spravovaných instancí a vyberte **Odstraněná databáze**. Vyberte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky s databází odstraněných instancí Azure SQL](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Obnovení odstraněné databáze pomocí prostředí PowerShell

Pomocí následujících ukázkových skriptů obnovte odstraněnou databázi pro Azure SQL Database a spravovanou instanci pomocí PowerShellu.

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Ukázkový skript Prostředí PowerShell, který ukazuje, jak obnovit odstraněnou databázi Azure SQL, najdete [v tématu Obnovení databáze SQL pomocí PowerShellu](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Ukázkový skript prostředí PowerShell, který ukazuje, jak obnovit odstraněnou databázi instancí, najdete v tématu [Obnovení odstraněné databáze spravované instance pomocí prostředí PowerShell.](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Programově obnovení odstraněné databáze naleznete [v tématu Programově provádění obnovení pomocí automatického zálohování](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Geografické obnovení

Databázi SQL můžete obnovit na libovolném serveru v libovolné oblasti Azure z nejnovějších geograficky replikovaných záloh. Geografické obnovení používá jako zdroj geograficky replikovanou zálohu. Můžete požádat o geografické obnovení i v případě, že databáze nebo datové centrum je nepřístupné z důvodu výpadku.

Geografické obnovení je výchozí možnost obnovení, pokud databáze není k dispozici z důvodu incidentu v hostitelské oblasti. Databázi můžete obnovit na server v jakékoli jiné oblasti. Existuje prodleva mezi při zálohování a při geograficky replikovat do objektu blob Azure v jiné oblasti. V důsledku toho obnovená databáze může být až jednu hodinu za původní databáze. Následující obrázek znázorňuje obnovení databáze z poslední dostupné zálohy v jiné oblasti.

![Grafika geografické obnovy](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geografické obnovení pomocí portálu Azure

Na webu Azure Portal vytvoříte novou databázi jedné nebo spravované instance a vyberete dostupnou zálohu geografického obnovení. Nově vytvořená databáze obsahuje geograficky obnovená záložní data.

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Chcete-li geograficky obnovit jednu databázi SQL z portálu Azure v oblasti a serveru podle vašeho výběru, postupujte takto:

1. V **panelu vyberte** **přidat** > **vytvořit databázi SQL**. Na kartě **Základy** zadejte požadované informace.
2. Vyberte **Další nastavení**.
3. V **části Použít existující data**vyberte Možnost **Zálohovat**.
4. V **části Zálohování**vyberte zálohu ze seznamu dostupných záloh geografického obnovení.

    ![Snímek obrazovky s možnostmi vytvořit databázi SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Dokončete proces vytváření nové databáze ze zálohy. Když vytvoříte jednu databázi Azure SQL, obsahuje obnovenou zálohu geografického obnovení.

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Chcete-li geograficky obnovit databázi spravovaných instancí z portálu Azure do existující spravované instance v oblasti podle vašeho výběru, vyberte spravovanou instanci, ve které chcete databázi obnovit. Postupujte následovně:

1. Vyberte **novou databázi**.
2. Zadejte požadovaný název databáze.
3. V části **Použít existující data**vyberte **Zálohovat**.
4. Vyberte zálohu ze seznamu dostupných záloh geografického obnovení.

    ![Snímek obrazovky s možnostmi nové databáze](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Dokončete proces vytváření nové databáze. Když vytvoříte databázi instancí, obsahuje obnovenou zálohu geografického obnovení.

### <a name="geo-restore-by-using-powershell"></a>Geografické obnovení pomocí PowerShellu

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Skript prostředí PowerShell, který ukazuje, jak provádět geografické obnovení pro jednu databázi SQL, najdete v článku [Obnovení jediné databáze Azure SQL do dřívějšího bodu v čase pomocí PowerShellu.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Skript prostředí PowerShell, který ukazuje, jak provádět geografické obnovení pro databázi spravovaných instancí, najdete v článku [Obnovení databáze spravovaných instancí do jiné geografické oblasti pomocí prostředí PowerShell.](scripts/sql-managed-instance-restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Důležité informace o geografickém obnovení

V geograficky sekundární databázi nelze provést obnovení bodu v čase. To lze provést pouze v primární databázi. Podrobné informace o použití geografického obnovení k obnovení výpadku naleznete v [tématu Obnovení výpadku](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Geografické obnovení je nejzákladnější řešení zotavení po havárii, které je k dispozici v databázi SQL. Spoléhá se na automaticky vytvořené geograficky replikované zálohy s cílem bodu obnovení (RPO) rovnající se 1 hodině a odhadované době obnovení až 12 hodin. Nezaručuje, že cílová oblast bude mít schopnost obnovit databáze po regionálnívý výpadku, protože je pravděpodobné, že prudký nárůst poptávky. Pokud vaše aplikace používá relativně malé databáze a není důležité pro podnikání, geografické obnovení je vhodné řešení zotavení po havárii. Pro důležité obchodní aplikace, které vyžadují velké databáze a musí zajistit kontinuitu provozu, použijte [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md). Nabízí mnohem nižší RPO a cíl doby zotavení a kapacita je vždy zaručena. Další informace o možnostech kontinuity provozu naleznete v [tématu Přehled kontinuity provozu](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programově provádění obnovení pomocí automatických záloh

Můžete také použít Azure PowerShell nebo rozhraní REST API pro obnovení. Následující tabulky popisují sadu příkazů, které jsou k dispozici.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou do značné míry identické.

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Chcete-li obnovit samostatnou nebo sdruženou databázi, přečtěte si informace [o obnovení databáze AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Rutina | Popis |
  | --- | --- |
  | [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
  | [Get-AzSqlDeletedZálohování databáze](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
  | [Get-AzSqlDatabaseGeoZálohování](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Získá geograficky redundantní zálohu databáze. |
  | [Obnovit databázi AzSql](/powershell/module/az.sql/restore-azsqldatabase) |Obnoví databázi SQL. |

  > [!TIP]
  > Ukázkový skript prostředí PowerShell, který ukazuje, jak provést obnovení databáze v čase, najdete v [tématu Obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Chcete-li obnovit databázi spravovaných instancí, přečtěte si informace [o nástroji Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Rutina | Popis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Získá jednu nebo více spravovaných instancí. |
  | [Databáze get-azsqlinstance](/powershell/module/az.sql/get-azsqlinstancedatabase) | Získá databázi instancí. |
  | [Obnovit databázi instance AzSql](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Obnoví databázi instancí. |

### <a name="rest-api"></a>REST API

Chcete-li obnovit jednu nebo sdruženou databázi pomocí rozhraní REST API:

| rozhraní API | Popis |
| --- | --- |
| [REST (createMode=Zotavení)](https://docs.microsoft.com/rest/api/sql/databases) |Obnoví databázi. |
| [Získat stav vytvořit nebo aktualizovat databázi](https://docs.microsoft.com/rest/api/sql/operations) |Vrátí stav během operace obnovení. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>Jedna databáze Azure SQL

Chcete-li obnovit jednu nebo sdruženou databázi pomocí příkazového příkazu k webu Azure CLI, přečtěte si informace [o obnovení az SQL DB](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Databáze spravovaných instancí

Chcete-li obnovit databázi spravovaných instancí pomocí příkazového příkazu k webu Azure CLI, přečtěte si informace [o obnovení az sql midb](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Souhrn

Automatické zálohování chrání vaše databáze před chybami uživatelů a aplikací, náhodným odstraněním databáze a prodlouženým výpadkem. Tato integrovaná funkce je k dispozici pro všechny úrovně služeb a výpočetní velikosti.

## <a name="next-steps"></a>Další kroky

- [Přehled kontinuity provozu](sql-database-business-continuity.md)
- [Automatické zálohování databáze SQL Database](sql-database-automated-backups.md)
- [Dlouhodobé uchovávání](sql-database-long-term-retention.md)
- Informace o rychlejších možnostech obnovení naleznete [v tématu Aktivní geografická replikace](sql-database-active-geo-replication.md) nebo [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
