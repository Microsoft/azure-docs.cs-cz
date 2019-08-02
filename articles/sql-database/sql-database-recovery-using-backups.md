---
title: Obnovení databáze SQL Azure ze zálohy | Microsoft Docs
description: Přečtěte si o obnovení k určitému bodu v čase, které vám umožní vrátit Azure SQL Database k předchozímu bodu v čase (až 35 dní).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/30/2019
ms.openlocfilehash: 55d60ec332515fcfa3deb565a4a770027681537a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566975"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Obnovení databáze SQL Azure pomocí automatických záloh databáze

Ve výchozím nastavení se zálohy SQL Database ukládají v geograficky replikovaném úložišti objektů BLOB (RA-GRS). K dispozici jsou následující možnosti pro obnovení databáze pomocí [automatických záloh databáze](sql-database-automated-backups.md):

- Vytvoří novou databázi na stejném SQL Database serveru obnovenou v určitém časovém okamžiku v rámci doby uchování.
- Vytvoří databázi na stejném SQL Database serveru obnovenou do doby odstranění pro odstraněnou databázi.
- Vytvořte novou databázi na jakémkoli serveru SQL Database ve stejné oblasti, kterou jste obnovili do bodu nejaktuálnějšího zálohování.
- Vytvoří novou databázi na jakémkoli serveru SQL Database v jakékoli jiné oblasti, kterou jste obnovili do bodu posledního replikovaného zálohování.

Pokud jste nakonfigurovali [dlouhodobě dlouhodobé uchovávání záloh](sql-database-long-term-retention.md), můžete také vytvořit novou databázi z libovolné zálohy ltr na jakémkoli serveru SQL Database.

> [!IMPORTANT]
> Během obnovování nelze přepsat existující databázi.

Při použití úrovně služeb Standard nebo Premium se v obnovené databázi za následující podmínky vyskytnou dodatečné náklady na úložiště:

- Obnovení P11 – P15 na S4-S12 nebo P1 – P6, pokud je maximální velikost databáze větší než 500 GB.
- Obnova P1 – P6 na S4-S12, pokud je maximální velikost databáze větší než 250 GB.

