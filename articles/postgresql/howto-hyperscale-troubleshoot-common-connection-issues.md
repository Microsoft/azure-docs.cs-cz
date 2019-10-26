---
title: Řešení potíží s připojením pro Azure Database for PostgreSQL – škálování (Citus)
description: Přečtěte si, jak řešit potíže s připojením Azure Database for PostgreSQL – Citus (škálování)
keywords: připojení PostgreSQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b812b730cebba4dbf0735f49f544e53bf7f8787c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952144"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Řešení potíží s připojením pro Azure Database for PostgreSQL – škálování (Citus)

Problémy s připojením mohou být způsobeny několika způsoby, například:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Byl dosažen limit počtu připojení pro skupinu serverů.
* Problémy s infrastrukturou služby
* Údržba služby
* Převzetí služeb při selhání uzlem koordinátora na novém hardwaru

Obecně platí, že problémy s připojením k škálovatelnému škálování můžou být klasifikovány takto:

* Přechodné chyby (krátkodobé nebo občasné)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Řešení přechodných chyb

K přechodným chybám dochází z několika důvodů. Nejběžnější zahrnuje údržbu systému, chybu s hardwarem nebo softwarem a vCore upgrady uzlů koordinátora.

Povolení vysoké dostupnosti pro uzly skupin serverů ve velkém měřítku může automaticky zmírnit tyto typy problémů. Vaše aplikace by ale měla být stále připravená, aby krátce ztratila připojení. I další události mohou trvat delší dobu, například když velká transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení potíží s přechodným připojením

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo v době, kdy aplikace nahlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je Citus (s měřítkem), by měly očekávat přechodné chyby a řádně reagovat. Například aplikace by měly implementovat logiku opakování pro zpracování těchto chyb namísto jejich zpřístupnění jako chyby aplikací uživatelům.
3. Když skupina serverů přistupuje k omezením prostředků, můžou se chyby zdát jako problémy s přechodným připojením. Zvýšení velikosti paměti RAM uzlů nebo přidávání pracovních uzlů a vyrovnávání dat může pomáhat.
4. Pokud potíže s připojením trvají nebo trvají déle než 60 sekund nebo pokud se k tomu dojde více než jednou denně, zastavte žádost o podporu Azure výběrem možnosti **získat podporu** na webu [podpory Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Řešení trvalých chyb

Pokud se aplikace trvale nepřipojí ke škálování (Citus), nejběžnější příčiny jsou chybná konfigurace brány firewall nebo chyba uživatele.

* Konfigurace brány firewall uzlu koordinátora: Ujistěte se, že je brána firewall serveru pro škálování na úrovni serveru nakonfigurovaná tak, aby povolovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k vašemu databázovému serveru. Některé brány firewall vyžadují, aby povolily nejen aplikaci podle názvu, ale povolily IP adresy a porty serveru.
* Chyba uživatele: dvakrát ověřte připojovací řetězec. Je možné, že máte v uživatelském jménu parametry, jako je název serveru, nebo zapomenout *\@příponu servername* .

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení potíží s trvalým připojením

1. Nastavte [pravidla brány firewall](howto-hyperscale-manage-firewall-using-portal.md) tak, aby povolovala IP adresu klienta. Pro účely dočasného testování nastavte pravidlo brány firewall pomocí direktivy 0.0.0.0 jako počáteční IP adresu a jako koncovou IP adresu použijte 255.255.255.255. Toto pravidlo otevře Server pro všechny IP adresy. Pokud pravidlo vyřeší problém s připojením, odeberte ho a vytvořte pravidlo brány firewall pro odpovídající omezené IP adresy nebo rozsah adres.
2. U všech bran firewall mezi klientem a internetem se ujistěte, že je port 5432 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení.
4. Projděte si stav služby na řídicím panelu.

## <a name="next-steps"></a>Další kroky

* Seznamte se s koncepty [pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-firewall-rules.md)
* Přečtěte si téma [Správa pravidel brány firewall pro Azure Database for PostgreSQL – Citus (škálování)](howto-hyperscale-manage-firewall-using-portal.md) .
