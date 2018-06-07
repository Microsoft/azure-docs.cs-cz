---
title: Řešení běžných problémů s připojením ke službě Azure SQL Database
description: Postup identifikovat a řešit běžné chyby připojení pro databázi SQL Azure.
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 06a0abd673785a572fb8efa8df199562dc13067f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648866"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Řešení potíží s připojením k databázi SQL Azure
Když se připojení k databázi SQL Azure nezdaří, zobrazí se [chybové zprávy](sql-database-develop-error-messages.md). Tento článek je centralizované téma, které vám pomůže vyřešit problémy s připojením k databázi SQL Azure. Zavádí [běžných příčin](#cause) z problémů s připojením, doporučuje [nástroje pro odstraňování potíží](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) se vám pomůže identity problém a k řešení potíží naleznete [přechodné chyby](#troubleshoot-transient-errors) a [trvalé nebo jiných přechodná chyb](#troubleshoot-persistent-errors). 

Pokud narazíte potíže s připojením, opakujte kroky řešení, které jsou popsané v tomto článku.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Příčina
Potíže s připojením, může být způsobeno některé z následujících:

* Nepodařilo se použít osvědčené postupy a pokyny k návrhu při návrhu aplikace.  V tématu [přehled vývoje SQL databáze](sql-database-develop-overview.md) začít pracovat.
* Změna konfigurace Azure SQL Database
* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Dosažen maximální limit na některé prostředky Azure SQL Database

Obecně platí potíže s připojením k databázi SQL Azure můžou být klasifikované následujícím způsobem:

* [Přechodné chyby (krátkodobou nebo přerušované)](#troubleshoot-transient-errors)
* [Trvalé nebo jiných přechodná chyb (chyby, které pravidelně opakovat)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Zkuste Poradce při potížích pro problémy s připojením k databázi SQL Azure
Pokud narazíte na chyby konkrétních připojení, zkuste [tento nástroj](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), které vám pomohou rychle identity, který se problém lze vyřešit.

## <a name="troubleshoot-transient-errors"></a>Řešení potíží s přechodné chyby

Když se aplikace připojí k databázi Azure SQL, zobrazí se následující chybová zpráva:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Tato chybová zpráva se nejčastěji přechodné (krátkodobou).
> 
> 

K této chybě dojde, pokud se databáze Azure přesunout (nebo překonfigurovat) a vaše aplikace ztratí připojení k databázi SQL. Události změny konfigurace databáze SQL dojít z důvodu plánované událostí (například upgrade softwaru), nebo neplánované událost (například síť procesu havárií, nebo Vyrovnávání zatížení). Většina události změny konfigurace jsou obecně krátkodobou a by se měly dokončit za méně než 60 sekund nejvíce. Ale tyto události může občas trvat déle dokončit, například když velké transakce způsobí obnovení dlouhodobé.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroky řešení potíží s přechodný připojení

1. Zkontrolujte [řídicího panelu služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadkům, které došlo k chybě během doby, během které ohlášeny chyby v aplikaci.
2. Aplikace, které se připojují ke cloudové službě, jako je Azure SQL Database by měl očekávat pravidelné Rekonfigurace události a implementovat opakujte logiku ke zpracování těchto chyb místo zpřístupnění jako chyby aplikace pro uživatele. Zkontrolujte [přechodné chyby](sql-database-connectivity-issues.md) části a osvědčené postupy a pokyny k návrhu na [přehled vývoje SQL databáze](sql-database-develop-overview.md) Další informace a obecné opakujte strategie. Potom, najdete v části Ukázky kódu v [knihovny připojení k databázi SQL a SQL Server](sql-database-libraries.md) pro konkrétní.
3. Jako databázi blíží jeho omezení prostředků, může to vypadat jako přechodný problém s připojením. V tématu [řešení potíží s výkonem](sql-database-troubleshoot-performance.md).
4. Pokud se potíže s připojením k pokračovat, nebo pokud doba, pro kterou vaše aplikace naráží na chybu překročí 60 sekund nebo pokud se zobrazí více výskyty chyby v daný den, soubor žádost o podporu Azure tak, že vyberete **získat podporu** na [podporu Azure](https://azure.microsoft.com/support/options) lokality.

## <a name="troubleshoot-persistent-errors"></a>Řešení potíží s trvalé chyby
Pokud aplikace se trvale nepodaří připojit k databázi SQL Azure, obvykle označuje potíže s jedním z následujících akcí:

* Konfigurace brány firewall. Azure SQL database nebo klienta brána firewall blokuje připojení k databázi SQL Azure.
* Změna konfigurace na straně klienta sítě: například novou IP adresu nebo proxy server.
* Uživatelskou chybu: například chybně připojení parametrů, třeba název serveru v připojovacím řetězci.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroky řešení potíží s trvalé připojení
1. Nastavit [pravidla brány firewall](sql-database-configure-firewall-settings.md) umožňuje klientovi IP adresu. Pro dočasné pro účely testování nastavte pravidla brány firewall používání 0.0.0.0 jako počáteční rozsah IP adres a 255.255.255.255 jako koncová rozsah IP adres. Otevře se server pro všechny IP adresy. Pokud to řeší problém s připojením, toto pravidlo odebrat a vytvořte pravidlo brány firewall pro správně omezené IP adresu nebo rozsah adres. 
2. Na všechny brány firewall mezi klientem a Internetu Ujistěte se, že port 1433 je otevřený pro odchozí připojení. Zkontrolujte [konfigurace brány Windows Firewall a povolit přístup k serveru SQL](https://msdn.microsoft.com/library/cc646023.aspx) a [hybridní Identity požadované porty a protokoly](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) pro další ukazatele související s další porty, které budete muset otevřít pro ověřování Azure Active Directory.
3. Ověřte připojovací řetězec a další nastavení připojení. Najdete v části připojovací řetězec v [tématu problémy s připojením](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Zkontrolujte stav služby v řídicím panelu. Pokud se domníváte, že je místní výpadku, přečtěte si téma [zotavit výpadku](sql-database-disaster-recovery.md) pokyny k obnovení do nové oblasti.

## <a name="next-steps"></a>Další postup
* [Řešení potíží s výkonem databáze SQL Azure](sql-database-troubleshoot-performance.md)
* [Hledání v dokumentaci v Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Zobrazit nejnovější aktualizace služby databáze SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Další zdroje informací:
* [Přehled vývoje SQL databáze](sql-database-develop-overview.md)
* [Obecné pokyny přechodné selhání zpracování](../best-practices-retry-general.md)
* [Knihovny připojení k databázi SQL a SQL Server](sql-database-libraries.md)

