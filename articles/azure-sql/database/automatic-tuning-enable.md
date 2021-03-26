---
title: Povolení automatického ladění
description: Automatické ladění databáze můžete povolit snadno pomocí Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: 1362d8c1f15b64b9d76b28fd354cdae8919504b0
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558274"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Povolit automatické ladění v Azure Portal pro monitorování dotazů a zlepšení výkonu úloh
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database automaticky spravuje datové služby, které průběžně monitorují vaše dotazy a identifikují akci, kterou můžete provést za účelem zvýšení výkonu úloh. Můžete zkontrolovat doporučení a ručně je použít, nebo nechat Azure SQL Database automaticky použít opravné akce – jedná se o **Automatický režim optimalizace**.

Automatické ladění lze povolit na serveru nebo na úrovni databáze prostřednictvím:

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) volání
- Příkazy [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)

> [!NOTE]
> U spravované instance Azure SQL je možné podporovanou možnost FORCE_LAST_GOOD_PLAN nakonfigurovat jenom pomocí [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Možnosti konfigurace založené na Azure Portal a automatické ladění indexu popsané v tomto článku se nevztahují na spravovanou instanci Azure SQL.

> [!NOTE]
> Konfigurace možností automatického ladění prostřednictvím šablony ARM (Azure Resource Manager) není v tuto chvíli podporována.

## <a name="enable-automatic-tuning-on-server"></a>Povolit automatické ladění na serveru

Na úrovni serveru můžete zvolit dědění konfigurace automatického ladění z "výchozí hodnoty Azure" nebo Nedědit konfiguraci. Ve výchozím nastavení Azure jsou povolené FORCE_LAST_GOOD_PLAN, CREATE_INDEX je zakázané a DROP_INDEX je zakázaná.

> [!IMPORTANT]
> Od března 2020 nové výchozí hodnoty Azure pro automatické ladění jsou následující:
>
> - FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled a DROP_INDEX = Disabled.
> - Stávající servery bez nakonfigurovaného automatického naladění předvoleb se automaticky nakonfigurují tak, aby ZDĚDILy výchozí hodnoty Azure. To platí pro všechny zákazníky, kteří aktuálně mají nastavení serveru pro automatické ladění v nedefinovaném stavu.
> - Nově vytvořené servery se automaticky nakonfigurují tak, aby ZDĚDILy výchozí hodnoty Azure (na rozdíl od dřív, kdy byla konfigurace automatického ladění v nedefinovaném stavu při vytváření nového serveru).

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit automatické ladění na [serveru](logical-servers.md) v Azure SQL Database, přejděte na server v Azure Portal a pak v nabídce vyberte **Automatické ladění** .

![Snímek obrazovky zobrazuje automatické ladění v Azure Portal, kde můžete použít možnosti pro server.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Upozorňujeme, že možnost **DROP_INDEX** v tuto chvíli není kompatibilní s aplikacemi, které používají přepínání oddílů a pomocné parametry indexu a neměly by být v těchto případech povolené. Vyřazování nepoužívaných indexů se u úrovní služeb Premium a Pro důležité obchodní informace nepodporuje.

Vyberte možnosti automatického ladění, které chcete povolit, a vyberte **použít**.

Možnosti automatického ladění na serveru se aplikují na všechny databáze na tomto serveru. Ve výchozím nastavení dědí všechny databáze konfiguraci ze svého nadřazeného serveru, ale dá se přepsat a zadat pro každou databázi samostatně.

### <a name="rest-api"></a>REST API

Další informace o použití REST API k povolení automatického ladění na **serveru** najdete v tématu [aktualizace automatického ladění serveru a získání metod http](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Povolit automatické ladění v individuální databázi

Azure SQL Database vám umožní individuálně zadat konfiguraci automatického ladění pro každou databázi. Na úrovni databáze můžete zvolit dědění konfigurace automatického ladění z nadřazeného serveru, "výchozí nastavení Azure" nebo Nedědit konfiguraci. Ve výchozím nastavení Azure je povolená možnost FORCE_LAST_GOOD_PLAN, CREATE_INDEX je zakázaná a DROP_INDEX je zakázaná.

> [!TIP]
> Obecně se doporučuje spravovat konfiguraci automatického ladění na **úrovni serveru** , aby bylo možné v každé databázi automaticky použít stejné nastavení konfigurace. Automatické ladění můžete nakonfigurovat v individuální databázi jenom v případě, že potřebujete, aby tato databáze měla různá nastavení, než ostatní dědí nastavení ze stejného serveru.

### <a name="azure-portal"></a>portál Azure

Pokud chcete povolit automatické ladění pro izolovanou **databázi**, přejděte do databáze v Azure Portal a vyberte **Automatické ladění**.

Jednotlivá nastavení automatického ladění je možné pro každou databázi nakonfigurovat samostatně. Můžete ručně nakonfigurovat jednotlivou možnost automatického ladění nebo určit, že nastavení zdědí ze serveru.

![Snímek obrazovky zobrazuje automatické ladění v Azure Portal, kde můžete použít možnosti pro jednu databázi.](./media/automatic-tuning-enable/database.png)

Upozorňujeme, že možnost DROP_INDEX v tuto chvíli není kompatibilní s aplikacemi, které používají přepínání oddílů a pomocné parametry indexu a neměly by být v těchto případech povolené.

Po výběru požadované konfigurace klikněte na **použít**.

### <a name="rest-api"></a>REST API

Další informace o použití REST API k povolení automatického ladění pro jednu databázi najdete v tématu [Azure SQL Database aktualizace automatického ladění a získání metod http](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Pokud chcete povolit automatické ladění pro izolovanou databázi prostřednictvím T-SQL, připojte se k databázi a spusťte následující dotaz:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Nastavení automatického ladění na automatické bude platit pro výchozí nastavení Azure. Když nastavení nastavíte na ZDĚDĚNé, bude se dědit konfigurace automatického ladění z nadřazeného serveru. Zvolíte-li možnost vlastní, budete muset automatické ladění nakonfigurovat ručně.

Pokud chcete nakonfigurovat jednotlivé možnosti automatického ladění prostřednictvím T-SQL, připojte se k databázi a spusťte dotaz, jako je například tento:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Nastavení jednotlivé možnosti optimalizace na ZAPNUTo přepíše všechna nastavení, která databáze zdědila, a povolí možnost optimalizace. Nastavením na hodnotu Vypnuto dojde také k přepsání všech nastavení, která databáze zdědila, a zakázání možnosti optimalizace. Možnost automatického ladění, pro kterou je zadaná výchozí hodnota, zdědí konfiguraci automatického ladění z nastavení na úrovni serveru.  

> [!IMPORTANT]
> V případě [aktivní geografické replikace](auto-failover-group-overview.md)je nutné nakonfigurovat automatické ladění pouze v primární databázi. Automaticky použité akce optimalizace, například index Create nebo DELETE, se automaticky replikují do sekundárního režimu určeného jen pro čtení. Při pokusu o povolení automatického ladění přes T-SQL u sekundárního počítače jen pro čtení dojde k selhání, protože v sekundárním počítači určeném jen pro čtení není podporovaná jiná konfigurace ladění.
>

Další možnosti sousedit s T-SQL pro konfiguraci automatického ladění najdete v tématu věnovaném [možnostem ALTER DATABASE set (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="automated-recommendation-management-is-disabled"></a>Automatizovaná správa doporučení je zakázaná.

V případě chybových zpráv, které automatizovaná správa doporučení zakázala, nebo je jenom zakázaná systémem, nejběžnější příčiny:
- Úložiště dotazů není povoleno, nebo
- Úložiště dotazů je v režimu jen pro čtení pro zadanou databázi nebo
- Úložiště dotazů bylo zastaveno, protože používalo přidělený prostor úložiště.

K vyřešení tohoto problému je možné zvážit tyto kroky:
- Vyčistěte úložiště dotazů nebo upravte dobu uchování dat na hodnotu "auto" pomocí T-SQL. Podívejte se, jak [nakonfigurovat Doporučené zásady uchovávání a zachytávání pro úložiště dotazů](./query-performance-insight-use.md#recommended-retention-and-capture-policy).
- Použijte SQL Server Management Studio (SSMS) a postupujte podle následujících kroků:
  - Připojit k Azure SQL Database
  - Klikněte pravým tlačítkem na databázi.
  - Přejít na vlastnosti a kliknout na úložiště dotazů
  - Změňte režim operace na Read-Write
  - Změňte režim zachycení úložiště na automaticky.
  - Změnit režim čištění založený na velikosti na automatické

### <a name="permissions"></a>Oprávnění

Vzhledem k tomu, že automatické ladění je funkce Azure, budete muset použít předdefinované role Azure. Použití pouze ověřování SQL nebude stačit k použití funkce z Azure Portal.

Chcete-li použít automatické ladění, je minimální požadovaná oprávnění k udělení uživateli předdefinovaná [SQL Database role přispěvatele](../../role-based-access-control/built-in-roles.md#sql-db-contributor) Azure. Můžete také zvážit použití vyšších rolí oprávnění, jako je SQL Server přispěvatel, přispěvatel spravované instance SQL, přispěvatel a vlastník.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurace e-mailových oznámení automatického ladění

Pokud chcete dostávat automatizovaná e-mailová oznámení o doporučeních provedených automatickým laděním, přečtěte si příručku pro [e-mailová oznámení](automatic-tuning-email-notifications-configure.md)

## <a name="next-steps"></a>Další kroky

- Další informace o automatickém ladění a o tom, jak vám může pomoci vylepšit výkon, najdete v článku věnovaném [automatickému ladění](automatic-tuning-overview.md) .
- Přehled Azure SQL Databasech doporučení týkajících se výkonu najdete v tématu [doporučení pro výkon](database-advisor-implement-performance-recommendations.md) .
- V tématu [Přehled výkonu dotazů](query-performance-insight-use.md) najdete informace o tom, jak zobrazit dopad vašich dotazů na výkon.