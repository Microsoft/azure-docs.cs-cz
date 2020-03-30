---
title: Alias DNS
description: Vaše aplikace se můžou připojit ke aliasu pro název serveru Azure SQL Database. Mezitím můžete změnit databázi SQL alias odkazuje na kdykoliv, pro usnadnění testování a tak dále.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820619"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pro Azure SQL Database

Azure SQL Database má server DNS (Domain Name System). Api prostředí PowerShell a REST přijímají [volání k vytvoření a správě aliasů DNS](#anchor-powershell-code-62x) pro název serveru SQL Database.

*Alias DNS* lze použít místo názvu serveru Azure SQL Database. Klientské programy mohou alias používat ve svých připojovacích řetězcích. Alias DNS poskytuje vrstvu překladu, která může přesměrovat klientské programy na různé servery. Tato vrstva šetří potíže s hledáním a úpravou všech klientů a jejich připojovacích řetězců.

Běžné použití aliasu DNS zahrnuje následující případy:

- Vytvořte snadno zapamatovatelné jméno pro Azure SQL Server.
- Během počátečního vývoje může váš alias odkazovat na testovací databázový server SQL. Když aplikace přejde do provozu, můžete upravit alias odkazovat na produkční server. Přechod z testu do produkčního prostředí nevyžaduje žádné změny konfigurace několik klientů, kteří se připojují k databázovému serveru.
- Předpokládejme, že je pouze databáze v aplikaci přesunuta na jiný server databáze SQL. Zde můžete změnit alias bez nutnosti měnit konfigurace několika klientů.
- Během místnívýkárna použít geografické obnovení obnovit databázi v jiném serveru a oblasti. Existující alias můžete upravit tak, aby přecšlákem na nový server, aby se k němu mohla stávající klientská aplikace znovu připojit. 

## <a name="domain-name-system-dns-of-the-internet"></a>Systém DNS (DNS) internetu

Internet se spoléhá na DNS. Dns převádí popisné názvy do názvu serveru Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scénáře s jedním aliasem DNS

Předpokládejme, že potřebujete přepnout systém na nový server Azure SQL Database. V minulosti jste potřebovali najít a aktualizovat každý připojovací řetězec v každém klientském programu. Ale teď, pokud připojovací řetězce používají alias DNS, musí být aktualizována pouze vlastnost aliasu.

Funkce aliasu DNS v Azure SQL Database může pomoct v následujících scénářích:

### <a name="test-to-production"></a>Zkouška do výroby

Při spuštění vývoje klientských programů je nechte použít alias DNS ve svých připojovacích řetězcích. Vlastnosti aliasu přejdete na testovací verzi serveru Azure SQL Database.

Později, když nový systém přejde do provozu v produkčním prostředí, můžete aktualizovat vlastnosti aliasu tak, aby ukazoval na produkční databázový server SQL. Není nutná žádná změna klientských programů.

### <a name="cross-region-support"></a>Podpora napříč oblastmi

Zotavení po havárii může přesunout databázový server SQL do jiné geografické oblasti. Pro systém, který používal alias DNS, je třeba se vyhnout nutnosti najít a aktualizovat všechny připojovací řetězce pro všechny klienty. Místo toho můžete aktualizovat alias odkazovat na nový server sql databáze, který nyní hostuje databázi.

## <a name="properties-of-a-dns-alias"></a>Vlastnosti aliasu DNS

Pro každý alias DNS pro databázový server SQL platí následující vlastnosti:

- *Jedinečný název:* Každý název aliasu, který vytvoříte, je jedinečný na všech serverech Azure SQL Database, stejně jako názvy serverů.
- *Je vyžadován server:* Alias DNS nelze vytvořit, pokud neodkazuje přesně na jeden server a server již musí existovat. Aktualizovaný alias musí vždy odkazovat přesně na jeden existující server.
  - Když přetáhnete sql database server, systém Azure také klesne všechny aliasy DNS, které odkazují na server.
- *Není vázán na žádnou oblast:* Aliasy DNS nejsou vázány na oblast. Všechny aliasy DNS lze aktualizovat tak, aby odkazovaly na server Azure SQL Database, který se nachází v libovolné geografické oblasti.
  - Však při aktualizaci alias odkazovat na jiný server, oba servery musí existovat ve stejném *předplatném*Azure .
- *Oprávnění:* Chcete-li spravovat alias DNS, musí mít uživatel oprávnění *přispěvatele serveru* nebo vyšší. Další informace najdete [v tématu Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Správa aliasů DNS

Rozhraní REST API i rutiny prostředí PowerShell jsou k dispozici, které umožňují programovou správu aliasů DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API pro správu aliasů DNS

Dokumentace pro rozhraní REST API je k dispozici v blízkosti následujícího webového umístění:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Rozhraní REST API lze také vidět na GitHubu na adrese:

- [Databázový server Azure SQL, virtuální chod ALIAS DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell pro správu aliasů DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Rutiny prostředí PowerShell jsou k dispozici, které volají rozhraní REST API.

Příklad kódu rutin prostředí PowerShell používaných ke správě aliasů DNS je popsán na adrese:

- [PowerShell pro alias DNS do databáze Azure SQL](dns-alias-powershell.md)

Rutiny použité v příkladu kódu jsou následující:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na server Azure SQL Database 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získejte a seznam všech aliasů DNS, které jsou přiřazeny k serveru SQL DB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Upraví název serveru, na který je alias nakonfigurován, ze serveru 1 na server SQL DB server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias DNS ze serveru SQL DB 2 pomocí názvu aliasu.

## <a name="limitations-during-preview"></a>Omezení během náhledu

V současné době má alias DNS následující omezení:

- *Zpoždění až 2 minuty:* Aktualizace nebo odebrání aliasu DNS trvá až 2 minuty.
  - Bez ohledu na jakékoli krátké zpoždění alias okamžitě zastaví odkazování připojení klientů ke staršímu serveru.
- *Vyhledávání DNS:* Pro tuto chvíli je jediným autoritativním způsobem, jak zkontrolovat, na který server daný alias DNS odkazuje, provedení [vyhledávání DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Auditování tabulky není podporováno:_ Alias DNS nelze použít na serveru Azure SQL Database, který má v databázi povolené *auditování tabulek.*
  - Auditování tabulky je zastaralé.
  - Doporučujeme přejít na [objekt Blob auditování](sql-database-auditing.md).

## <a name="related-resources"></a>Související prostředky

- [Přehled kontinuity podnikání s Azure SQL Database](sql-database-business-continuity.md), včetně zotavení po havárii.

## <a name="next-steps"></a>Další kroky

- [PowerShell pro alias DNS do databáze Azure SQL](dns-alias-powershell.md)
