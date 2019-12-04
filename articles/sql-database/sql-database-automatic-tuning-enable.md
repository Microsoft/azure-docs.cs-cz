---
title: Povolení automatického ladění
description: Automatické ladění můžete v Azure SQL Database povolit snadno.
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
ms.openlocfilehash: bdd33d85ee0aac4808c343af088d4db1a0dc963e
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767768"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Povolení automatického ladění pro monitorování dotazů a zlepšení výkonu úloh

Azure SQL Database je automaticky spravovaná datová služba, která nepřetržitě monitoruje vaše dotazy a identifikuje akci, kterou můžete provést ke zvýšení výkonu vašich úloh. Můžete zkontrolovat doporučení a ručně je použít, nebo nechat Azure SQL Database automaticky použít opravné akce – jedná se o **Automatický režim optimalizace**.

Automatické ladění lze povolit na serveru nebo na úrovni databáze prostřednictvím [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) volání a příkazů [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) .

> [!NOTE]
> V případě spravované instance lze podporovanou možnost FORCE_LAST_GOOD_PLAN nakonfigurovat pouze pomocí [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Možnosti konfigurace založené na portálu a automatického ladění indexu popsané v tomto článku se nevztahují na spravovanou instanci.

> [!NOTE]
> Konfigurace možností automatického ladění prostřednictvím šablony ARM (Azure Resource Manager) není v tuto chvíli podporována.

## <a name="enable-automatic-tuning-on-server"></a>Povolit automatické ladění na serveru

Na úrovni serveru můžete zvolit dědění konfigurace automatického ladění z "výchozí hodnoty Azure" nebo Nedědit konfiguraci. Ve výchozím nastavení Azure jsou povolené FORCE_LAST_GOOD_PLAN, CREATE_INDEX je povolené a DROP_INDEX je zakázané.

### <a name="azure-portal"></a>Portál Azure

Pokud chcete povolit automatické ladění Azure SQL Database logického **serveru**, přejděte na server v Azure Portal a pak v nabídce vyberte **Automatické ladění** .

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Upozorňujeme, že možnost **DROP_INDEX** v tuto chvíli není kompatibilní s aplikacemi, které používají přepínání oddílů a pomocné parametry indexu a neměly by být v těchto případech povolené. Vyřazování nepoužívaných indexů se u úrovní služeb Premium a Pro důležité obchodní informace nepodporuje.
>

Vyberte možnosti automatického ladění, které chcete povolit, a vyberte **použít**.

Možnosti automatického ladění na serveru se aplikují na všechny databáze na tomto serveru. Ve výchozím nastavení dědí všechny databáze konfiguraci ze svého nadřazeného serveru, ale dá se přepsat a zadat pro každou databázi samostatně.

### <a name="rest-api"></a>Rozhraní REST API

Další informace o použití REST API k povolení automatického ladění na serveru najdete v tématu [SQL Server aktualizace automatického ladění a získání metod http](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Povolit automatické ladění v individuální databázi

Azure SQL Database vám umožní individuálně zadat konfiguraci automatického ladění pro každou databázi. Na úrovni databáze můžete zvolit dědění konfigurace automatického ladění z nadřazeného serveru, "výchozí nastavení Azure" nebo Nedědit konfiguraci. Ve výchozím nastavení Azure je povolené FORCE_LAST_GOOD_PLAN, CREATE_INDEX je povolené a DROP_INDEX je zakázané.

> [!TIP]
> Obecně se doporučuje spravovat konfiguraci automatického ladění na **úrovni serveru** , aby bylo možné v každé databázi automaticky použít stejné nastavení konfigurace. Automatické ladění můžete nakonfigurovat v individuální databázi jenom v případě, že potřebujete, aby tato databáze měla různá nastavení, než ostatní dědí nastavení ze stejného serveru.
>

### <a name="azure-portal"></a>Portál Azure

Pokud chcete povolit automatické ladění pro izolovanou **databázi**, přejděte do databáze v Azure Portal a vyberte **Automatické ladění**.

Jednotlivá nastavení automatického ladění je možné pro každou databázi nakonfigurovat samostatně. Můžete ručně nakonfigurovat jednotlivou možnost automatického ladění nebo určit, že nastavení zdědí ze serveru.

![Databáze](./media/sql-database-automatic-tuning-enable/database.png)

Upozorňujeme, že možnost DROP_INDEX v tuto chvíli není kompatibilní s aplikacemi, které používají přepínání oddílů a pomocné parametry indexu a neměly by být v těchto případech povolené.

Po výběru požadované konfigurace klikněte na **použít**.

### <a name="rest-api"></a>Rozhraní REST API

Další informace o použití REST API k povolení automatického ladění pro izolovanou databázi najdete v tématu [SQL Database aktualizace automatického ladění a získání metod http](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Pokud chcete povolit automatické ladění pro izolovanou databázi prostřednictvím T-SQL, připojte se k databázi a spusťte následující dotaz:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Nastavení automatického ladění na automatické bude platit pro výchozí nastavení Azure. Když nastavení nastavíte na ZDĚDĚNé, bude se dědit konfigurace automatického ladění z nadřazeného serveru. Zvolíte-li možnost vlastní, budete muset automatické ladění nakonfigurovat ručně.

Pokud chcete nakonfigurovat jednotlivé možnosti automatického ladění prostřednictvím T-SQL, připojte se k databázi a spusťte dotaz, jako je například tento:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Když nastavíte možnost jednotlivého ladění na ZAPNUTo, přepíše se všechna nastavení, která databáze zdědila, a povolení možnosti optimalizace. Nastavení je vypnuto, přepíše také všechna nastavení, která databáze zdědila, a zakáže možnost optimalizace. Možnost automatického ladění, pro kterou je zadaná výchozí hodnota, zdědí konfiguraci automatického ladění z nastavení na úrovni serveru.  

> [!IMPORTANT]
> V případě [aktivní geografické replikace](sql-database-auto-failover-group.md)je nutné nakonfigurovat automatické ladění pouze v primární databázi. Automaticky použité akce optimalizace, například index Create nebo DELETE, se automaticky replikují do sekundárního režimu určeného jen pro čtení. Při pokusu o povolení automatického ladění přes T-SQL u sekundárního počítače jen pro čtení dojde k selhání, protože v sekundárním počítači určeném jen pro čtení není podporovaná jiná konfigurace ladění.
>

Další možnosti sousedit s T-SQL pro konfiguraci automatického ladění najdete v tématu [ALTER DATABASE set Options (Transact-SQL) pro SQL Database Server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Zakázáno systémem

Automatické ladění sleduje všechny akce, které v databázi provádí, a v některých případech může zjistit, že automatické ladění nemůže v databázi správně fungovat. V této situaci systém zakáže možnost vyladění. Ve většině případů k tomu dochází, protože úložiště dotazů není povolené nebo je v konkrétní databázi ve stavu jen pro čtení.

## <a name="permissions"></a>Oprávnění

Protože automatické ladění je funkce Azure, budete muset použít předdefinované role RBAC v Azure. Použití pouze ověřování SQL nebude stačit k používání funkce z Azure Portal.

Chcete-li použít automatické ladění, je minimální požadovaná oprávnění pro udělení uživateli předdefinovaná role [přispěvatele databáze SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) Azure. Můžete také zvážit použití vyšších rolí oprávnění, jako je SQL Server přispěvatel, přispěvatel a vlastník.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurace e-mailových oznámení automatického ladění

Viz Průvodce [automatickým laděním e-mailových oznámení](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Další kroky

* Další informace o automatickém ladění a o tom, jak vám může pomoci vylepšit výkon, najdete v článku věnovaném [automatickému ladění](sql-database-automatic-tuning.md) .
* Přehled Azure SQL Databasech doporučení týkajících se výkonu najdete v tématu [doporučení pro výkon](sql-database-advisor.md) .
* V tématu [Přehled výkonu dotazů](sql-database-query-performance.md) najdete informace o tom, jak zobrazit dopad vašich dotazů na výkon.
