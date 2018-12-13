---
title: Řešení běžných problémů s připojením ke službě Azure SQL Database
description: Postup identifikace a řešení běžných chyb připojení pro Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 53061cde5621be06df8f992278a6510d62e51934
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872574"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Řešení problémů s připojením ke službě Azure SQL Database
Při připojení ke službě Azure SQL Database selže, obdržíte [chybové zprávy](sql-database-develop-error-messages.md). Tento článek je centralizovaná téma, které vám pomohou s řešením problémům s připojením Azure SQL Database. Zavádí [nejčastější příčiny](#cause) z problémů s připojením, doporučuje [nástroje pro odstraňování potíží](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , která pomáhá identity problém a odstraňování potíží vysvětluje, jak vyřešit [přechodné chyby](#troubleshoot-transient-errors) a [trvalé nebo nepřechodných chyb](#troubleshoot-persistent-errors). 

Pokud narazíte na problémy s připojením, vyzkoušejte postup řešení potíží, které jsou popsány v tomto článku.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Příčina
Může být způsobeno problémy s připojením pomocí některé z následujících akcí:

* Nepodařilo se použít osvědčené postupy a pokyny pro návrh během procesu návrhu aplikace.  Zobrazit [přehled vývoje SQL Database](sql-database-develop-overview.md) začít.
* Azure SQL Database Rekonfigurace
* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* U některých prostředků Azure SQL Database se dosáhlo maximálního limitu

Obecně platí problémy s připojením ke službě Azure SQL Database můžete klasifikovat takto:

* [Přechodné chyby (krátkodobé nebo přerušované)](#troubleshoot-transient-errors)
* [Trvalé nebo nepřechodných chyb (chyb, které se pravidelně opakují)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Zkuste Poradce při potížích pro problémy s připojením k Azure SQL Database
Pokud narazíte na chyby konkrétních připojení, zkuste [tento nástroj](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), které se vám pomohou vám rychle identifikovat a vyřešit váš problém.

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

Když se aplikace připojí ke službě Azure SQL database, zobrazí se následující chybová zpráva:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Tato chybová zpráva se nejčastěji přechodné (krátkodobou).
> 
> 

Tato chyba nastane, pokud se databázi Azure přesunout (nebo překonfigurovat) a vaše aplikace ztratí připojení ke službě SQL database. Události Rekonfigurace databáze SQL, k nimž dochází kvůli plánované události (například upgrade softwaru) nebo neplánované události (například chybové ukončení procesu, nebo služby Vyrovnávání zatížení). Většina události změny konfigurace jsou obecně krátkodobé a by se měl dokončit za méně než 60 sekund nejvíce. Však tyto události může někdy trvat déle dokončit, například když způsobí, že velké transakce dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroky k vyřešení problémů s připojením přechodné

1. Zkontrolujte, [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) všechny známé výpadky, ke kterým došlo během doby, během kterého byly oznámeny chyby aplikací.
2. Aplikace, které se připojují ke cloudové službě, jako jsou Azure SQL Database by měl očekávat pravidelné Rekonfigurace události a implementovat logiku zpracování těchto chyb místo za chyby aplikace zpřístupnění uživatelům opakování. Zkontrolujte [přechodné chyby](sql-database-connectivity-issues.md) oddílu a osvědčené postupy a pokyny pro návrh na [přehled vývoje SQL Database](sql-database-develop-overview.md) Další informace a obecná strategie opakování. Poté, najdete v ukázkách kódu na [připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md) pro konkrétní.
3. Jako databázi přistupuje k jeho omezení prostředků, může to vypadat třeba přechodné potíže s připojením. Zobrazit [omezení prostředků](sql-database-resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Pokud se potíže s připojením k pokračovat nebo pokud překročí 60 sekund doby trvání, pro kterou vaše aplikace došlo k chybě, nebo když se zobrazí více výskytů chyby v daném dni souborů žádost o podporu Azure tak, že vyberete **získat podporu**na [podpory Azure](https://azure.microsoft.com/support/options) lokality.

## <a name="troubleshoot-persistent-errors"></a>Řešení potíží s chybami trvalé
Pokud aplikace trvale nepodaří připojit ke službě Azure SQL Database, obvykle znamená problém s jedním z následujících akcí:

* Konfigurace brány firewall. Azure SQL database nebo na straně klienta brána firewall blokuje připojení ke službě Azure SQL Database.
* Změnu konfigurace na straně klienta sítě: například novou IP adresu nebo proxy server.
* Chyba uživatele: například chybně parametry připojení, jako je například název serveru v připojovacím řetězci.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroky při řešení problémů trvalého připojení
1. Nastavit [pravidla brány firewall](sql-database-configure-firewall-settings.md) by klientovi umožňoval IP adresu. Pro dočasné pro účely testování nastavte pravidla brány firewall pomocí 0.0.0.0 jako výchozí rozsah IP adres a pomocí 255.255.255.255 jako koncový rozsah IP adres. Otevře se server pro všechny IP adresy. Pokud to řeší problém s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídajícím způsobem omezené IP adresu nebo rozsah adres. 
2. Na všechny brány firewall mezi klientem a Internetu Ujistěte se, že port 1433 je otevřený pro odchozí připojení. Kontrola [konfigurace brány Windows Firewall pro povolení přístupu k serveru SQL](https://msdn.microsoft.com/library/cc646023.aspx) a [hybridní Identity požadované porty a protokoly](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) pro další ukazatele související s další porty, které budete muset otevřít pro Azure Ověřování služby Active Directory.
3. Ověřte připojovací řetězec a další nastavení připojení. Naleznete v části připojovací řetězec [tématu problémy s připojením](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Kontrola stavu služeb v řídicím panelu. Pokud si myslíte, dojde k oblastnímu výpadku, přečtěte si téma [zotavení z výpadku](sql-database-disaster-recovery.md) pokyny k obnovení do nové oblasti.

## <a name="next-steps"></a>Další postup
* [Hledání v dokumentaci v Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Prohlédněte si nejnovější aktualizace ke službě Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Další zdroje informací:
* [Přehled vývoje SQL Database](sql-database-develop-overview.md)
* [Obecné pokyny přechodné zpracování chyb](../best-practices-retry-general.md)
* [Připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md)

