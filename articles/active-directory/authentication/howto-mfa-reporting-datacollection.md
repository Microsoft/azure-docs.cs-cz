---
title: Shromažďování uživatelských dat Azure MFA – Azure Active Directory
description: Jaké informace se používají k ověření uživatelů pomocí azure multi-factor authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3b5af972ad6dd15b7c992d5e264ede97bd1dde
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653637"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Shromažďování uživatelských dat azure s vícefaktorovým ověřováním

Tento dokument vysvětluje, jak najít informace o uživateli shromážděné serverem Azure Multi-Factor Authentication Server (MFA Server) a Azure MFA (cloudová) v případě, že je chcete odebrat.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Shromažďované informace

Server MFA, rozšíření NPS a adaptér Služby Azure MFA AD FS pro Windows Server 2016 shromažďují a ukládají následující informace po dobu 90 dnů.

Pokusy o ověření (používá se pro vytváření sestav a odstraňování potíží):

- Časové razítko
- Uživatelské jméno
- Jméno
- Příjmení
- E-mailová adresa
- Skupina uživatelů
- Metoda ověřování (telefonní hovor, textová zpráva, mobilní aplikace, token OATH)
- Režim telefonního hovoru (standardní, PIN)
- Směr textové zprávy (jednosměrný, obousměrný)
- Režim textových zpráv (OTP, OTP + PIN)
- Režim mobilní aplikace (standardní, PIN)
- Režim tokenu OATH (standardní, PIN)
- Typ ověřování
- Název aplikace
- Kód země primárního volání
- Primární telefonní číslo hovoru
- Primární rozšíření volání
- Primární volání ověřeno
- Výsledek primárního volání
- Kód země záložního volání
- Telefonní číslo záložního hovoru
- Rozšíření záložního volání
- Ověřený záložní hovor
- Výsledek volání zálohy
- Celkově ověřeno
- Celkový výsledek
- Výsledky
- Ověřené
- Výsledek
- Zahájení adresy IP
- Zařízení
- Token zařízení
- Typ zařízení
- Verze mobilní aplikace
- Verze operačního systému
- Výsledek
- Použitá kontrola oznámení

Aktivace (pokusy o aktivaci účtu v mobilní aplikaci Microsoft Authenticator):
- Uživatelské jméno
- Název účtu
- Časové razítko
- Výsledek získání aktivačního kódu
- Aktivovat úspěch
- Aktivovat chybu
- Výsledek stavu aktivace
- Název zařízení
- Typ zařízení
- Verze aplikace
- Token OATH povolen

Bloky (slouží k určení blokovaného stavu a pro vykazování):

- Časové razítko bloku
- Blokovat podle uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Formátované telefonní číslo
- Linka
- Vyčistit rozšíření
- Blokované
- Důvod zablokování
- Časové razítko dokončení
- Důvod dokončení
- Uzamčení účtu
- Upozornění na podvod
- Upozornění na podvod není blokováno
- Jazyk

Obchvaty (používá se pro vykazování):

- Obejít časové razítko
- Obejít sekundy
- Obejít podle uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Formátované telefonní číslo
- Linka
- Vyčistit rozšíření
- Obejít důvod
- Časové razítko dokončení
- Důvod dokončení
- Obejít použité

Změny (slouží k synchronizaci uživatelských změn na serveru MFA nebo Azure AD):

- Změnit časové razítko
- Uživatelské jméno
- Nový kód země
- Nové telefonní číslo
- Nové rozšíření
- Nový kód záložní země
- Nové záložní telefonní číslo
- Nové rozšíření zálohy
- Nový PIN
- Je nutná změna kódu PIN.
- Starý token zařízení
- Token nového zařízení

## <a name="gather-data-from-mfa-server"></a>Shromažďování dat ze serveru MFA

Pro server MFA verze 8.0 nebo vyšší umožňuje následující proces správcům exportovat všechna data pro uživatele:

