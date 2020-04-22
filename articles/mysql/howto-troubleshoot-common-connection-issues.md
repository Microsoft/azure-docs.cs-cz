---
title: Řešení problémů s připojením – Azure Database for MySQL
description: Zjistěte, jak řešit problémy s připojením k Azure Database for MySQL, včetně přechodných chyb vyžadujících opakování, problémy s bránou firewall a výpadky.
keywords: připojení mysql,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b22d47d177c4606396b0c8b2279301121c905ca2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768277"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Řešení potíží s připojením ke službě Azure Database for MySQL

Problémy s připojením mohou být způsobeny různými věcmi, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Maximální limit dosažený u některých prostředků Azure Database pro prostředky MySQL
* Problémy s infrastrukturou služby
* Údržba prováděná v provozu
* Přidělení výpočetních prostředků serveru se změní škálováním počtu virtuálních jader nebo přesunutím na jinou úroveň služby.

Obecně platí problémy s připojením k Azure Database for MySQL lze klasifikovat takto:

* Přechodné chyby (krátkodobé nebo přerušované)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Poradce při potížích s přechodným chybám

Při provádění údržby dochází k přechodným chybám, systém narazí na chybu s hardwarem nebo softwarem nebo změníte virtuální jádra nebo úroveň služeb serveru. Služba Azure Database for MySQL má integrovanou vysokou dostupnost a je navržena tak, aby tyto typy problémů automaticky zmírnila. Aplikace však ztratí připojení k serveru na krátkou dobu obvykle kratší než 60 sekund. Některé události může občas trvat déle zmírnit, například když velké transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení problémů s přechodným připojením

1. Zkontrolujte [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo během doby, ve které byly hlášeny chyby aplikací.
2. Aplikace, které se připojují ke cloudové službě, jako je například Azure Database for MySQL, by měly očekávat přechodné chyby a implementovat logiku opakování pro zpracování těchto chyb namísto jejich zobrazení jako chyby aplikace pro uživatele. Zkontrolujte [Zpracování přechodných chyb připojení pro Azure Database for MySQL](concepts-connectivity.md) pro osvědčené postupy a pokyny pro návrh pro zpracování přechodných chyb.
3. Jako server blíží jeho omezení prostředků, chyby se může zdát být přechodný problém připojení. Viz [Omezení v Azure Database for MySQL](concepts-limits.md).
4. Pokud problémy s připojením přetrvávají nebo pokud doba trvání, po kterou vaše aplikace narazí na chybu, překročí 60 sekund nebo pokud se v daný den zobrazí více výskytů chyby, nasuňte žádost o podporu Azure výběrem **možnosti Získat podporu** na webu [podpory Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Poradce při potížích s trvalými chybami

Pokud se aplikaci trvale nedaří připojit k Azure Database for MySQL, obvykle označuje problém s jedním z následujících:

* Konfigurace brány firewall serveru: Ujistěte se, že je brána firewall serveru Azure Database for MySQL nakonfigurovaná tak, aby umožňovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k databázovému serveru. IP adresy a porty serveru, které nelze povolit, stejně jako názvy aplikací, jako je MySQL v některých firewallech.
* Chyba uživatele: Pravděpodobně máte chybně zadané parametry připojení, například název serveru * \@* v připojovacím řetězci nebo chybějící příponu názvu serveru v uživatelském jménu.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení přetrvávajících problémů s připojením

1. Nastavte [pravidla brány firewall](howto-manage-firewall-using-portal.md) tak, aby umožňovala IP adresu klienta. Pouze pro dočasné testovací účely nastavte pravidlo brány firewall, které používá 0.0.0.0 jako počáteční adresu IP a jako koncovou adresu IP 255.255.255. Tím se server otevře všem IP adresám. Pokud se tím problém s připojením vyřeší, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro vhodně omezenou adresu IP nebo rozsah adres.
2. U všech bran firewall mezi klientem a Internetem zkontrolujte, zda je port 3306 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení. Přečtěte [si, jak připojit aplikace k Azure Database for MySQL](howto-connection-string.md).
4. Zkontrolujte stav služby na řídicím panelu. Pokud si myslíte, že je místní výpadek, najdete [v tématu Přehled kontinuity podnikání s Azure Database pro MySQL](concepts-business-continuity.md) kroky k obnovení do nové oblasti.

## <a name="next-steps"></a>Další kroky

* [Zpracování přechodných chyb připojení pro Azure Database for MySQL](concepts-connectivity.md)
