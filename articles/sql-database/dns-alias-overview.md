---
title: Alias DNS pro Azure SQL Database | Microsoft Docs
description: Vaše aplikace se můžou připojit k aliasu pro název vašeho serveru Azure SQL Database. Mezitím můžete změnit SQL Database alias na kdykoli, abyste usnadnili testování a tak dále.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, ayolubek, jrasnick
ms.date: 06/26/2019
ms.openlocfilehash: 3d0a4b5890ed5758f4045459815fb4ebbffe75c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550655"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pro Azure SQL Database

Azure SQL Database má server DNS (Domain Name System). Prostředí PowerShell a rozhraní REST API přijímají [volání pro vytváření a správu aliasů DNS](#anchor-powershell-code-62x) pro název vašeho serveru SQL Database.

Namísto názvu Azure SQL Database serveru se dá použít *alias DNS* . Klientské programy můžou alias používat v připojovacích řetězcích. Alias DNS poskytuje vrstvu překladu, která může přesměrovat klientské programy na různé servery. Tato vrstva si vyprázdní problémy při hledání a úpravách všech klientů a jejich připojovacích řetězců.

Mezi běžné použití aliasu DNS patří následující případy:

- Vytvořte si snadno zapamatovatelné jméno pro Azure SQL Server.
- Při počátečním vývoji může váš alias odkazovat na testovací SQL Database Server. Když bude aplikace živá, můžete alias upravit tak, aby odkazoval na provozní server. Přechod z testu do produkčního prostředí nevyžaduje žádnou změnu konfigurace několika klientů, kteří se připojují k databázovému serveru.
- Předpokládejme, že je jediná databáze ve vaší aplikaci přesunuta na jiný server SQL Database. Tady můžete alias upravit, aniž byste museli měnit konfiguraci několika klientů.
- Během regionálního výpadku použijete geografické obnovení k obnovení databáze na jiném serveru a oblasti. Existující alias můžete upravit tak, aby odkazoval na nový server, aby se stávající klientská aplikace mohla znovu připojit. 

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) Internetu

Internet spoléhá na DNS. DNS překládá vaše popisné názvy na název vašeho serveru Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scénáře s jedním aliasem DNS

Předpokládejme, že je nutné přepnout systém na nový server Azure SQL Database. V minulosti jste potřebovali vyhledat a aktualizovat všechny připojovací řetězce v každém klientském programu. Ale teď, pokud připojovací řetězce používají alias DNS, je nutné aktualizovat pouze vlastnost alias.

Funkce aliasu DNS Azure SQL Database může pomáhat v následujících scénářích:

### <a name="test-to-production"></a>Testování do produkčního prostředí

Když začnete vyvíjet klientské programy, požádejte je pomocí aliasu DNS ve svých připojovacích řetězcích. Vlastnosti aliasu můžete nastavit na testovací verzi serveru Azure SQL Database.

Později po zprovoznění nového systému v produkčním prostředí můžete aktualizovat vlastnosti aliasu tak, aby odkazovaly na provozní SQL Database Server. Nejsou nutné žádné změny klientských programů.

### <a name="cross-region-support"></a>Podpora různých oblastí

Zotavení po havárii může přesunout server SQL Database do jiné geografické oblasti. V případě systému, který používá alias DNS, je možné vyhnout se nutnosti najít a aktualizovat všechny připojovací řetězce pro všechny klienty. Místo toho můžete alias aktualizovat tak, aby odkazoval na nový SQL Database Server, který je teď hostitelem vaší databáze.

## <a name="properties-of-a-dns-alias"></a>Vlastnosti aliasu DNS

Následující vlastnosti se vztahují na všechny aliasy DNS pro váš SQL Database Server:

- *Jedinečný název:* Každý název aliasu, který vytvoříte, je jedinečný na všech Azure SQL Database serverech, stejně jako názvy serverů.
- *Vyžaduje se server:* Alias DNS nelze vytvořit, pokud neodkazuje přesně na jeden server a server již musí existovat. Aktualizovaný alias musí vždy odkazovat přesně na jeden existující server.
  - Když vyřadíte SQL Database Server, systém Azure také zruší všechny aliasy DNS, které na server odkazují.
- *Není svázána s žádnou oblastí:* Aliasy DNS nejsou svázané s oblastí. Jakékoli aliasy DNS je možné aktualizovat, aby odkazovaly na Azure SQL Database Server, který se nachází v libovolné geografické oblasti.
  - Když ale aktualizujete alias tak, aby odkazoval na jiný server, musí oba servery existovat ve stejném předplatném Azure.
- *Nastaven* Aby uživatel mohl spravovat alias DNS, musí mít oprávnění *přispěvatele serveru* nebo vyšší. Další informace najdete v tématu [Začínáme s Access Control na základě rolí v Azure Portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Správa aliasů DNS

K dispozici jsou jak rozhraní REST API, tak rutiny PowerShellu, které vám umožní programově spravovat vaše aliasy DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>Rozhraní REST API pro správu aliasů DNS

Dokumentace k rozhraním REST API je k dispozici v blízkosti následujícího webového umístění:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Rozhraní REST API můžete také vidět v GitHubu na adrese:

- [Azure SQL Database Server, rozhraní REST API aliasu DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell pro správu aliasů DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

K dispozici jsou rutiny PowerShellu, které volají rozhraní REST API.

Příklad kódu rutin PowerShellu, který se používá ke správě aliasů DNS, je popsaný na adrese:

- [PowerShell pro Azure SQL Database aliasu DNS](dns-alias-powershell.md)

Rutiny používané v tomto příkladu kódu jsou následující:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Vytvoří nový alias DNS v systému Azure SQL Database služby. Alias odkazuje na Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získá a vypíše všechny aliasy DNS, které jsou přiřazené k serveru SQL DB Server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Upraví název serveru, na který je nakonfigurován odkaz, ze serveru 1 na server SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias DNS ze serveru SQL DB Server 2 pomocí názvu aliasu.

## <a name="limitations-during-preview"></a>Omezení ve verzi Preview

V současné době má alias DNS tato omezení:

- *Zpoždění až do 2 minut:* Aktualizace nebo odebrání aliasu DNS trvá až 2 minuty.
  - Bez ohledu na krátké zpoždění přestane alias okamžitě přesměrovat odkazy na připojení klientů na starší verzi serveru.
- *Vyhledání DNS:* V současné době je jediným autoritativním způsobem, jak zkontrolovat, na který server daný alias DNS odkazuje, způsob provádění [vyhledávání DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Auditování tabulek není podporováno:_ Alias DNS nelze použít na serveru Azure SQL Database, který má povoleno *auditování tabulek* v databázi.
  - Auditování tabulek je zastaralé.
  - Doporučujeme přejít na [auditování objektů BLOB](sql-database-auditing.md).

## <a name="related-resources"></a>Související prostředky

- [Přehled provozní kontinuity s Azure SQL Database](sql-database-business-continuity.md), včetně zotavení po havárii.

## <a name="next-steps"></a>Další kroky

- [PowerShell pro Azure SQL Database aliasu DNS](dns-alias-powershell.md)
