---
title: Řešení potíží s připojením – Azure Database for MySQL
description: Přečtěte si, jak řešit potíže s připojením Azure Database for MySQL, včetně přechodných chyb, které vyžadují opakované pokusy, problémy s bránou firewall a výpadky.
keywords: připojení MySQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: c2a2fba0d23ef9102374979300fe850732137c31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109909"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Řešení potíží s připojením ke službě Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Problémy s připojením mohou být způsobeny řadou věcí, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Dosáhlo se maximálního limitu u některých prostředků Azure Database for MySQL.
* Problémy s infrastrukturou služby
* Údržba prováděná ve službě
* Přidělení výpočetních prostředků serveru se změní škálováním počtu virtuální jádra nebo přesunutím na jinou úroveň služby.

Obecně mohou být problémy s připojením k Azure Database for MySQL klasifikovány takto:

* Přechodné chyby (krátkodobé nebo občasné)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

K přechodným chybám dochází při provádění údržby, systém zaznamená chybu s hardwarem nebo softwarem nebo změníte virtuální jádra nebo úroveň služby serveru. Služba Azure Database for MySQL má vestavěnou vysokou dostupnost a je navržena tak, aby automaticky zmírnila tyto typy problémů. Vaše aplikace však ztratí připojení k serveru po krátkou dobu obvykle méně než 60 sekund. Některé události mohou občas trvat delší dobu, například když velká transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení potíží s přechodným připojením

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo v době, kdy aplikace nahlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je například Azure Database for MySQL, by měly očekávat přechodné chyby a implementují logiku opakování pro zpracování těchto chyb namísto zpřístupněníí chyb aplikací uživatelům. Kontrola [nad zpracováním přechodných chyb připojení pro Azure Database for MySQL](concepts-connectivity.md) osvědčenými postupy a pokyny pro návrh pro zpracování přechodných chyb.
3. V případě, že se server blíží omezení prostředků, může se zdát, že chyby můžou být přechodný problém s připojením. Viz [omezení v Azure Database for MySQL](concepts-limits.md).
4. Pokud potíže s připojením trvají nebo pokud doba, po kterou vaše aplikace narazí na chybu, překračuje 60 sekund, nebo pokud se v daném dni zobrazí více výskytů chyby, zastavte žádost o podporu Azure výběrem možnosti **získat podporu** na webu [podpory Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Řešení trvalých chyb

Pokud se aplikace trvale nepřipojí k Azure Database for MySQL, obvykle se jedná o problém s jedním z následujících způsobů:

* Konfigurace brány firewall serveru: Ujistěte se, že je brána firewall serveru Azure Database for MySQL nakonfigurovaná tak, aby povolovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k vašemu databázovému serveru. IP adresy a porty serveru, které se nedají povolit, a také názvy aplikací, jako je MySQL v některých branách firewall.
* Chyba uživatele: v připojovacím řetězci můžete mít chybné typové parametry připojení, jako je název serveru nebo chybějící přípona *\@ servername* v uživatelském jménu.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení potíží s trvalým připojením

1. Nastavte [pravidla brány firewall](howto-manage-firewall-using-portal.md) tak, aby povolovala IP adresu klienta. Pro účely dočasného testování nastavte pravidlo brány firewall pomocí direktivy 0.0.0.0 jako počáteční IP adresu a jako koncovou IP adresu použijte 255.255.255.255. Tím se Server otevře na všech IP adresách. Pokud dojde k vyřešení problému s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídající omezené IP adresy nebo rozsah adres.
2. U všech bran firewall mezi klientem a internetem se ujistěte, že je port 3306 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení. Přečtěte si, [Jak připojit aplikace k Azure Database for MySQL](howto-connection-string.md).
4. Projděte si stav služby na řídicím panelu. Pokud se domníváte, že se jedná o oblastní výpadky, přečtěte si téma [Přehled provozní kontinuity a Azure Database for MySQL](concepts-business-continuity.md) kroky k obnovení do nové oblasti.

## <a name="next-steps"></a>Další kroky

* [Manipulace s přechodnými chybami připojení pro Azure Database for MySQL](concepts-connectivity.md)
