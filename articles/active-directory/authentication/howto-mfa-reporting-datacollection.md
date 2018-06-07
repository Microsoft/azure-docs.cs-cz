---
title: Shromažďování dat uživatele služby Azure Multi-Factor Authentication
description: Jaké informace se používají pro ověřování uživatelů službou Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: article
ms.date: 05/01/2018
ms.openlocfilehash: d28bb4b8e171ef6189f81acc337088b3c5499ccf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654769"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Shromažďování dat uživatele služby Azure Multi-Factor Authentication

Tento dokument vysvětluje, jak najít uživatele shromažďované nástrojem Server Azure Multi-Factor Authentication (MFA Server) a Azure MFA (založené na cloudu) v případě, že chcete odebrat.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Shromážděné informace

MFA Server NPS rozšíření a Windows Server 2016 Azure MFA adaptér služby AD FS shromažďovat a ukládat následující informace pro 90 dní.

Pokusy o ověření (používá se pro vytváření sestav a řešení potíží s):

- Časové razítko
- Uživatelské jméno
- Jméno
- Příjmení
- E-mailová adresa
- Skupina uživatelů
- Metoda ověřování (telefonní hovor, Text tokenu OATH zpráva, mobilní aplikace)
- Režim telefonního hovoru (standardní, kód PIN)
- Směr textové zprávy (jednosměrné, obousměrný)
- Režim textové zprávy (jednorázového HESLA, OTP + kód PIN)
- Režim mobilní aplikace (standardní, kód PIN)
- Režim tokenu OATH (Standard, PIN kód)
- Typ ověření
- Název aplikace
- Kód země primárního volání
- Volání primární telefonní číslo
- Primární volání rozšíření
- Primární volání ověření
- Výsledek primární volání
- Kód země záložního volání
- Zálohování volání telefonní číslo
- Rozšíření zálohování volání
- Zálohování volání ověření
- Výsledek volání zálohy
- Celkové ověření
- Celkový výsledek
- Výsledky
- Ověřeno
- Výsledek
- Inicializace IP adresu
- Zařízení
- Token zařízení
- Typ zařízení
- Verze mobilní aplikace
- Verze operačního systému
- Výsledek
- Použít kontrolu pro oznámení

Počet aktivací (počet pokusů o aktivovat účet mobilní aplikace Microsoft Authenticator):
- Uživatelské jméno
- Název účtu
- Časové razítko
- Získání aktivační kód výsledku
- Aktivovat úspěch
- Aktivovat chyby
- Výsledek stavu aktivace
- Název zařízení
- Typ zařízení
- Verze aplikace
- Token OATH povoleno

Bloky (používá k určení stavu blokované a pro generování sestav):

- Časové razítko bloku
- Blok jiným uživatelským jménem
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Telefonní číslo formátu
- Linka
- Vyčištění rozšíření
- Blokováno
- Důvod blokování
- Časové razítko dokončení
- Dokončení důvod
- Uzamčení účtu
- Upozornění na podvod
- Upozornění na podvod není blokované.
- Jazyk

Přeskočení (používá se pro vytváření sestav):

- Nepoužívat časové razítko
- Počet sekund jednorázového přihlášení
- Jednorázové přihlášení pomocí uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Telefonní číslo formátu
- Linka
- Vyčištění rozšíření
- Důvod k jednorázovému přihlášení
- Časové razítko dokončení
- Dokončení důvod
- Použít alternativní metody komunikace

Změny (používá se k synchronizaci změny uživatelů MFA Server nebo AAD):

- Změnit časové razítko
- Uživatelské jméno
- Nový kód země
- Nové telefonní číslo
- Nové číslo linky
- Nový kód země záložního telefonu
- Nové telefonní číslo záložního telefonu
- Nové rozšíření zálohování
- Nový kód PIN
- Je vyžadována změna kódu PIN
- Původní Token zařízení
- Nový token zařízení

## <a name="gather-data-from-mfa-server"></a>Shromáždění dat ze serveru MFA

Následující proces pro MFA Server verze 8.0 nebo vyšší mohou správci exportovat všechna data pro uživatele:

