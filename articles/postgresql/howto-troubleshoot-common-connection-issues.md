---
title: Poradce při potížích s připojením – Databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak řešit problémy s připojením k Azure Database for PostgreSQL – jeden server.
keywords: postgresql připojení,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 593dc060ff92c7f4bc338e235dd1a0dd9f303ab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767613"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Řešení problémů s připojením k databázi Azure pro PostgreSQL – jeden server

Problémy s připojením mohou být způsobeny různými věcmi, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Maximální limit dosažený u některých prostředků Azure Database pro postgreSQL
* Problémy s infrastrukturou služby
* Údržba prováděná v provozu
* Přidělení výpočetních prostředků serveru se změní škálováním počtu virtuálních jader nebo přesunutím na jinou úroveň služby.

Obecně platí, že problémy s připojením k Azure Database pro PostgreSQL lze klasifikovat takto:

* Přechodné chyby (krátkodobé nebo přerušované)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Poradce při potížích s přechodným chybám

Při provádění údržby dochází k přechodným chybám, systém narazí na chybu s hardwarem nebo softwarem nebo změníte virtuální jádra nebo úroveň služeb serveru. Služba Azure Database for PostgreSQL má integrovanou vysokou dostupnost a je navržena tak, aby tyto typy problémů automaticky zmírnila. Aplikace však ztratí připojení k serveru na krátkou dobu obvykle kratší než 60 sekund. Některé události může občas trvat déle zmírnit, například když velké transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení problémů s přechodným připojením

1. Zkontrolujte [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo během doby, ve které byly hlášeny chyby aplikací.
2. Aplikace, které se připojují ke cloudové službě, jako je Například Azure Database for PostgreSQL, by měly očekávat přechodné chyby a implementovat logiku opakování pro zpracování těchto chyb namísto jejich zobrazení jako chyby aplikace pro uživatele. Zkontrolujte [Zpracování přechodných chyb připojení pro Azure Database for PostgreSQL](concepts-connectivity.md) pro osvědčené postupy a pokyny pro návrh pro zpracování přechodných chyb.
3. Jako server blíží jeho omezení prostředků, chyby se může zdát být přechodný problém připojení. Viz [Omezení v databázi Azure pro PostgreSQL](concepts-limits.md).
4. Pokud problémy s připojením přetrvávají nebo pokud doba trvání, po kterou vaše aplikace narazí na chybu, překročí 60 sekund nebo pokud se v daný den zobrazí více výskytů chyby, nasuňte žádost o podporu Azure výběrem **možnosti Získat podporu** na webu [podpory Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Poradce při potížích s trvalými chybami

Pokud se aplikaci trvale nedaří připojit k Azure Database pro PostgreSQL, obvykle označuje problém s jedním z následujících:

* Konfigurace brány firewall serveru: Ujistěte se, že brána firewall serveru Azure Database for PostgreSQL je nakonfigurovaná tak, aby umožňovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k databázovému serveru. Ip adresy a porty serveru, které nelze povolit, stejně jako názvy aplikací, jako je PostgreSQL v některých firewallech.
* Chyba uživatele: Pravděpodobně máte chybně zadané parametry připojení, například název serveru * \@* v připojovacím řetězci nebo chybějící příponu názvu serveru v uživatelském jménu.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení přetrvávajících problémů s připojením

1. Nastavte [pravidla brány firewall](howto-manage-firewall-using-portal.md) tak, aby umožňovala IP adresu klienta. Pouze pro dočasné testovací účely nastavte pravidlo brány firewall, které používá 0.0.0.0 jako počáteční adresu IP a jako koncovou adresu IP 255.255.255. Tím se server otevře všem IP adresám. Pokud se tím problém s připojením vyřeší, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro vhodně omezenou adresu IP nebo rozsah adres.
2. U všech bran firewall mezi klientem a Internetem zkontrolujte, zda je port 5432 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení.
4. Zkontrolujte stav služby na řídicím panelu. Pokud si myslíte, že je místní výpadek, najdete [v tématu Přehled kontinuity podnikání s Azure Database pro PostgreSQL](concepts-business-continuity.md) kroky k obnovení do nové oblasti.

## <a name="next-steps"></a>Další kroky

* [Zpracování přechodných chyb připojení pro Azure Database pro PostgreSQL](concepts-connectivity.md)
