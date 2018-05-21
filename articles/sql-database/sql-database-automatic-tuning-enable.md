---
title: Povolit automatické ladění pro Azure SQL Database | Microsoft Docs
description: Můžete povolit automatické ladění na vaší databázi SQL Azure, snadno.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: e4c3a2c1f21bf14bfc75f20dd18cefca68fd2067
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="enable-automatic-tuning"></a>Povolení automatického ladění

Databáze SQL Azure je služba automaticky spravovaná data, která neustále monitoruje vaše dotazy a identifikuje akci, která umožňuje zvýšit výkon vašich úloh. Můžete zkontrolovat doporučení a ručně je použít nebo to Azure SQL Database automaticky použít opravné akce – to se označuje jako **automatické ladění režimu**. Automatické ladění se dá nastavit na úrovni databáze nebo serveru.

## <a name="enable-automatic-tuning-on-server"></a>Povolit automatické ladění na serveru
Na úrovni serveru můžete nastavit dědění automatickou konfiguraci ladění z "Azure je výchozí" nebo zdědit konfiguraci. Azure výchozí hodnoty jsou FORCE_LAST_GOOD_PLAN je povoleno, je povoleno CREATE_INDEX a DROP_INDEX je zakázána.

### <a name="azure-portal"></a>Azure Portal
Chcete-li povolit automatické ladění na Azure SQL Database **server**, přejděte na server na portálu Azure a pak vyberte **automatické ladění** v nabídce. Vyberte možnost Automatické ladění možnosti, které chcete povolit a vyberte **použít**.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Pamatujte, že **DROP_INDEX** možnost v tuto chvíli není kompatibilní s aplikací pomocí pomocné parametry přepínání a index oddílu a nesmí být zapnut v těchto případech.
>

Automatické možnosti ladění na serveru se použijí pro všechny databáze na serveru. Ve výchozím nastavení všechny databáze Zdědit konfiguraci z jejich nadřízeného serveru, ale to můžete přepsat a zadat pro každou databázi jednotlivě.

### <a name="rest-api"></a>REST API
[Klikněte sem, chcete-li další informace o tom, jak povolit automatické ladění na úrovni serveru pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Povolit automatické ladění na jednotlivé databáze

Databáze SQL Azure umožňuje jednotlivě zadat automatickou konfiguraci ladění na každou databázi. Na úrovni databáze můžete nastavit dědění automatickou konfiguraci ladění z nadřízeného serveru, "Azure je výchozí" nebo zdědit konfiguraci. Azure výchozí hodnoty jsou povolené FORCE_LAST_GOOD_PLAN, CREATE_INDEX povolené a zakázané DROP_INDEX.

> [!NOTE]
> Obecné doporučení je spravovat konfiguraci automatické ladění na úrovni serveru, aby stejné nastavení konfigurace můžete použít na každou databázi automaticky. Konfigurujte automatické ladění na jednotlivé databáze Pokud databázi se liší jiné na stejném serveru.
>

### <a name="azure-portal"></a>Azure Portal

Chcete-li povolit automatické ladění na **jedné databáze**, přejděte do databáze na portálu Azure a pak vyberte **automatické ladění**. Můžete nakonfigurovat jednu databázi pro dědění nastavení ze serveru tak, že vyberete možnost nebo můžete zadat konfiguraci pro databázi jednotlivě.

![Databáze](./media/sql-database-automatic-tuning-enable/database.png)

Jakmile vyberete odpovídající konfiguraci, klikněte na tlačítko **použít**.

Upozorňujeme, že možnost DROP_INDEX v tuto chvíli není kompatibilní s aplikací pomocí pomocné parametry přepínání a index oddílu a nesmí být zapnut v těchto případech.

### <a name="rest-api"></a>Rest API
[Kliknutím na další informace o tom, jak povolit automatické ladění na jedné databáze pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Pokud chcete povolit automatické ladění na jedné databáze pomocí T-SQL, připojení k databázi a spusťte následující dotaz:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Nastavení automatické ladění na hodnotu AUTO, použije se výchozí nastavení Azure. Jeho nastavení na ZDĚDĚNÉ automatickou konfiguraci ladění zdědí z nadřízeného serveru. Výběr vlastní, musíte ručně nakonfigurovat automatické ladění.

Chcete-li konfigurovat jednotlivé možnosti automatické ladění pomocí T-SQL, připojení k databázi a spustit dotaz, jako je tato:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Nastavení jednotlivých vyladění možnost na hodnotu ON, přepíše všechna nastavení, která databáze zděděná a povolte možnost vyladění. Jeho nastavení na hodnotu OFF, bude také přepíše všechna nastavení, která databáze zděděná a zakažte možnost vyladění. Automatické ladění možnost, pro které je zadána výchozí, zdědí nastavení z automatické ladění nastavení databáze úrovně.  

## <a name="disabled-by-the-system"></a>Zakázáno v systému
Automatické ladění monitoruje všechny akce, kterou trvá v databázi a v některých případech můžete určit, že automatické ladění nemůže pracovat správně v databázi. V takovém případě možnost vyladění bude zakázáno v systému. Ve většině případů k tomu dochází, protože úložiště dotazů není povolena nebo je ve stavu jen pro čtení určitou databázi.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurovat automatické ladění e-mailových oznámení

V tématu [automatické ladění e-mailových oznámení](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Další postup
* Pro čtení [automatické ladění článku](sql-database-automatic-tuning.md) Další informace o automatické ladění a jak se vám může pomoct zlepšit výkon.
* V tématu [výkonu doporučení](sql-database-advisor.md) přehled o výkonu doporučení Azure SQL Database.
* V tématu [Query Performance Insight](sql-database-query-performance.md) Další informace o zobrazení dopad na výkon nejčastějších dotazů.