- Přihlaste se k serveru MFA, přejděte na **uživatelé** , vyberte uživatele a klikněte **upravit** tlačítko. Pořizovat snímky obrazovky (Alt-PrtScn) jednotlivé karty můžete poskytnout uživateli jejich aktuální nastavení vícefaktorového ověřování.
- Z příkazového řádku serveru MFA, spusťte následující příkaz Změna cesty podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` k vytvoření JSON souboru formátu.
- Správci mohou také pomocí operace webové služby sady SDK GetUserGdpr jako možnost pro export všech vícefaktorového ověřování cloudové služby informace shromážděné pro daného uživatele nebo začlenit do větší řešení generování sestav.
- Hledání `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` a všechny zálohy pro "<username>" (do vyhledávání přidat uvozovky) k vyhledání všech instancí záznamu uživatele se přidat nebo změnit.
   - Tyto záznamy můžete omezené (ale ne eliminovat) zrušením zaškrtnutí políčka **"Protokolovat změny uživatelů"** v UX Server MFA, části protokolování, karta soubory protokolu.
   - Pokud je nakonfigurovaná syslog, a **"Protokolovat změny uživatelů"** se změnami UX Server MFA, části protokolování, karta Syslog, pak položky protokolu se dají shromáždit z syslog místo.
- Soubory týkající se ověřování, které pokusy jsou považovány za provozu a hromadnými informacím pomocí exportu MultiFactorAuthGdpr.exe nebo sady SDK webové služby protokolu další výskyty uživatelské jméno v MultiFactorAuthSvc.log a druhý Server MFA GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Odstranění dat z MFA serveru

Z příkazového řádku serveru MFA, spusťte následující příkaz Změna cesty podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` můžete odstranit všechny vícefaktorového ověřování cloudové služby informace shromážděné pro tohoto uživatele.

- Odstranění dat obsažených v exportu v reálném čase, ale může trvat až 30 dnů pro provozní nebo hromadnými data plně odeberou.
- Správci mohou také pomocí operace webové služby sady SDK DeleteUserGdpr jako možnost odstranit všechny vícefaktorového ověřování cloudové služby informace shromážděné pro daného uživatele nebo začlenit do větší řešení generování sestav.

## <a name="gather-data-from-nps-extension"></a>Shromažďování dat ze serveru NPS rozšíření

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost na pro Export.

- MFA informace je obsažena v exportu, což může trvat hodiny nebo i dny.
- Výskyty uživatelské jméno v AzureMfa/ověřovacího/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh a protokoly událostí AzureMfa/AuthZ/AuthZOptCh jsou považovány za provozu a hromadnými na informacích uvedených v exportu.

## <a name="delete-data-from-nps-extension"></a>Odstranění dat z rozšíření serveru NPS

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost pro účet zavřít odstraníte všechny vícefaktorového ověřování cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů dat na plně odeberou.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Shromažďování dat ze systému Windows Server 2016 Azure MFA adaptér služby AD FS

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost na pro Export. 

- MFA informace je obsažena v exportu, což může trvat hodiny nebo i dny.
- Výskyty uživatelské jméno v protokolech událostí AD FS trasování/Debug (Pokud je povoleno) se považují za provozu a hromadnými na informacích uvedených v exportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Odstranění dat z Windows serveru 2016 Azure MFA adaptér služby AD FS

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost pro účet zavřít odstraníte všechny vícefaktorového ověřování cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů dat na plně odeberou.

## <a name="gather-data-for-azure-mfa"></a>Shromažďování dat pro Azure MFA

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost na pro Export.

- MFA informace je obsažena v exportu, což může trvat hodiny nebo i dny.

## <a name="delete-data-for-azure-mfa"></a>Odstranit Data pro Azure MFA

Použití [portál Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview.) vytvořte žádost pro účet zavřít odstraníte všechny vícefaktorového ověřování cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů dat na plně odeberou.

## <a name="next-steps"></a>Další postup

[MFA Server zasílání zpráv](howto-mfa-reporting.md)
