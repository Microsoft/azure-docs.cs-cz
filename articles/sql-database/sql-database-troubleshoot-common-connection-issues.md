---
title: Řešení běžných problémů s připojením
description: Postup identifikace a řešení běžných chyb připojení pro Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 11/14/2019
ms.openlocfilehash: ffbe52bfcef3f32a12e97d37c39a2199cefe72ce
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082464"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Řešení potíží s připojením pro Azure SQL Database

Pokud se připojení k Azure SQL Database selžou, zobrazí se [chybové zprávy](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Tento článek je centralizované téma, které vám pomůže při řešení potíží s problémy s připojením Azure SQL Database. Zavádí [běžné příčiny](#cause) potíží s připojením, doporučuje Nástroj pro [řešení potíží](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , který vám pomůže s tím, jak problém vyřešit, a poskytuje kroky pro řešení problémů s [přechodem k přechodným chybám](#troubleshoot-transient-errors) a [trvalým nebo nepřechodným chybám ](#troubleshoot-persistent-errors).

Pokud narazíte na problémy s připojením, zkuste postup řešení potíží, který je popsaný v tomto článku.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Příčina

Problémy s připojením mohou být způsobeny některým z následujících způsobů:

* Neúspěšné použití osvědčených postupů a pokynů pro návrh během procesu návrhu aplikace.  Začněte tím, že najdete [Přehled vývoje SQL Database](sql-database-develop-overview.md) .
* Azure SQL Database rekonfigurace
* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Dosáhlo se maximálního limitu u některých prostředků Azure SQL Database.

Obecně mohou být problémy s připojením k Azure SQL Database klasifikovány takto:

* [Přechodné chyby (krátkodobé nebo občasné)](#troubleshoot-transient-errors)
* [Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Vyzkoušejte poradce při potížích s Azure SQL Database problémy s připojením

Pokud narazíte na konkrétní chybu připojení, vyzkoušejte si [Tento nástroj](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), který vám pomůže rychle zjistit identitu a vyřešit váš problém.

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

Když se aplikace připojí k databázi SQL Azure, zobrazí se tato chybová zpráva:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Tato chybová zpráva je obvykle přechodný (krátkodobá).

K této chybě dojde, když přesunete databázi (nebo znovu nakonfigurujete) a vaše aplikace ztratí připojení k databázi. Události opětovné konfigurace databáze nastávají z důvodu plánované události (například upgrade softwaru) nebo neplánované události (například selhání procesu nebo vyrovnávání zatížení). Většina událostí opětovné konfigurace je obecně krátkodobá a měla by být dokončena během méně než 60 sekund. Tyto události mohou nicméně občas trvat déle, například když velká transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení potíží s přechodným připojením

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo v době, během které aplikace ohlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je třeba Azure SQL Database, by měly očekávat události pravidelného překonfigurování a implementují logiku opakování pro zpracování těchto chyb, místo aby je zpřístupnění jako chyby aplikací pro uživatele. Další informace a obecné strategie opakování najdete v části [přechodné chyby](sql-database-connectivity-issues.md) a osvědčené postupy a pokyny pro návrh při [SQL Database přehledu vývoje](sql-database-develop-overview.md) . Pak si přečtěte téma ukázky kódu v tématu [knihovny připojení pro SQL Database a SQL Server](sql-database-libraries.md) pro konkrétní.
3. Vzhledem k tomu, že se databáze blíží omezení prostředků, může se jednat o přechodný problém s připojením. Viz [omezení prostředků](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Pokud potíže s připojením trvají nebo pokud doba, po kterou vaše aplikace narazí na chybu, překračuje 60 sekund, nebo pokud se v daném dni zobrazí více výskytů chyby, zastavte žádost o podporu Azure výběrem možnosti **získat podporu** na [Azure. Web podpory](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Řešení trvalých chyb

Pokud se aplikace trvale nepřipojí k Azure SQL Database, obvykle se jedná o problém s jedním z následujících způsobů:

* Konfigurace brány firewall. Služba Azure SQL Database nebo brána firewall na straně klienta blokuje připojení k Azure SQL Database.
* Rekonfigurace sítě na straně klienta: například nová IP adresa nebo proxy server.
* Chyba uživatele: například chybné typové parametry připojení, například název serveru v připojovacím řetězci.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení potíží s trvalým připojením

1. Nastavte [pravidla brány firewall](sql-database-configure-firewall-settings.md) tak, aby povolovala IP adresu klienta. Pro účely dočasného testování nastavte pravidlo brány firewall s použitím direktivy 0.0.0.0 jako počátečního rozsahu IP adres a jako konečný rozsah IP adres použijte 255.255.255.255. Tím se Server otevře na všech IP adresách. Pokud dojde k vyřešení problému s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídající omezené IP adresy nebo rozsah adres.
2. U všech bran firewall mezi klientem a internetem se ujistěte, že je port 1433 otevřený pro odchozí připojení. Přečtěte si téma [Konfigurace brány Windows Firewall, aby povolovala přístup k SQL serverům a aby](https://msdn.microsoft.com/library/cc646023.aspx) byly [porty a protokoly](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) pro další ukazatele týkající se dalších portů, které je třeba otevřít pro Azure Active Directory ověřování, nemusely používat.
3. Ověřte připojovací řetězec a další nastavení připojení. Viz část připojovací řetězec v [tématu problémy s připojením](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Ověřte stav služby na řídicím panelu. Pokud se domníváte, že se jedná o oblastní výpadky, přečtěte si téma [zotavení po výpadku](sql-database-disaster-recovery.md) , kde najdete kroky k obnovení do nové oblasti.

## <a name="next-steps"></a>Další kroky

* [Přehled vývoje SQL Database](sql-database-develop-overview.md)
* [Obecné pokyny pro zpracování přechodných chyb](../best-practices-retry-general.md)
* [Knihovny připojení pro SQL Database a SQL Server](sql-database-libraries.md)
