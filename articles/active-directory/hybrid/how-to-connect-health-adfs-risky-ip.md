---
title: Azure AD Connect Health se AD FSm rizikovou zprávou IP | Microsoft Docs
description: Popisuje Azure AD Connect Health AD FS rizikové zprávy IP.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f8a60c5b955096f1661877416936b747a16979
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306391"
---
# <a name="risky-ip-report-public-preview"></a>Sestava rizikových IP adres (Public Preview)
Zákazníci AD FS můžou zveřejnit koncové body ověřování hesla pro Internet, aby koncovým uživatelům poskytovali služby ověřování pro přístup k aplikacím SaaS, jako je Microsoft 365. V takovém případě může pochybný aktér zkoušet přihlášení do systému AD FS za účelem uhodnutí hesla koncového uživatele a získání přístupu k prostředkům aplikace. Služba AD FS od verze AD FS ve Windows Serveru 2012 R2 poskytuje funkci uzamčení účtu pro extranet, která brání těmto typům útoku. Pokud používáte nižší verzi, důrazně doporučujeme upgradovat systém AD FS na Windows Server 2016. <br />

Kromě toho je možné, aby se jedna IP adresa pokoušela o přihlášení za několik uživatelů. V takových případech je možné počet pokusů na jednoho uživatele omezit prahovou hodnotou pro ochranu uzamčení účtu ve službě AD FS. Azure AD Connect Health nyní poskytuje sestavu rizikových IP adres, která tuto podmínku detekuje a upozorňuje na její výskyt správce. Klíčové výhody této sestavy jsou následující: 
- Detekce IP adres, které překročí prahovou hodnotu neúspěšných pokusů o přihlášení na základě hesla
- Podpora neúspěšných přihlášení kvůli špatnému heslu nebo kvůli stavu uzamčení extranetu
- E-mailová oznámení s přizpůsobitelným nastavením e-mailu, která okamžitě upozorní správce, když k tomu dojde
- Přizpůsobitelná nastavení prahových hodnot, která odpovídají zásadám zabezpečení organizace
- Sestavy ke stažení pro účely offline analýzy a integrace s dalšími systémy prostřednictvím automatizace

