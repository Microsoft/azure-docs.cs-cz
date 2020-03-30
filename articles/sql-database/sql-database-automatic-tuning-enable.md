---
title: Povolení automatického ladění
description: Automatické ladění v azure sql database můžete snadno povolit.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299324"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Povolení automatického ladění pro sledování dotazů a zlepšení výkonu pracovního vytížení

Azure SQL Database je automaticky spravovaná datová služba, která neustále monitoruje vaše dotazy a identifikuje akci, kterou můžete provést za účelem zlepšení výkonu úlohy. Můžete zkontrolovat doporučení a ručně je použít, nebo nechat Azure SQL Database automaticky použít nápravná opatření – to se označuje jako **režim automatického ladění**.

Automatické ladění lze povolit na úrovni serveru nebo databáze prostřednictvím [portálu Azure](sql-database-automatic-tuning-enable.md#azure-portal)Portal , volání [rozhraní REST API](sql-database-automatic-tuning-enable.md#rest-api) a příkazů [T-SQL.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> Pro spravovanou instanci lze podporovanou možnost FORCE_LAST_GOOD_PLAN nakonfigurovat pouze prostřednictvím [T-SQL.](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) Možnosti konfigurace založené na portálu a automatické optimalizace indexu popsané v tomto článku se nevztahují na spravovanou instanci.

> [!NOTE]
> Konfigurace možností automatického ladění prostřednictvím šablony ARM (Azure Resource Manager) není v tuto chvíli podporována.

## <a name="enable-automatic-tuning-on-server"></a>Povolení automatického ladění na serveru

Na úrovni serveru můžete zdědit konfiguraci automatického ladění z "Výchozí nastavení Azure" nebo nedědit konfiguraci. Výchozí hodnoty Azure jsou FORCE_LAST_GOOD_PLAN je povolená, CREATE_INDEX je povolená a DROP_INDEX je zakázána.

> [!IMPORTANT]
> Od března 2020 změny výchozích hodnot Azure pro automatické ladění se projeví takto:
>
> - Nové výchozí hodnoty Azure budou FORCE_LAST_GOOD_PLAN = povoleno, CREATE_INDEX = zakázáno a DROP_INDEX = zakázáno.
> - Existující servery bez nakonfigurovaných předvoleb automatického ladění se automaticky nakonfigurují na INHERIT nové výchozí hodnoty Azure. To platí pro všechny zákazníky, kteří mají aktuálně nastavení serveru pro automatické ladění v nedefinovaném stavu.
> - Nové vytvořené servery se automaticky nakonfigurují na INHERIT nové výchozí hodnoty Azure (na rozdíl od dřívějších, kdy byla konfigurace automatického ladění v nedefinovaném stavu při vytváření nového serveru).

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit automatické ladění na logickém **serveru**Azure SQL Database , přejděte na server na webu Azure Portal a v yberte **Automatické ladění** v nabídce.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Vezměte prosím na vědomí, **že možnost DROP_INDEX** v tomto okamžiku není kompatibilní s aplikacemi pomocí přepínání oddílů a rady pro index a v těchto případech by neměla být povolena. Uvolnění nepoužívaných indexů není podporováno pro úrovně služeb Premium a Business Critical.
>

Vyberte možnosti automatického ladění, které chcete povolit, a vyberte **Použít**.

Možnosti automatického ladění na serveru jsou použity pro všechny databáze na tomto serveru. Ve výchozím nastavení všechny databáze dědí konfiguraci z nadřazeného serveru, ale to může být přepsáno a určeno pro každou databázi jednotlivě.

### <a name="rest-api"></a>REST API

Další informace o použití rozhraní REST API k povolení automatického ladění na serveru naleznete v tématu [SQL Server Automatic tuning UPDATE and GET HTTP methods](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Povolení automatického ladění v jednotlivých databázích

Azure SQL Database umožňuje individuálně určit konfiguraci automatického ladění pro každou databázi. Na úrovni databáze můžete zdědit konfiguraci automatického ladění z nadřazeného serveru "Výchozí Azure" nebo nedědit konfiguraci. Výchozí hodnoty Azure jsou nastaveny na FORCE_LAST_GOOD_PLAN je povolená, CREATE_INDEX je povolená a DROP_INDEX je zakázána.

> [!TIP]
> Obecným doporučením je spravovat konfiguraci automatického ladění na **úrovni serveru,** aby bylo možné automaticky použít stejné nastavení konfigurace na každou databázi. Automatické ladění na jednotlivé databázi nakonfigurujte pouze v případě, že potřebujete, aby tato databáze měla jiné nastavení než ostatní, kteří dědí nastavení ze stejného serveru.
>

### <a name="azure-portal"></a>portál Azure

Chcete-li povolit automatické ladění v **jedné databázi**, přejděte do databáze na webu Azure Portal a vyberte **možnost Automatické ladění**.

Individuální nastavení automatického ladění lze samostatně konfigurovat pro každou databázi. Můžete ručně nakonfigurovat jednotlivé možnosti automatického ladění nebo určit, že možnost zdědí její nastavení ze serveru.

![Databáze](./media/sql-database-automatic-tuning-enable/database.png)

Vezměte prosím na vědomí, že možnost DROP_INDEX v tomto okamžiku není kompatibilní s aplikacemi pomocí přepínání oddílů a rady pro index a v těchto případech by neměla být povolena.

Po výběru požadované konfigurace klepněte na **tlačítko Použít**.

### <a name="rest-api"></a>Rozhraní API pro odpočinek

Další informace o použití rozhraní REST API k povolení automatického ladění na jedné databázi naleznete v [tématu SQL Database Automatic tuning UPDATE and GET HTTP methods](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Chcete-li povolit automatické ladění na jedné databázi prostřednictvím T-SQL, připojte se k databázi a spusťte následující dotaz:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Nastavení automatického ladění na AUTO použije výchozí nastavení Azure. Nastavení na INHERIT, automatické ladění konfigurace bude zděděna z nadřazeného serveru. Výběrem vlastní, budete muset ručně nakonfigurovat automatické ladění.

Chcete-li nakonfigurovat jednotlivé možnosti automatického ladění pomocí t-SQL, připojte se k databázi a spusťte dotaz, jako je tento:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Nastavení matování jednotlivých možnostzapnout, přepíše všechna nastavení, které databáze zděděné a povolit možnost ladění. Nastavení na OFF, bude také přepsat všechna nastavení, která databáze zděděné a zakázat možnost ladění. Možnost automatického ladění, pro kterou je zadána funkce DEFAULT, zdědí konfiguraci automatického ladění z nastavení na úrovni serveru.  

> [!IMPORTANT]
> V případě [aktivní geografické replikace](sql-database-auto-failover-group.md)je třeba automatické ladění konfigurovat pouze v primární databázi. Automaticky použité akce optimalizace, jako jsou například index vytvořit nebo odstranit budou automaticky replikovány do sekundární jen pro čtení. Pokus o povolení automatického ladění prostřednictvím T-SQL v sekundárním jazyce jen pro čtení bude mít za následek selhání, protože má jinou konfiguraci optimalizace na sekundární mj.
>

Najděte naše další sousedící t-SQL možnosti konfigurace automatického ladění, viz [ALTER DATABASE SET Options (Transact-SQL) pro SQL Database server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Zakázáno systémem

Automatické ladění sleduje všechny akce, které provede v databázi a v některých případech může určit, že automatické ladění nemůže správně fungovat v databázi. V takovém případě bude možnost ladění zakázána systémem. Ve většině případů k tomu dochází, protože úložiště dotazů není povoleno nebo je ve stavu jen pro čtení v konkrétní databázi.

## <a name="permissions"></a>Oprávnění

Vzhledem k tomu, že automatické ladění je funkce Azure, budete muset použít integrované role RBAC Azure. Použití ověřování SQL nebude stačit k použití funkce z webu Azure Portal.

Chcete-li použít automatické ladění, minimální požadované oprávnění k udělení uživateli je azure je vestavěná role [přispěvatele SQL DB.](../role-based-access-control/built-in-roles.md#sql-db-contributor) Můžete také zvážit použití vyšší chutin role, jako jsou SQL Server Přispěvatel, Přispěvatel a vlastník.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurace automatického ladění e-mailových oznámení

Viz [průvodce automatickým laděním e-mailových oznámení.](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Další kroky

* Další informace o automatickém ladění a o tom, jak vám může pomoci zlepšit výkon, najdete v [článku Automatické ladění.](sql-database-automatic-tuning.md)
* Přehled doporučení pro výkon azure sql database u článku najdete v článku Doporučení pro [výkon.](sql-database-advisor.md)
* Informace o zobrazení dopadu na výkon vašich hlavních dotazů najdete v tématu [Přehledy výkonu dotazů.](sql-database-query-performance.md)
