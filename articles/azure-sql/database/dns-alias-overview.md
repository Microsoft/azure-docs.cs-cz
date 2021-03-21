---
title: Alias DNS
description: Vaše aplikace se můžou připojit k aliasu pro název serveru pro Azure SQL Database. Mezitím můžete změnit SQL Database alias na kdykoli, abyste usnadnili testování a tak dále.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 25c2157a8de237d0ec66caa72d59c810d419ac76
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94989118"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database má server DNS (Domain Name System). Prostředí PowerShell a rozhraní REST API přijímají [volání pro vytváření a správu aliasů DNS](#anchor-powershell-code-62x) pro váš [logický název SQL serveru](logical-servers.md) .

Namísto názvu serveru se dá použít *alias DNS* . Klientské programy můžou alias používat v připojovacích řetězcích. Alias DNS poskytuje vrstvu překladu, která může přesměrovat klientské programy na různé servery. Tato vrstva si vyprázdní problémy při hledání a úpravách všech klientů a jejich připojovacích řetězců.

Mezi běžné použití aliasu DNS patří následující případy:

- Vytvořte si snadno zapamatovatelné jméno serveru.
- Při počátečním vývoji může váš alias odkazovat na testovací server. Když bude aplikace živá, můžete alias upravit tak, aby odkazoval na provozní server. Přechod z testu do produkčního prostředí nevyžaduje žádnou změnu konfigurace několika klientů, kteří se připojují k serveru.
- Předpokládejme, že je jediná databáze ve vaší aplikaci přesunuta na jiný server. Alias můžete změnit, aniž byste museli měnit konfigurace několika klientů.
- Během regionálního výpadku použijete geografické obnovení k obnovení databáze na jiném serveru a oblasti. Existující alias můžete upravit tak, aby odkazoval na nový server, aby se stávající klientská aplikace mohla znovu připojit.

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) Internetu

Internet spoléhá na DNS. DNS překládá vaše popisné názvy na název vašeho serveru.

## <a name="scenarios-with-one-dns-alias"></a>Scénáře s jedním aliasem DNS

Předpokládejme, že je nutné přepnout systém na nový server. V minulosti jste potřebovali vyhledat a aktualizovat všechny připojovací řetězce v každém klientském programu. Ale teď, pokud připojovací řetězce používají alias DNS, je nutné aktualizovat pouze vlastnost alias.

Funkce aliasu DNS Azure SQL Database může pomáhat v následujících scénářích:

### <a name="test-to-production"></a>Testování do produkčního prostředí

Když začnete vyvíjet klientské programy, požádejte je pomocí aliasu DNS ve svých připojovacích řetězcích. Vlastnosti aliasu můžete nastavit na testovací verzi serveru.

Později po zprovoznění nového systému v produkčním prostředí můžete aktualizovat vlastnosti aliasu tak, aby odkazovaly na provozní server. Nejsou nutné žádné změny klientských programů.

### <a name="cross-region-support"></a>Podpora různých oblastí

Zotavení po havárii může přesunout server do jiné geografické oblasti. V případě systému, který používá alias DNS, je možné vyhnout se nutnosti najít a aktualizovat všechny připojovací řetězce pro všechny klienty. Místo toho můžete alias aktualizovat tak, aby odkazoval na nový server, který je teď hostitelem vašeho Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>Vlastnosti aliasu DNS

Následující vlastnosti se vztahují na všechny aliasy DNS vašeho serveru:

- *Jedinečný název:* Každý název aliasu, který vytvoříte, je v rámci všech serverů jedinečný, stejně jako názvy serverů.
- *Vyžaduje se server:* Alias DNS nelze vytvořit, pokud neodkazuje přesně na jeden server a server již musí existovat. Aktualizovaný alias musí vždy odkazovat přesně na jeden existující server.
  - Když vyřadíte Server, systém Azure také zruší všechny aliasy DNS, které na server odkazují.
- *Není svázána s žádnou oblastí:* Aliasy DNS nejsou svázané s oblastí. Všechny aliasy DNS je možné aktualizovat tak, aby odkazovaly na server, který se nachází v libovolné geografické oblasti.
  - Když ale aktualizujete alias tak, aby odkazoval na jiný server, musí oba servery existovat ve stejném *předplatném* Azure.
- *Oprávnění:* Aby uživatel mohl spravovat alias DNS, musí mít oprávnění *přispěvatele serveru* nebo vyšší. Další informace najdete v tématu [Začínáme s řízením přístupu na základě role Azure v Azure Portal](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Správa aliasů DNS

K dispozici jsou jak rozhraní REST API, tak rutiny PowerShellu, které vám umožní programově spravovat vaše aliasy DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>Rozhraní REST API pro správu aliasů DNS

Dokumentace k rozhraním REST API je k dispozici v blízkosti následujícího webového umístění:

- [Azure SQL Database REST API](/rest/api/sql/)

Rozhraní REST API můžete také vidět v GitHubu na adrese:

- [Azure SQL Database rozhraní REST API aliasu DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell pro správu aliasů DNS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

K dispozici jsou rutiny PowerShellu, které volají rozhraní REST API.

Příklad kódu rutin PowerShellu, který se používá ke správě aliasů DNS, je popsaný na adrese:

- [PowerShell pro Azure SQL Database aliasu DNS](dns-alias-powershell-create.md)

Rutiny používané v tomto příkladu kódu jsou následující:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na server 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získejte a vypíše všechny aliasy DNS, které jsou přiřazené k serveru 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): upraví název serveru, pro který je nakonfigurován odkaz na, ze serveru 1 na server 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias DNS ze serveru 2 pomocí názvu aliasu.

## <a name="limitations-during-preview"></a>Omezení ve verzi Preview

V současné době má alias DNS tato omezení:

- *Zpoždění až do 2 minut:* Aktualizace nebo odebrání aliasu DNS trvá až 2 minuty.
  - Bez ohledu na krátké zpoždění přestane alias okamžitě přesměrovat odkazy na připojení klientů na starší verzi serveru.
- *Vyhledání DNS:* V současné době je jediným autoritativním způsobem, jak zkontrolovat, na který server daný alias DNS odkazuje, způsob provádění [vyhledávání DNS](/windows-server/administration/windows-commands/nslookup).
- _Auditování tabulek není podporováno:_ Alias DNS nelze použít na serveru, který má povoleno *auditování tabulky* v databázi.
  - Auditování tabulek je zastaralé.
  - Doporučujeme přejít na [auditování objektů BLOB](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Související prostředky

- [Přehled provozní kontinuity s Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md), včetně zotavení po havárii.
- [Referenční informace k rozhraní Azure REST API](/rest/api/azure/)
- [Rozhraní API pro aliasy serveru DNS](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>Další kroky

- [PowerShell pro Azure SQL Database aliasu DNS](dns-alias-powershell-create.md)