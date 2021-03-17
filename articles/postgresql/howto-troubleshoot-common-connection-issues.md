---
title: Řešení potíží s připojením – Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak řešit potíže s připojením k Azure Database for PostgreSQL – na jeden server.
keywords: připojení PostgreSQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bff930153dc8941fbfe561edf963d5b1c1e7811f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014614"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Řešení potíží s připojením pro Azure Database for PostgreSQL – jeden server

Problémy s připojením mohou být způsobeny řadou věcí, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Dosáhlo se maximálního limitu u některých prostředků Azure Database for PostgreSQL.
* Problémy s infrastrukturou služby
* Údržba prováděná ve službě
* Přidělení výpočetních prostředků serveru se změní škálováním počtu virtuální jádra nebo přesunutím na jinou úroveň služby.

Obecně mohou být problémy s připojením k Azure Database for PostgreSQL klasifikovány takto:

* Přechodné chyby (krátkodobé nebo občasné)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

K přechodným chybám dochází při provádění údržby, systém zaznamená chybu s hardwarem nebo softwarem nebo změníte virtuální jádra nebo úroveň služby serveru. Služba Azure Database for PostgreSQL má vestavěnou vysokou dostupnost a je navržena tak, aby automaticky zmírnila tyto typy problémů. Vaše aplikace však ztratí připojení k serveru po krátkou dobu obvykle méně než 60 sekund. Některé události mohou občas trvat delší dobu, například když velká transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení potíží s přechodným připojením

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo v době, kdy aplikace nahlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je například Azure Database for PostgreSQL, by měly očekávat přechodné chyby a implementují logiku opakování pro zpracování těchto chyb namísto zpřístupněníí chyb aplikací uživatelům. Kontrola [nad zpracováním přechodných chyb připojení pro Azure Database for PostgreSQL](concepts-connectivity.md) osvědčenými postupy a pokyny pro návrh pro zpracování přechodných chyb.
3. V případě, že se server blíží omezení prostředků, může se zdát, že chyby můžou být přechodný problém s připojením. Viz [omezení v Azure Database for PostgreSQL](concepts-limits.md).
4. Pokud potíže s připojením trvají nebo pokud doba, po kterou vaše aplikace narazí na chybu, překračuje 60 sekund, nebo pokud se v daném dni zobrazí více výskytů chyby, zastavte žádost o podporu Azure výběrem možnosti **získat podporu** na webu [podpory Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Řešení trvalých chyb

Pokud se aplikace trvale nepřipojí k Azure Database for PostgreSQL, obvykle se jedná o problém s jedním z následujících způsobů:

* Konfigurace brány firewall serveru: Ujistěte se, že je brána firewall serveru Azure Database for PostgreSQL nakonfigurovaná tak, aby povolovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k vašemu databázovému serveru. IP adresy a porty serveru, které se nedají povolit, a také názvy aplikací, jako je PostgreSQL v některých branách firewall.
* Chyba uživatele: v připojovacím řetězci můžete mít chybné typové parametry připojení, jako je název serveru nebo chybějící přípona *\@ servername* v uživatelském jménu.
* Pokud se zobrazí chybová zpráva _Server není nakonfigurovaný tak, aby povoloval připojení IPv6_, počítejte s tím, že úroveň Basic nepodporuje koncové body služby virtuální sítě. Musíte odebrat koncový bod Microsoft. SQL z podsítě, která se pokouší připojit k základnímu serveru.
* Pokud se zobrazí chyba připojení _sslmode "* * *" neplatné, když se podpora SSL nezkompiluje v_ chybě, znamená to, že váš klient PostgreSQL nepodporuje protokol SSL. S největší pravděpodobností libpq na straně klienta nebyla zkompilována s příznakem--with-OpenSSL. Zkuste se připojit pomocí klienta PostgreSQL, který má podporu SSL. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení potíží s trvalým připojením

1. Nastavte [pravidla brány firewall](howto-manage-firewall-using-portal.md) tak, aby povolovala IP adresu klienta. Pro účely dočasného testování nastavte pravidlo brány firewall pomocí direktivy 0.0.0.0 jako počáteční IP adresu a jako koncovou IP adresu použijte 255.255.255.255. Tím se Server otevře na všech IP adresách. Pokud dojde k vyřešení problému s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídající omezené IP adresy nebo rozsah adres.
2. U všech bran firewall mezi klientem a internetem se ujistěte, že je port 5432 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení.
4. Projděte si stav služby na řídicím panelu. Pokud se domníváte, že se jedná o oblastní výpadky, přečtěte si téma [Přehled provozní kontinuity a Azure Database for PostgreSQL](concepts-business-continuity.md) kroky k obnovení do nové oblasti.

## <a name="next-steps"></a>Další kroky

* [Manipulace s přechodnými chybami připojení pro Azure Database for PostgreSQL](concepts-connectivity.md)
