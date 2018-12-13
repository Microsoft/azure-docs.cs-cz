---
title: Řešení problémů s připojením ke službě Azure Database for PostgreSQL | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže s připojením ke službě Azure Database for PostgreSQL.
keywords: připojení k postgresql, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 67383db4bd1d57d194e10de2dc1964532b3619a4
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53160807"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql"></a>Řešení problémů s připojením ke službě Azure Database for PostgreSQL

Problémy s připojením může způsobovat různé věci, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* V některých – Azure Database for postgresql – prostředky se dosáhlo maximálního limitu
* Problémy s infrastrukturou služby
* Ve službě právě probíhá údržba
* Přidělení výpočetní serveru při změně škálování počtu virtuálních jader nebo přesun do jiné úrovně

Obecně platí problémy s připojením ke službě Azure Database for PostgreSQL můžete klasifikovat takto:

* Přechodné chyby (krátkodobé nebo přerušované)
* Trvalé nebo nepřechodných chyb (chyb, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

Při provádění údržby systému dojde k chybě hardwaru nebo softwaru či změnit na úrovni virtuálních jader nebo služby v rámci vašeho serveru dojde k přechodným chybám. Služba Azure Database for postgresql – má integrovanou vysokou dostupnost a je určený pro zmírnění těchto typů problémů automaticky. Ale aplikace ztratí připojení k serveru pro krátké době obvykle méně než 60 sekund nejvíce. Některé události může někdy trvat delší dobu zmírnit, například když způsobí, že velké transakce dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroky k vyřešení problémů s připojením přechodné

1. Zkontrolujte, [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) všechny známé výpadky, ke kterým došlo během doby, ve kterém byly oznámeny chyby aplikace.
2. Aplikace, které se připojují ke cloudové službě, jako jsou Azure Database for PostgreSQL by měla očekávat přechodné chyby a implementovat logiku ke zpracování těchto chyb, které místo za chyby aplikace zpřístupnění uživatelům zkuste to znovu. Kontrola [zpracování připojení přechodných chyb pro službu Azure Database for PostgreSQL](concepts-connectivity.md) osvědčené postupy a pokyny k návrhu pro zpracování přechodných chyb.
3. Jako server přiblíží její omezení prostředků, zdá se, že chyby můžou být přechodné potíže s připojením. Zobrazit [omezení ve službě Azure Database for PostgreSQL](concepts-limits.md).
4. Pokud se potíže s připojením k pokračovat nebo pokud překročí 60 sekund doby trvání, pro kterou vaše aplikace došlo k chybě, nebo když se zobrazí více výskytů chyby v daném dni souborů žádost o podporu Azure tak, že vyberete **získat podporu**na [podpory Azure](https://azure.microsoft.com/support/options) lokality.

## <a name="troubleshoot-persistent-errors"></a>Řešení potíží s chybami trvalé

Pokud aplikace trvale nepodaří připojit ke službě Azure Database for PostgreSQL, obvykle znamená problém s jedním z následujících akcí:

* Konfigurace brány firewall serveru: Ujistěte se, že Azure Database for PostgreSQL serverová brána firewall nakonfigurována, aby umožňovala připojení z klienta, včetně proxy servery a brány.
* Konfigurace brány firewall klienta: Brána firewall na váš klient musí umožňovat připojení k vašemu databázovému serveru. IP adresy a porty, ke které nelze na serveru musí být povoleny a také názvy aplikací, například PostgreSQL v některé brány firewall.
* Chyba uživatele: Může být chybně parametry připojení, jako je například název serveru v připojovacím řetězci nebo chybějící *@servername* příponu do pole uživatelské jméno.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroky při řešení problémů trvalého připojení

1. Nastavit [pravidla brány firewall](howto-manage-firewall-using-portal.md) by klientovi umožňoval IP adresu. Pro dočasné pouze pro účely testování nastavte pravidla brány firewall pomocí 0.0.0.0 jako počáteční IP adresa a jako koncovou IP adresu použijete 255.255.255.255. Otevře se server pro všechny IP adresy. Pokud to řeší problém s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídajícím způsobem omezené IP adresu nebo rozsah adres.
2. Na všechny brány firewall mezi klientem a Internetu Ujistěte se, že port 3306 je otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení.
4. Kontrola stavu služeb v řídicím panelu. Pokud si myslíte, dojde k oblastnímu výpadku, přečtěte si téma [přehled kontinuity obchodních procesů ve službě Azure Database for PostgreSQL](concepts-business-continuity.md) pokyny k obnovení do nové oblasti.

## <a name="next-steps"></a>Další postup

* [Řešení chyb přechodné připojení pro službu Azure Database for PostgreSQL](concepts-connectivity.md)