- Přihlaste se k serveru MFA, přejděte na kartu **Uživatelé,** vyberte daného uživatele a klikněte na tlačítko **Upravit.** Pořiďte snímky obrazovky (Alt-PrtScn) z každé karty a poskytněte uživateli aktuální nastavení vícefaktorové ověřování.
- Z příkazového řádku serveru MFA spusťte následující příkaz, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` který změní cestu podle instalace a vytvoří soubor ve formátu JSON.
- Správci mohou také použít operaci Web Service SDK GetUserGdpr jako možnost exportu všech informací cloudové služby MFA shromážděných pro daného uživatele nebo začlenit do většího řešení pro vytváření sestav.
- Hledání `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` a všechny zálohy pro "uživatelské\<jméno>" (zahrnout nabídky do hledání) najít všechny instance záznamu uživatele, které jsou přidány nebo změněny.
   - Tyto záznamy mohou být omezeny (ale neodstraněny) zrušením zaškrtnutí **políčka "Log user changes"** v části MFA Server UX, Protokolování, na kartě Soubory protokolu.
   - Pokud je nakonfigurován syslog a **"Protokolovat změny uživatele"** je zaškrtnuto v MFA Server UX, Protokolování části, Syslog kartu, pak položky protokolu lze shromáždit z syslog místo.
- Další výskyty uživatelského jména v MultiFactorAuthSvc.log a dalších souborech protokolu serveru MFA, které se týkající se pokusů o ověření, jsou považovány za funkční a duplicitní k informacím poskytnutým pomocí exportu MultiFactorAuthGdpr.exe nebo Web Service SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Odstranění dat ze serveru MFA

Z příkazového řádku serveru MFA spusťte následující příkaz, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` který změní cestu podle instalace a odstraní všechny informace cloudové služby MFA shromážděné pro tohoto uživatele.

- Údaje obsažené v exportu se v reálném čase vymažou, ale může trvat až 30 dní, než budou provozní nebo duplicitní údaje zcela odstraněny.
- Správci mohou také použít operaci Web Service SDK DeleteUserGdpr jako možnost odstranit všechny informace cloudové služby MFA shromážděné pro daného uživatele nebo začlenit do většího řešení pro vytváření sestav.

## <a name="gather-data-from-nps-extension"></a>Shromažďování dat z rozšíření NPS

K vytvoření žádosti o export použijte [portál Microsoft Privacy Portal.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)

- Informace vícefaktorové finanční finanční informace jsou zahrnuty do exportu, což může trvat hodiny nebo dny.
- Výskyty uživatelského jména v protokolech událostí AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh a AzureMfa/AuthZ/AuthZOptCh jsou považovány za provozní a duplicitní k informacím poskytnutým v exportu.

## <a name="delete-data-from-nps-extension"></a>Odstranit data z rozšíření NPS

Pomocí [portálu Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) můžete požádat o uzavření účtu k odstranění všech informací cloudové služby MFA shromážděných pro tohoto uživatele.

- Úplné odstranění dat může trvat až 30 dní.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Shromažďování dat z adaptéru Azure MFA AD FS pro Windows Server 2016

K vytvoření žádosti o export použijte [portál Microsoft Privacy Portal.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) 

- Informace vícefaktorové finanční finanční informace jsou zahrnuty do exportu, což může trvat hodiny nebo dny.
- Výskyty uživatelského jména v protokolech událostí trasování a ladění služby AD FS (pokud jsou povoleny) jsou považovány za provozní a duplicitní k informacím poskytnutým v exportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Odstranění dat z adaptéru Azure MFA AD FS pro Windows Server 2016

Pomocí [portálu Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) můžete požádat o uzavření účtu k odstranění všech informací cloudové služby MFA shromážděných pro tohoto uživatele.

- Úplné odstranění dat může trvat až 30 dní.

## <a name="gather-data-for-azure-mfa"></a>Shromažďování dat pro Azure MFA

K vytvoření žádosti o export použijte [portál Microsoft Privacy Portal.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)

- Informace vícefaktorové finanční finanční informace jsou zahrnuty do exportu, což může trvat hodiny nebo dny.

## <a name="delete-data-for-azure-mfa"></a>Odstranění dat pro Azure MFA

Pomocí [portálu Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) můžete požádat o uzavření účtu k odstranění všech informací cloudové služby MFA shromážděných pro tohoto uživatele.

- Úplné odstranění dat může trvat až 30 dní.

## <a name="next-steps"></a>Další kroky

[Vykazování serveru MFA](howto-mfa-reporting.md)