> [!NOTE]
> Pokud chcete tuto sestavu použít, ujistěte se, že je povolené auditování AD FS. Další informace najdete v článku o [povolení auditování služby AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Pro přístup k náhledu jsou vyžadována oprávnění Globálního správce nebo [Čtenáře zabezpečení](../../role-based-access-control/built-in-roles.md#security-reader).  
> 

## <a name="what-is-in-the-report"></a>Co je v sestavě?
IP adresy klienta neúspěšného přihlášení jsou agregovány prostřednictvím proxy serverů webových aplikací. Každá položka v sestavě rizikových IP adres ukazuje agregované informace o neúspěšných aktivitách přihlášení ke službě AD FS, které překročí určenou prahovou hodnotu. Obsahuje následující informace: ![ snímek obrazovky, který zobrazuje rizikové zprávy IP se zvýrazněnými záhlavími sloupců.](./media/how-to-connect-health-adfs/report4a.png)

| Položky sestavy | Popis |
| ------- | ----------- |
| Časové razítko | Ukazuje časové razítko na základě místního času webu Azure Portal při zahájení časového intervalu zjišťování.<br /> Všechny denní události se generují o půlnoci UTC. <br />Hodinové události mají hodnotu časového razítka zaokrouhlenou na celou hodinu. Čas spuštění první aktivity můžete vyhledat v položce firstAuditTimestamp v exportovaném souboru. |
| Typ triggeru | Ukazuje typ časového intervalu zjišťování. Typ triggeru agregace určuje, jestli se aktivuje každou hodinu nebo každý den. To je užitečně k rozpoznání útoku hrubou silou s vysokou frekvencí od pomalého útoku, při kterém se počet pokusů distribuuje během celého dne. |
| IP adresa | Jedna riziková IP adresa, ze které probíhaly aktivity přihlášení se špatným heslem nebo uzamčením extranetu. Může to být adresa IPv4 nebo IPv6 adresa. |
| Počet chybných zadání hesla | Počet chybných zadání hesla, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. K chybnému zadání hesla může u určitých uživatelů dojít vícekrát. Všimněte si, že toto číslo nezahrnuje neúspěšné pokusy kvůli heslům, kterým vypršela platnost. |
| Počet chyb uzamčení extranetu | Počet chyb uzamčení extranetu, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. K chybám uzamčení extranetu může u určitých uživatelů dojít vícekrát. Tato položka se zobrazí pouze v případě, že je uzamčení extranetu nakonfigurované ve službě AD FS (verze 2012R2 nebo novější). <b>Poznámka:</b> Důrazně doporučujeme tuto funkci zapnout, pokud umožňujete přihlášení pomocí hesel z extranetu. |
| Počet vyzkoušených jedinečných uživatelů | Počet vyzkoušených jedinečných uživatelských účtů, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. Tato položka poskytuje mechanismus pro odlišení vzorce útoku na jednoho uživatele od vzorce útoku na více uživatelů.  |

Například níže uvedená položka sestavy značí, že 28. 2. 2018 v časovém intervalu od 18:00 do 19:00 u IP adresy <i>104.2XX.2XX.9</i> nedošlo k žádnému chybnému zadání hesla, ale došlo k 284 chybám uzamčení extranetu. V rámci kritérií to mělo dopad na 14 jedinečných uživatelů. Událost aktivity překročila určenou hodinovou prahovou hodnotu sestavy. 

![Snímek obrazovky, který ukazuje příklad záznamu rizikové IP adresy.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - V seznamu sestavy se zobrazí pouze aktivity překračující určenou prahovou hodnotu. 
> - V této sestavě je možné dojít maximálně 30 dnů nazpět.
> - V této sestavě upozornění se nezobrazují IP adresy Exchange ani privátní IP adresy. Ty jsou však stále součástí exportovaného seznamu. 
>

![Snímek obrazovky zobrazující sestavu rizikových IP adres se zvýrazněnou možností "Stáhnout", "nastavení oznámení" a "mezní hodnota".](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>IP adresy služby Vyrovnávání zatížení v seznamu
Nástroj pro vyrovnávání zatížení agreguje aktivity přihlášení, které selhaly, a překročení prahové hodnoty výstrahy. Pokud se vám zobrazují IP adresy nástroje pro vyrovnávání zatížení, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta. Nakonfigurujte ve svém nástroji pro vyrovnávání zatížení správně předávání IP adresy klienta. 

## <a name="download-risky-ip-report"></a>Stáhnout sestavu rizikových IP adres 
Pomocí funkce **Stáhnout** můžete z portálu služby Connect Health exportovat celý seznam rizikových IP adres za posledních 30 dnů. Výsledek exportu bude obsahovat všechny neúspěšné aktivity přihlášení ke službě AD FS v jednotlivých časových intervalech zjišťování, abyste si po exportu mohli upravit filtrování. Kromě zvýrazněných agregací na portálu bude výsledek exportu obsahovat také další podrobnosti o neúspěšných aktivitách přihlášení podle IP adresy:

|  Položky sestavy  |  Popis  | 
| ------- | ----------- | 
| firstAuditTimestamp | Ukazuje časové razítko prvního výskytu neúspěšných aktivit během časového intervalu zjišťování.  | 
| lastAuditTimestamp | Ukazuje časové razítko posledního výskytu neúspěšných aktivit během časového intervalu zjišťování.  | 
| attemptCountThresholdIsExceeded | Příznak, který značí, jestli aktuální aktivity překračují prahovou hodnotu pro upozornění.  | 
| isWhitelistedIpAddress | Příznak, který značí, jestli se daná IP adresa filtruje z upozorňování a generování sestav. Privátní IP adresy (<i>10.x.x.x, 172.x.x.x a 192.168.x.x</i>) a IP adresy Exchange se filtrují a jsou označené hodnotou True. Pokud se vám zobrazují rozsahy privátních IP adres, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta.  | 

## <a name="configure-notification-settings"></a>Konfigurovat nastavení oznámení
Kontakty pro správu sestavy je možné aktualizovat prostřednictvím **Nastavení oznámení**. Ve výchozím nastavení je e-mailové oznámení o upozornění na rizikové IP adresy vypnuté. Oznámení můžete zapnout přepnutím tlačítka v části Dostávat e-mailová oznámení o sestavě IP adres, které překročily prahovou hodnotu neúspěšné aktivity. Podobně jako v případě nastavení oznámení o obecných upozornění ve službě Connect Health můžete na tomto místě upravit určený seznam příjemců oznámení o sestavě rizikových IP adres. Při provádění změny můžete také upozornit všechny globální správce. 

## <a name="configure-threshold-settings"></a>Konfigurovat nastavení prahové hodnoty
Prahovou hodnotu pro upozornění můžete upravit prostřednictvím nastavení prahových hodnot. Pro začátek má systém nastavené výchozí prahové hodnoty. Nastavení prahových hodnot pro sestavu rizikových IP adres obsahuje čtyři kategorie:

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Položka prahové hodnoty | Popis |
| --- | --- |
| (Chybné U/P + uzamčení extranetu) / den  | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když součet počtu chybných zadání hesla a uzamčení extranetu za **den** překročí tuto prahovou hodnotu. |
| (Chybné U/P + uzamčení extranetu) / hodina | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když součet počtu chybných zadání hesla a uzamčení extranetu za **hodinu** překročí tuto prahovou hodnotu. |
| Uzamčení extranetu / den | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když počet uzamčení extranetu za **den** překročí tuto prahovou hodnotu. |
| Uzamčení extranetu / hodina| Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když počet uzamčení extranetu za **hodinu** překročí tuto prahovou hodnotu. |

> [!NOTE]
> - Změna prahových hodnot pro sestavu se projeví za hodinu od provedení změny nastavení. 
> - Stávající ohlášené položky změna prahových hodnot neovlivní. 
> - Doporučujeme analyzovat počet událostí, ke kterým ve vašem prostředí dochází, a upravit prahové hodnoty odpovídajícím způsobem. 
>
>

## <a name="faq"></a>Časté otázky
**Proč se v sestavě zobrazují rozsahy privátních IP adres?**  <br />
Privátní IP adresy (<i>10. x. x. x, 172. x. x. x & 192.168. x. x</i>) a IP adresy Exchange se filtrují a v seznamu schválených IP adres jsou označené jako true. Pokud se vám zobrazují rozsahy privátních IP adres, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta.

**Proč se v sestavě zobrazují IP adresy nástroje pro vyrovnávání zatížení?**  <br />
Pokud se vám zobrazují IP adresy nástroje pro vyrovnávání zatížení, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta. Nakonfigurujte ve svém nástroji pro vyrovnávání zatížení správně předávání IP adresy klienta. 

**Jak můžu zablokovat IP adresu?**  <br />
Zjištěné škodlivé IP adresy byste měli přidat do brány firewall nebo je blokovat v Exchange.   <br />

**Proč se mi v této sestavě nezobrazují žádné položky?** <br />
- Neúspěšné aktivity přihlášení nepřekračují nastavení prahových hodnot.
- Ujistěte se, že v seznamu serverů AD FS nejsou žádná aktivní upozornění Služba Health není aktuální.  Další informace o [řešení potíží s tímto upozorněním](how-to-connect-health-data-freshness.md).
- Na farmách služby AD FS není povolené auditování.

**Proč k sestavě nemám přístup?**  <br />
Vyžadují se oprávnění globálního správce nebo [čtenáře zabezpečení](../../role-based-access-control/built-in-roles.md#security-reader). Obraťte se s žádostí o udělení přístupu na globálního správce.


## <a name="next-steps"></a>Další kroky
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalace agenta Azure AD Connect Health](how-to-connect-health-agent-install.md)