Dodatečné náklady se icurred, když je maximální velikost obnovené databáze větší než velikost úložiště zahrnutá do úrovně služby a úrovně výkonu cílové databáze. Dodatečné úložiště zřízené nad zahrnutou částku se účtuje navíc. Podrobnosti o cenách dodatečného úložiště najdete na [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečná velikost využitého místa menší než velikost zahrnutého úložiště, můžete této dodatečné ceně zabránit tím, že nastavíte maximální velikost databáze na zahrnutou částku.

> [!NOTE]
> [Automatizované zálohy databáze](sql-database-automated-backups.md) se používají při vytváření [kopie databáze](sql-database-copy.md).

## <a name="recovery-time"></a>Čas obnovení

Doba obnovení pro obnovení databáze pomocí automatizovaného zálohování databáze má vliv na několik faktorů:

- Velikost databáze
- Velikost výpočetní databáze
- Počet zahrnutých protokolů transakcí
- Množství aktivity, které je třeba znovu přehrát pro obnovení do bodu obnovení
- Šířka pásma sítě, pokud se obnovení provádí v jiné oblasti
- Počet souběžných požadavků na obnovení zpracovávaných v cílové oblasti

V případě rozsáhlých a/nebo vysoce aktivních databází může obnovení trvat několik hodin. Pokud v oblasti dojde k dlouhodobému výpadku, je možné, že jsou zpracovávány velké počty požadavků geografického obnovení v jiných oblastech. Pokud existuje mnoho požadavků, doba obnovení se může zvýšit pro databáze v této oblasti. Většina obnovení databáze je dokončena za méně než 12 hodin.

U jednoho předplatného platí omezení počtu souběžných požadavků na obnovení.  Tato omezení platí pro jakoukoli kombinaci bodu v čase obnovení, geografické obnovení a obnovení ze zálohy dlouhodobého uchovávání dat:

| | **Maximální počet souběžných požadavků zpracovávaných** | **Maximální počet souběžných požadavků, které jsou odesílány** |
| :--- | --: | --: |
|Samostatná databáze (na předplatné)|10|60|
|Elastický fond (pro každý fond)|4|200|
||||

V současné době není k dispozici integrovaná metoda pro obnovení celého serveru. [Azure SQL Database: Úplný skript pro](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) obnovení serveru je příkladem toho, jak můžete tuto úlohu provést.

> [!IMPORTANT]
> Chcete-li provést obnovení pomocí automatizovaných záloh, musíte být členem role Přispěvatel SQL Server v předplatném nebo předplatným vlastníka předplatného – viz [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí webu Azure Portal, prostředí PowerShell nebo rozhraní REST API. Nelze použít jazyk Transact-SQL.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

K dřívějšímu bodu v čase můžete pomocí Azure Portal, PowerShellu nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases)obnovit samostatnou databázi ve fondu nebo instanci [](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase). Požadavek může pro obnovenou databázi zadat libovolnou úroveň služby nebo výpočetní velikost. Ujistěte se, že máte na serveru dostatek prostředků, na které obnovujete databázi. Po dokončení bude nová databáze vytvořena na stejném serveru jako původní databáze. Obnovená databáze se bude účtovat za normálních sazeb na základě její úrovně služeb a výpočetní velikosti. Neúčtují se vám poplatky, dokud se obnovení databáze nedokončí.

Obecně obnovuje databázi do dřívějšího bodu pro účely obnovení. Obnovenou databázi můžete zacházet jako s náhradou původní databáze nebo ji použít jako zdrojová data k aktualizaci původní databáze.

- **Nahrazení databáze**

  Pokud je obnovená databáze zamýšlena jako náhrada původní databáze, měli byste zadat výpočetní velikost a úroveň služby původní databáze. Pak můžete původní databázi přejmenovat a pomocí příkazu [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) v T-SQL pojmenovat obnovenou databázi původní název.

- **Obnovení dat**

  Pokud plánujete načíst data z obnovené databáze pro obnovení z chyby uživatele nebo aplikace, je nutné napsat a spustit skript pro obnovení dat, který extrahuje data z obnovené databáze a platí pro původní databázi. I když operace obnovení může trvat dlouhou dobu, obnovovaná databáze se zobrazí v seznamu databáze během procesu obnovení. Pokud databázi odstraníte během obnovování, operace obnovení se zruší a nebude se vám účtovat databáze, která obnovení nedokončila.

Chcete-li obnovit jednu, sdruženou nebo instanci databáze k určitému bodu v čase pomocí Azure Portal, otevřete stránku pro vaši databázi a klikněte na tlačítko **obnovit** na panelu nástrojů.

![obnovení bodu v čase](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Pokud chcete programově obnovit databázi ze zálohy, přečtěte si téma [programové provádění obnovení pomocí automatických záloh](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups) .

## <a name="deleted-database-restore"></a>Obnovení databáze se odstranilo

Odstraněnou databázi můžete obnovit na čas odstranění nebo předchozí bod v čase na stejném serveru SQL Database pomocí Azure Portal, [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)nebo [Rest (CreateMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Odstraněnou [databázi můžete na spravované instanci obnovit pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Vzorový skript PowerShellu, který ukazuje, jak obnovit odstraněnou databázi, najdete v tématu [obnovení databáze SQL pomocí PowerShellu](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Odstraníte-li instanci serveru Azure SQL Database, budou odstraněny také všechny jeho databáze a nelze je obnovit. Obnovení odstraněného serveru se momentálně nepodporuje.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Odstranění obnovení databáze pomocí Azure Portal

Chcete-li obnovit odstraněnou databázi pomocí Azure Portal, otevřete stránku serveru aplikace a v oblasti operace klikněte na položku **odstraněné databáze**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Postup při programovém obnovení odstraněné databáze najdete v tématu [programové provádění obnovení pomocí automatických záloh](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups) .

## <a name="geo-restore"></a>Geografické obnovení

SQL Database můžete obnovit na jakémkoli serveru v libovolné oblasti Azure z posledních geograficky replikovaných záloh. Geografické obnovení používá jako zdroj geograficky replikovanou zálohu. Může být požadován i v případě, že je databáze nebo datacentrum nedostupné kvůli výpadku.

Geografické obnovení je výchozí možností obnovení v případě, že databáze není k dispozici z důvodu incidentu v oblasti hostování. Databázi můžete obnovit na server v libovolné jiné oblasti. Doba, po kterou se zálohování provádí, a když se geograficky replikují do objektu blob Azure v jiné oblasti, nastane zpoždění. V důsledku toho může být obnovená databáze až o jednu hodinu za databází orignal. Následující ilustrace znázorňuje obnovení databáze z poslední dostupné zálohy v jiné oblasti.

![geografické obnovení](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Vzorový skript PowerShellu ukazující, jak provést geografické obnovení, najdete v tématu [obnovení databáze SQL pomocí PowerShellu](scripts/sql-database-restore-database-powershell.md).

Obnovení k určitému bodu v čase v geograficky sekundárním není aktuálně podporováno. Obnovení k určitému bodu v čase lze provést pouze v primární databázi. Podrobné informace o použití geografického obnovení k zotavení po výpadku najdete v tématu [obnovení](sql-database-disaster-recovery.md)při výpadku.

> [!IMPORTANT]
> Geografické obnovení je nejzákladnější řešení zotavení po havárii dostupné v SQL Database. Spoléhá se na automaticky vytvořená geograficky replikovaná zálohování s cílem RPO = 1 hodina a odhadovanou dobu obnovení až 12 hodin. Nezaručuje, že cílová oblast bude mít kapacitu pro obnovení vašich databází po regionálních ourage, protože bude pravděpodobně ostřejší nárůst poptávky. Pro nepodnikovou aplikaci, která používá relativně malé databáze, je geograficky obnoveno vhodné řešení pro zotavení po havárii. U důležitých podnikových aplikací, které používají velké databáze a které musí zajistit kontinuitu podnikových služeb, byste měli použít [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md). Nabízí mnohem nižší bod RPO a RTO a kapacita je vždycky zaručená. Další informace o volbách pro provozní kontinuitu najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Geografické obnovení pomocí Azure Portal

Chcete-li geograficky obnovit databázi během [období uchování modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo [Vcore období uchování](sql-database-service-tiers-vcore.md) pomocí Azure Portal, otevřete stránku databáze SQL a potom klikněte na tlačítko **Přidat**. V textovém poli **Vybrat zdroj** vyberte **zálohovat**. Zadejte zálohu, ze které se má obnovení provést v oblasti a na serveru dle vašeho výběru.

> [!Note]
> Geografické obnovení pomocí Azure Portal není k dispozici ve spravované instanci. Místo toho prosím použijte PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programové provádění obnovení pomocí automatizovaných záloh

Jak je uvedeno výše, kromě Azure Portal lze obnovení databáze provést programově pomocí Azure PowerShell nebo REST API. V následujících tabulkách jsou popsány sady příkazů, které jsou k dispozici.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

- Informace o obnovení samostatné databáze nebo databáze ve fondu najdete v tématu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Rutiny | Popis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Získá geograficky redundantní zálohu databáze. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Obnoví databázi SQL. |

  > [!TIP]
  > Vzorový skript PowerShellu ukazující, jak provést obnovení databáze k určitému bodu v čase, najdete v tématu [obnovení databáze SQL pomocí PowerShellu](scripts/sql-database-restore-database-powershell.md).

- Chcete-li obnovit databázi spravované instance, přečtěte si téma [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Rutiny | Popis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Získá jednu nebo víc spravovaných instancí. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Načte databáze instancí. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Obnoví databázi instance. |

### <a name="rest-api"></a>REST API

Postup obnovení jedné nebo sdružené databáze pomocí REST API:

| rozhraní API | Popis |
| --- | --- |
| [REST (createMode = obnovení)](https://docs.microsoft.com/rest/api/sql/databases) |Obnoví databázi. |
| [Získat stav databáze pro vytvoření nebo aktualizaci](https://docs.microsoft.com/rest/api/sql/operations) |Vrátí stav během operace obnovení. |

### <a name="azure-cli"></a>Azure CLI

- Pokud chcete obnovit jednu nebo sdruženou databázi pomocí rozhraní příkazového řádku Azure, přečtěte si téma [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).
- Postup obnovení spravované instance pomocí Azure CLI najdete v tématu [AZ SQL MIDB Restore](/cli/azure/sql/midb#az-sql-midb-restore) .

## <a name="summary"></a>Souhrn

Automatické zálohování chrání vaše databáze před chybami uživatelů a aplikací, náhodným odstraněním databáze a prodlouženými výpadky. Tato integrovaná funkce je k dispozici pro všechny úrovně služeb a výpočetní velikosti.

## <a name="next-steps"></a>Další postup

- Přehled provozní kontinuity a scénáře najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md).
- Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání najdete v tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).
- Další informace o možnostech rychlejšího obnovení najdete v tématu [Aktivní geografická replikace](sql-database-active-geo-replication.md) nebo [skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md).
