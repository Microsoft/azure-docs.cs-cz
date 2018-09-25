---
title: Povolení automatického ladění pro službu Azure SQL Database | Dokumentace Microsoftu
description: Můžete povolit automatické vyladění Azure SQL Database snadno.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 4f043cfc9c85513abe16bde38c5cac6e16e34af9
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063315"
---
# <a name="enable-automatic-tuning"></a>Povolení automatického ladění

Azure SQL Database je automaticky spravovanou datovou službu, která neustále monitoruje dotazy a identifikuje akce, které můžete provést pro zvýšení výkonu vašich úloh. Můžete zkontrolovat doporučení a ručně aplikovat nebo nechat službu Azure SQL Database automaticky použít nápravná opatření – to se označuje jako **režim automatické optimalizace**.

Automatické ladění je možné povolit na serveru nebo na úrovni databáze pomocí [webu Azure portal](sql-database-automatic-tuning-enable.md#azure-portal), [rozhraní REST API](sql-database-automatic-tuning-enable.md#rest-api) volání a [T-SQL](sql-database-automatic-tuning-enable.md#t-sql) příkazy.

## <a name="enable-automatic-tuning-on-server"></a>Povolení automatického ladění na serveru
Na úrovni serveru můžete dědí konfiguraci automatického ladění z "Azure je výchozí" nebo dědí konfiguraci. Azure výchozími hodnotami jsou povolené FORCE_LAST_GOOD_PLAN, CREATE_INDEX je povolená a DROP_INDEX je zakázaná.

### <a name="azure-portal"></a>portál Azure
Povolit automatické vyladění Azure SQL Database logické **server**, přejděte na server, na webu Azure portal a potom vyberte **automatické ladění** v nabídce.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Pamatujte, že **DROP_INDEX** v tuto chvíli není kompatibilní s aplikací s použitím pomocné parametry přepnutí a index oddílu a v těchto případech by se neměla povolovat.
>

Vyberte možnosti automatického ladění, které chcete povolit a vyberte **použít**.

Možnosti automatického ladění na serveru se použijí pro všechny databáze na tomto serveru. Ve výchozím nastavení všechny databáze dědí konfiguraci z jeho nadřazeného serveru, ale to se přepíše a jednotlivě zadat pro každou databázi.

### <a name="rest-api"></a>REST API

Další informace o použití rozhraní REST API k povolení automatického ladění na serveru, najdete v článku [SQL Server automatického ladění metody UPDATE a GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).


## <a name="enable-automatic-tuning-on-an-individual-database"></a>Povolení automatického ladění na jednotlivé databáze

Azure SQL Database umožňuje zadat konfiguraci automatického ladění pro každou databázi. Na úrovni databáze, kterou můžete dědí konfiguraci automatického ladění z nadřazeného serveru, "Azure je výchozí" nebo dědí konfiguraci. Azure výchozí hodnoty jsou nastavené FORCE_LAST_GOOD_PLAN je povoleno, je povoleno CREATE_INDEX a DROP_INDEX je zakázaná.

> [!NOTE]
> Obecné doporučení je spravovat konfiguraci automatického ladění na **úroveň serveru** tak stejné nastavení konfigurace můžete použít na všechny databáze automaticky. Konfigurace automatického ladění na jednotlivé databáze pouze v případě, že je nutné mít jiné nastavení než ostatní databáze dědí nastavení ze stejného serveru.
>

### <a name="azure-portal"></a>portál Azure

Povolit automatické vyladění **izolované databáze**, přejděte do databáze na webu Azure portal a vyberte **automatické ladění**.

Individuální nastavení automatického ladění můžete nakonfigurovat samostatně pro každou databázi. Můžete ručně nakonfigurovat jednotlivé možnosti automatického ladění nebo určit, že možnost dědí nastavení ze serveru.

![Databáze](./media/sql-database-automatic-tuning-enable/database.png)

Mějte prosím na paměti, že možnost DROP_INDEX v tuto chvíli není kompatibilní s aplikací s použitím pomocné parametry přepnutí a index oddílu a v těchto případech by se neměla povolovat.

Jakmile vyberete požadovanou konfiguraci, klikněte na tlačítko **použít**.

### <a name="rest-api"></a>Rest API

Další informace o použití rozhraní REST API k povolení automatického ladění na jednu databázi, naleznete v tématu [automatického ladění metody UPDATE a GET HTTP SQL Database](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Pokud chcete povolit automatické ladění na izolované databáze prostřednictvím T-SQL, připojení k databázi a spusťte následující dotaz:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Nastavení automatického ladění automaticky použije výchozí nastavení Azure. Nastavení pro ZDĚDIT, konfiguraci automatického ladění se budou dědit z nadřazeného serveru. Výběr vlastního, musíte ručně konfigurovat automatické ladění.

Chcete-li konfigurovat jednotlivé možnosti automatického ladění prostřednictvím T-SQL, připojení k databázi a spusťte dotaz, jako je například tento:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Nastavení jednotlivých možnost na hodnotu ON, přepíše nastavení, která databáze dědí a povolte možnost. Nastavení na hodnotu OFF, bude také přepsat nastavení, která databáze dědí a zakažte možnost. Možnosti automatického ladění, což výchozí nastavení je definováno, bude tuto konfiguraci dědit z automatického ladění nastavení úroveň databáze.  

Najít možnosti jazyka T-SQL pro konfiguraci automatického ladění, naleznete v tématu o našich dalších [možnostem ALTER DATABASE SET (Transact-SQL) pro logický server služby SQL Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Zakázaná v systému
Automatické ladění monitoruje všechny akce, kterou zabere v databázi a v některých případech můžete určit, že automatické ladění nejde fungovat správně v databázi. V takovém případě možnost vyladění bude zakázán systémem. Ve většině případů k tomu dochází, protože není povolená Query Store nebo se nachází ve stavu jen pro čtení v konkrétní databázi.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurovat automatické ladění e-mailová oznámení

Zobrazit [automatického ladění e-mailová oznámení](sql-database-automatic-tuning-email-notifications.md) průvodce.

## <a name="next-steps"></a>Další postup
* Čtení [automatické ladění článku](sql-database-automatic-tuning.md) získat další informace o automatickém ladění a jak ho můžete zlepšit výkon.
* Zobrazit [doporučení k výkonu](sql-database-advisor.md) přehled doporučení ohledně výkonu pro Azure SQL Database.
* Zobrazit [informace o výkonu dotazů](sql-database-query-performance.md) Další informace o zobrazení dopad na výkon vaše hlavní dotazy.
