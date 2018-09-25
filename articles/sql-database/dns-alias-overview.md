---
title: Alias DNS pro službu Azure SQL Database | Dokumentace Microsoftu
description: Vaše aplikace můžou připojit k alias pro název vašeho serveru Azure SQL Database. Mezitím můžete změnit databázi SQL s aliasem, který se odkazuje na kdykoli a usnadňuje testování a tak dále.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DhruvMsft
ms.author: dmalik
ms.reviewer: genemi,ayolubek
manager: craigg
ms.date: 02/05/2018
ms.openlocfilehash: 6c174871ff7bc61d11804e32aeac738bf6159c10
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054111"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS pro službu Azure SQL Database

Azure SQL Database má server systému DNS (Domain Name). Prostředí PowerShell a rozhraní REST API přijmout [volání můžete vytvářet a spravovat aliasy DNS](#anchor-powershell-code-62x) pro název databáze SQL serveru.

A *DNS alias* jde použít místo názvu serveru Azure SQL Database. Klientské programy můžete použít alias ve svých připojovacích řetězců. DNS alias poskytuje transakční vrstva, která můžete přesměrovat klientských programů na různých serverech. Tato vrstva ušetří potíže a hledání a úprava všech klientů a jejich připojovací řetězce.

Mezi běžná použití aliasu DNS patří následujících případech:

- Vytvoření snadno zapamatovatelné jméno pro Azure SQL Server.
- Během počáteční vývoje testovací server služby SQL Database najdete váš alias. Když aplikace přejde za provozu, můžete upravit alias pro odkazování na provozním serveru. Přechod z testovacího do produkčního prostředí nevyžaduje žádné změny konfigurace několik klientů, které se připojují k databázovému serveru.
- Předpokládejme, že jediná databáze ve vaší aplikaci se přesune na jiný server SQL Database. Zde můžete upravit alias aniž byste museli měnit konfiguraci z několika klientů.

#### <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) z Internetu

Internet závisí na DNS. DNS překládá popisné názvy do názvu serveru Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scénáře s jeden alias DNS

Předpokládejme, že potřebujete přepnout systému na nový server Azure SQL Database. V minulosti jste museli najít a aktualizovat všechny připojovací řetězec ve více programech klienta. Ale nyní DNS alias používáte připojovací řetězce pouze vlastnost alias musí být aktualizovány.

Funkce alias DNS služby Azure SQL Database může pomoct v těchto scénářích:

#### <a name="test-to-production"></a>Test do produkčního prostředí

Při spuštění vývoji klientských programů dát použít DNS alias v jejich připojovací řetězce. Vlastnosti bodu alias provedete na zkušební verzi serveru Azure SQL Database.

Později při přechodu na nový systém naživo v produkčním prostředí, můžete aktualizovat vlastnosti aliasu, aby odkazoval na produkční server SQL Database. Žádné změny klientské programy je nezbytné.

#### <a name="cross-region-support"></a>Podpora mezi oblastmi

Zotavení po havárii může posunout vaše databáze SQL server v jiné geografické oblasti. Pro systém než používal DNS alias, třeba najít a aktualizovat připojovací řetězce pro všechny klienty se můžete vyhnout. Místo toho můžete aktualizovat alias jako reference na nový server databáze SQL, který je teď hostitelem databáze.




## <a name="properties-of-a-dns-alias"></a>Vlastnosti DNS alias

Následující vlastnosti se vztahují na každý alias DNS pro server SQL Database:

- *Jedinečný název:* každý název aliasu vytvoříte je jedinečná napříč všemi servery Azure SQL Database, stejně jako server se názvy.

- *Je vyžadován server:* A DNS alias nelze vytvořit, pokud odkazuje na právě jeden server a server už musí existovat. Aktualizované alias vždy musí odkazovat přesně na jednom stávajícím serveru.
    - Při umístění serveru SQL Database systému Azure i zahodí všechny aliasy DNS, které odkazují na serveru.

- *Bez vazby na libovolné oblasti:* aliasy DNS nejsou vázány na oblast. Všechny aliasy DNS je možné aktualizovat k odkazování na serveru Azure SQL Database, které se nacházejí v libovolné geografické oblasti.
    - Ale při aktualizaci aliasu pro odkazování na jiný server, oba servery, musí existovat ve stejné *předplatné*.

- *Oprávnění:* ke správě DNS alias, musí mít uživatel *Přispěvatel serveru* oprávnění, nebo vyšší. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../role-based-access-control/overview.md).





## <a name="manage-your-dns-aliases"></a>Spravovat vaše aliasy DNS

Rutiny Powershellu a rozhraní REST API jsou k dispozici umožňují programově spravovat vaše aliasy DNS.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API pro správu vašich aliasy DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

V dokumentaci k rozhraní REST API je k dispozici v následujícím umístění na webu:
- [Rozhraní REST API služby Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

Také rozhraní REST API najdete v Githubu na:
- [Server služby Azure SQL Database, DNS alias rozhraní REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Prostředí PowerShell pro správu vašich aliasy DNS

Rutiny Powershellu jsou dostupné, které volají rozhraní REST API.

Příklad kódu používá ke správě aliasy DNS rutiny prostředí PowerShell je popsána v:
- [Prostředí PowerShell pro Alias DNS do služby Azure SQL Database](dns-alias-powershell.md)


Rutiny používané v příkladu kódu jsou následující:
- [Nový-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na serveru Azure SQL Database 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): získání a seznam všechny aliasy DNS, které jsou přiřazeny k serveru SQL DB 1.
- [Rutiny Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): upraví název serveru alias nakonfigurovaný tak, aby si ze serveru 1 do databáze SQL serveru 2.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): odebrání DNS alias serveru SQL DB 2, pomocí názvu aliasu.


Předchozí rutiny byly přidány do **AzureRM.Sql** modul od verze 5.1.1 modulu.




## <a name="limitations-during-preview"></a>Omezení ve verzi preview

V současné době DNS alias má následující omezení:

- *Prodlevě o délce až 2 minuty:* trvá až 2 minut aliasu DNS pro aktualizace nebo odstranění.
    - Bez ohledu na krátké prodlevě alias okamžitě zastaví odkazující připojení klientů pro starší verze serveru.

- *Vyhledávání DNS:* prozatím pouze autoritativní způsob, jak zkontrolovat, co server dané DNS alias odkazuje na je pomocí provádí [vyhledávání DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Auditování tabulek není podporován](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* nelze použít DNS alias na serveru Azure SQL Database, který má *auditování tabulek* na databázi povolená.
    - Auditování tabulek je zastaralá.
    - Doporučujeme vám, že přejdete na [auditování objektů Blob](sql-database-auditing.md).




## <a name="related-resources"></a>Související prostředky

- [Přehled kontinuity se službou Azure SQL Database](sql-database-business-continuity.md), včetně zotavení po havárii.



## <a name="next-steps"></a>Další postup

- [Prostředí PowerShell pro Alias DNS do služby Azure SQL Database](dns-alias-powershell.md)

