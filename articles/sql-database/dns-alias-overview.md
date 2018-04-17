---
title: Alias DNS pro Azure SQL Database | Microsoft Docs
description: Vaše aplikace může připojit k alias pro název serveru Azure SQL Database. Mezitím můžete změnit databázi SQL alias odkazuje na kdykoli, aby se usnadnilo, testování a tak dále.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: DNS alias
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;ayolubek
ms.author: dmalik
ms.openlocfilehash: 1793f44026db55d17fabde5fb92bc46795109418
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pro databázi SQL Azure

Azure SQL Database má server systému DNS (Domain Name). Prostředí PowerShell a rozhraní REST API přijmout [volání k vytváření a správě aliasy DNS](#anchor-powershell-code-62x) pro název databáze SQL serveru.

A *DNS alias* jde použít místo názvu serveru Azure SQL Database. Programy pro klienta můžete použít alias v jejich připojovací řetězce. DNS alias poskytuje překlad vrstva, která můžete přesměrovat programy klienta na různých serverech. Tato vrstva ušetří problémy toho, že k vyhledání a upravit všech klientů a jejich připojovací řetězce.

Mezi běžná použití pro DNS alias patří v následujících případech:

- Vytvořte snadno zapamatovatelné jméno pro Azure SQL Server.
- Během počáteční vývoj může váš alias odkazovat na testovací server databáze SQL. Kdy aplikace přestane za provozu, můžete upravit alias, který bude odkazovat na provozním serveru. Přechod z testu do produkčního prostředí nevyžaduje žádné změny konfigurace několik klientů, které se připojují k serveru databáze.
- Předpokládejme, že pouze databáze v aplikaci je přesunut do jiné databáze SQL serveru. Zde můžete upravit alias bez nutnosti měnit konfiguraci několik klientů.

#### <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) z Internetu

Internet spoléhá na DNS. DNS překládá popisné názvy do názvu serveru Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scénáře s jeden alias DNS

Předpokládejme, že je potřeba přepnout systému na nový server Azure SQL Database. V minulosti, jste museli najít a aktualizovat každých připojovací řetězec v každé aplikaci klienta. Nyní, pokud připojovací řetězce použít DNS alias, pouze vlastnost alias musí se ale aktualizovat.

Funkce alias DNS Azure SQL Database můžou pomoct v těchto scénářích:

#### <a name="test-to-production"></a>Testování do produkčního prostředí

Když spustíte vývoj klientských programů, uživatelé použít DNS alias v jejich připojovací řetězce. Vlastnosti bodu alias provedete na zkušební verzi serveru Azure SQL Database.

Později kdy je nový systém přestane za provozu v produkčním prostředí, můžete aktualizovat vlastnosti alias tak, aby odkazoval na produkční databázi SQL Server. Žádná změna programy klienta je nezbytné.

#### <a name="cross-region-support"></a>Podpora mezi oblastmi

Zotavení po havárii může posunutí server služby SQL Database v jiné geografické oblasti. Pro systém než používal DNS alias potřeba najít a aktualizovat připojovací řetězce pro všechny klienty se vyhnout. Místo toho můžete aktualizovat alias odkazoval na nový server databáze SQL, který je teď hostitelem databáze.




## <a name="properties-of-a-dns-alias"></a>Vlastnosti DNS alias

Následující vlastnosti použít pro každý aliasu DNS pro server služby SQL Database:

- *Jedinečný název:* každý název aliasu vytvoříte je jedinečná napříč všemi servery Azure SQL Database, stejně jako server názvů.

- *Je vyžadován server:* A DNS alias nelze vytvořit, dokud se odkazuje na právě jeden server a server již musí existovat. Aktualizované alias vždy musí odkazovat přesně na jednom stávajícím serveru.
    - Při umístění databáze SQL serveru, systému Azure také zahodí všechny aliasy DNS, které odkazují na server.

- *Není vázána na libovolné oblasti:* aliasy DNS nejsou vázány na oblast. Všechny aliasy DNS mohou být aktualizovány k odkazování na serveru Azure SQL Database, který se nachází v libovolné geografické oblasti.
    - Ale při aktualizaci alias, který bude odkazovat na jiný server se oba servery, musí existovat ve stejném Azure *předplatné*.

- *Oprávnění:* ke správě DNS alias, musí mít uživatel *Server Přispěvatel* oprávnění, nebo vyšší. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](../role-based-access-control/overview.md).





## <a name="manage-your-dns-aliases"></a>Spravovat vaše aliasy DNS

Rutiny prostředí PowerShell a rozhraní REST API jsou k dispozici umožňují programově spravovat vaše aliasy DNS.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API pro správu vaše aliasy DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

V dokumentaci k rozhraní API REST je k dispozici téměř následující webové umístění:
- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

Rozhraní REST API můžete také zobrazit na webu GitHub na:
- [Server Azure SQL Database, DNS alias rozhraní REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Prostředí PowerShell pro správu vaše aliasy DNS

Rutiny prostředí PowerShell jsou k dispozici volání rozhraní REST API.

Příklad kódu používá ke správě aliasy DNS rutin prostředí PowerShell je popsána v:
- [Prostředí PowerShell pro Alias DNS do Azure SQL Database](dns-alias-powershell.md)


Rutiny používané v příkladu kódu jsou následující:
- [Nové AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na serveru Azure SQL Database 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): získání a zobrazit všechny aliasy DNS, které jsou přiřazeny k databázi SQL serveru 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): upraví název serveru nakonfigurovaný tak, aby alias odkazovat na umístění, ze serveru 1 k databázi SQL serveru 2.
- [Odebrat AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Odeberte DNS alias z databáze SQL serveru 2, pomocí název aliasu.


Předchozí rutiny byly přidány do **AzureRM.Sql** modulu od verze modulu 5.1.1.




## <a name="limitations-during-preview"></a>Omezení při preview

V současné době DNS alias má následující omezení:

- *Zpoždění až 2 minuty:* trvá až 2 minuty aliasu DNS pro aktualizace nebo odstranění.
    - Bez ohledu na krátké prodlevě alias okamžitě zastaví odkazující připojení klientů pro starší verze serveru.

- *Vyhledávání DNS:* prozatím pouze autoritativní způsob, jak zkontrolovat, co server dané DNS alias odkazuje je provedením [vyhledávání DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Auditování tabulka nepodporuje](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* nelze použít DNS alias na serveru Azure SQL Database, která má *auditování tabulka* v databázi povoleno.
    - Auditování tabulka je zastaralý.
    - Doporučujeme přesunout [auditování objektů Blob](sql-database-auditing.md).




## <a name="related-resources"></a>Související prostředky

- [Přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md), včetně zotavení po havárii.



## <a name="next-steps"></a>Další postup

- [Prostředí PowerShell pro Alias DNS do Azure SQL Database](dns-alias-powershell.md)

