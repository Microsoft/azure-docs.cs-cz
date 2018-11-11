---
title: Řešení problémů s připojením ke službě Azure Database pro MariaDB | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže s připojením ke službě Azure Database pro MariaDB.
keywords: připojení mariadb, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
services: mariadb
author: janeng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 63ccb2fe73a411ac7e4fca663f0d6b47647c02f4
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347683"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Řešení problémů s připojením ke službě Azure Database pro MariaDB

Problémy s připojením může způsobovat různé věci, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* V některých – Azure Database pro MariaDB prostředky se dosáhlo maximálního limitu
* Problémy s infrastrukturou služby
* Ve službě právě probíhá údržba
* Přidělení výpočetní serveru při změně škálování počtu virtuálních jader nebo přesun do jiné úrovně

Problémy s připojením ke službě Azure Database pro MariaDB obecně můžete klasifikovat následujícím způsobem:

* Přechodné chyby (krátkodobé nebo přerušované)
* Trvalé nebo nepřechodných chyb (chyb, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

Při provádění údržby systému dojde k chybě hardwaru nebo softwaru či změnit na úrovni virtuálních jader nebo služby v rámci vašeho serveru dojde k přechodným chybám. Azure Database pro MariaDB service má integrovanou vysokou dostupnost a je určený pro zmírnění těchto typů problémů automaticky. Ale aplikace ztratí připojení k serveru pro krátké době obvykle méně než 60 sekund nejvíce. Některé události může někdy trvat delší dobu zmírnit, například když způsobí, že velké transakce dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroky k vyřešení problémů s připojením přechodné

1. Zkontrolujte, [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) všechny známé výpadky, ke kterým došlo během doby, ve kterém byly oznámeny chyby aplikace.
2. Aplikace, které se připojují ke cloudové službě, jako jsou Azure Database pro MariaDB by měla očekávat přechodné chyby a implementovat logiku zpracování těchto chyb místo za chyby aplikace zpřístupnění uživatelům opakování. Kontrola [zpracování připojení přechodných chyb pro službu Azure Database pro MariaDB](concepts-connectivity.md) osvědčené postupy a pokyny k návrhu pro zpracování přechodných chyb.
3. Jako server přiblíží její omezení prostředků, zdá se, že chyby můžou být přechodné potíže s připojením. Zobrazit [omezení ve službě Azure Database pro MariaDB](concepts-limits.md).
4. Pokud se potíže s připojením k pokračovat nebo pokud překročí 60 sekund doby trvání, pro kterou vaše aplikace došlo k chybě, nebo když se zobrazí více výskytů chyby v daném dni souborů žádost o podporu Azure tak, že vyberete **získat podporu**na [podpory Azure](https://azure.microsoft.com/support/options) lokality.

## <a name="troubleshoot-persistent-errors"></a>Řešení potíží s chybami trvalé

Pokud aplikace trvale nepodaří připojit ke službě Azure Database pro MariaDB, obvykle znamená problém s jedním z následujících akcí:

* Konfigurace brány firewall: The – Azure Database pro MariaDB serveru nebo na straně klienta brána firewall neblokuje připojení.
* Změnu konfigurace na straně klienta sítě: byla přidána nová IP adresa nebo proxy server.
* Chyba uživatele: například může být chybně se zadala některá připojení parametry, jako je například název serveru v připojovacím řetězci nebo chybějící *@servername* příponu do pole uživatelské jméno.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroky při řešení problémů trvalého připojení

1. Nastavit [pravidla brány firewall](howto-manage-firewall-portal.md) by klientovi umožňoval IP adresu. Pro dočasné pouze pro účely testování nastavte pravidla brány firewall pomocí 0.0.0.0 jako počáteční IP adresa a jako koncovou IP adresu použijete 255.255.255.255. Otevře se server pro všechny IP adresy. Pokud to řeší problém s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídajícím způsobem omezené IP adresu nebo rozsah adres.
2. Na všechny brány firewall mezi klientem a Internetu Ujistěte se, že port 3306 je otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení. Kontrola [postupy připojení aplikací ke službě Azure Database pro MariaDB](howto-connection-string.md).
4. Kontrola stavu služeb v řídicím panelu. Pokud si myslíte, dojde k oblastnímu výpadku, přečtěte si téma [přehled kontinuity obchodních procesů ve službě Azure Database pro MariaDB](concepts-business-continuity.md) pokyny k obnovení do nové oblasti.

## <a name="next-steps"></a>Další postup

* [Řešení chyb přechodné připojení pro službu Azure Database pro MariaDB](concepts-connectivity.md)
