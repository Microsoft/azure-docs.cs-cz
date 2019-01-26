---
title: Shromažďování dat uživatele služby Azure Multi-Factor Authentication
description: Jaké informace slouží ke snadnější, ověřování uživatelů pomocí ověřování Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: b96e1d6cfd476d90afaecd44eaa9861de34130ce
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081581"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Shromažďování dat uživatele služby Azure Multi-Factor Authentication

Tento dokument vysvětluje, jak vyhledat uživatele shromažďované nástrojem Azure Multi-Factor Authentication (MFA Server) a Azure MFA (Cloud-based) v případě, že chcete odebrat.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Shromážděné informace

MFA Server, rozšíření NPS a Windows Server 2016 Azure MFA adaptér služby AD FS shromažďovat a ukládat následující informace po dobu 90 dnů.

Pokusy o ověření (používá se pro vytváření sestav a řešení potíží s):

- Časové razítko
- Uživatelské jméno
- Jméno
- Příjmení
- E-mailová adresa
- Skupina uživatelů
- Metoda ověřování (telefonní hovor, Text tokenu OATH zpráva, mobilní aplikace)
- Režim telefonního hovoru (standardní, kód PIN)
- Směr textové zprávy (jednosměrné, pokud vytvoříte obousměrný)
- Režim textové zprávy (ověřování jednorázovým HESLEM, jednorázové heslo + kód PIN)
- Režim mobilní aplikace (standardní, kód PIN)
- Režim tokenu OATH (standardní, kód PIN)
- Typ ověření
- Název aplikace
- Kód země primárního volání
- Volání primární telefonní číslo
- Primární volání rozšíření
- Primární ověření volání
- Výsledek volání primární
- Kód země záložního volání
- Volání záložní telefonní číslo
- Rozšíření zálohování volání
- Volání zálohy ověřen
- Výsledek volání zálohy
- Celkové ověření
- Celkový výsledek
- Výsledky
- Ověřeno
- Výsledek
- Zahajuje se IP adresa
- Zařízení
- Token zařízení
- Typ zařízení
- Verze mobilní aplikace
- Verze operačního systému
- Výsledek
- Využité vyhledat oznámení

Počet aktivací (pokusí aktivovat účet mobilní aplikace Microsoft Authenticator):
- Uživatelské jméno
- Název účtu
- Časové razítko
- Získat výsledek aktivační kód
- Aktivovat úspěch
- Chyba aktivace
- Aktivace stavu výsledku
- Název zařízení
- Typ zařízení
- Verze aplikace
- Token OATH povoleno

Bloky (používá k určení blokovaných stavu a pro vytváření sestav):

- Blokovat časové razítko
- Blok podle uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Telefonní číslo ve formátu
- Linka
- Vyčištění rozšíření
- Blokováno
- Důvod blokování
- Časové razítko ukončení
- Důvod ukončení
- Uzamčení účtu
- Upozornění na podvod
- Upozornění na podvod Neblokován
- Jazyk

Přeskočení (používá se pro vytváření sestav):

- Časové razítko jednorázové přihlášení
- Počet sekund jednorázového přihlášení
- Jednorázové přihlášení podle uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Telefonní číslo ve formátu
- Linka
- Vyčištění rozšíření
- Důvod k jednorázovému přihlášení
- Časové razítko ukončení
- Důvod ukončení
- Použít jednorázové přihlášení

Změny (používají k synchronizaci změny uživatelů do MFA serveru nebo AAD):

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

## <a name="gather-data-from-mfa-server"></a>Shromažďování dat z MFA serveru

Následující proces pro MFA Server verze 8.0 nebo novější umožňuje správcům se exportovat všechna data pro uživatele:

- Přihlaste se k serveru MFA, přejděte **uživatelé** kartu, vyberte uživatele a klikněte na **upravit** tlačítko. Pořizovat snímky obrazovky (Alt + PrtScn) jednotlivé karty můžete poskytnout uživateli jejich aktuální nastavení vícefaktorového ověřování.
- Z příkazového řádku MFA serveru, spusťte následující příkaz změna cestu podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` k vytvoření JSON ve formátu souboru.
- Správci mohou také pomocí operace GetUserGdpr sady SDK webové služby jako možnost exportovat všechny MFA cloudové služby informace shromážděné pro daného uživatele nebo začlenit do větší řešení generování sestav.
- Hledání `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` a všechny zálohy pro "<username>" (včetně uvozovek v hledání) najít všechny výskyty záznam uživatele je přidáno nebo změněno.
   - Tyto záznamy můžete omezen (ale nebylo odstraněno) zrušením **"Protokolovat změny uživatelů"** v MFA Server uživatelského rozhraní, protokolování části, karta soubory protokolu.
   - Pokud je nakonfigurovaná syslog, a **"Protokolovat změny uživatelů"** se změnami MFA Server uživatelského rozhraní, protokolování části, pomocí karty Syslog, pak položky protokolu se dají shromáždit z protokolu syslog místo.
- Další výskyty uživatelské jméno v souborech MultiFactorAuthSvc.log a druhý Server MFA protokolů vztahující se k ověřování, které pokusy jsou považovány za provozu a hromadně šířenými informacím pomocí exportu MultiFactorAuthGdpr.exe nebo sadu SDK webové služby GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Odstranění dat z MFA serveru

Z příkazového řádku MFA serveru, spusťte následující příkaz změna cestu podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` můžete odstranit všechny MFA cloudové služby informace shromážděné pro tohoto uživatele.

- Odstranění dat zahrnutých v exportu v reálném čase, ale může trvat až 30 dnů pro provozní databázi nebo hromadně šířenými data chcete úplně odebrat.
- Správci mohou také pomocí operace DeleteUserGdpr sady SDK webové služby jako možnost odstranit všechny MFA cloudové služby informace shromážděné pro daného uživatele nebo začlenit do větší řešení generování sestav.

## <a name="gather-data-from-nps-extension"></a>Shromažďování dat z rozšíření NPS

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvoříte žádost o Export.

- Informace o MFA je součástí export, což může trvat hodiny nebo i dny.
- Výskyty uživatelské jméno v AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh a protokoly událostí AzureMfa/AuthZ/AuthZOptCh jsou považovány za provozu a hromadně šířenými na informace uvedené v exportu.

## <a name="delete-data-from-nps-extension"></a>Odstranění dat z rozšíření NPS

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost pro zavřít účet odstraníte všechny MFA cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů pro data úplně odebrala.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Shromažďování dat ze systému Windows Server 2016 Azure MFA adaptér služby AD FS

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvoříte žádost o Export. 

- Informace o MFA je součástí export, což může trvat hodiny nebo i dny.
- Výskyty uživatelské jméno v protokolech událostí AD FS trasování/Debug (je-li povoleno) se považují za provozu a hromadně šířenými na informace uvedené v exportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Odstranění dat z Windows serveru 2016 Azure MFA adaptér služby AD FS

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost pro zavřít účet odstraníte všechny MFA cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů pro data úplně odebrala.

## <a name="gather-data-for-azure-mfa"></a>Shromažďování dat pro Azure MFA

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvoříte žádost o Export.

- Informace o MFA je součástí export, což může trvat hodiny nebo i dny.

## <a name="delete-data-for-azure-mfa"></a>Odstranit Data pro Azure MFA

Použití [portál společnosti Microsoft o ochraně osobních údajů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost pro zavřít účet odstraníte všechny MFA cloudové služby informace shromážděné pro tohoto uživatele.

- Může trvat až 30 dnů pro data úplně odebrala.

## <a name="next-steps"></a>Další postup

[MFA Server sestav](howto-mfa-reporting.md)
