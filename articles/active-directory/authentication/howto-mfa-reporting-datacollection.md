---
title: Shromažďování uživatelských dat Azure MFA – Azure Active Directory
description: Jaké informace slouží k usnadnění ověřování uživatelů pomocí Azure Multi-Factor Authentication?
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80653637"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Shromažďování uživatelských dat v Azure Multi-Factor Authentication

Tento dokument vysvětluje, jak najít informace o uživateli shromažďované službou Azure Multi-Factor Authentication Server (MFA Server) a Azure MFA (v cloudu) v případě, že ji chcete odebrat.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Shromážděné informace

Server MFA, rozšíření serveru NPS a Windows Server 2016 Azure MFA AD FS adaptér shromažďují a ukládají následující informace po dobu 90 dnů.

Pokusy o ověření (používané pro vytváření sestav a řešení potíží):

- Timestamp
- Uživatelské jméno
- Jméno
- Příjmení
- E-mailová adresa
- Skupina uživatelů
- Metoda ověřování (telefonní hovor, textová zpráva, mobilní aplikace, token OATH)
- Režim telefonního hovoru (standardní, PIN)
- Směr textové zprávy (jednosměrný, obousměrný)
- Režim textové zprávy (jednorázové heslo, jednorázové heslo + kód PIN)
- Režim mobilní aplikace (Standard, PIN)
- Režim tokenu OATH (Standard, PIN)
- Typ ověřování
- Název aplikace
- Kód primární země volání
- Telefonní číslo primárního volání
- Primární rozšíření volání
- Primární volání se ověřilo.
- Výsledek primárního volání
- Kód země pro volání zálohy
- Telefonní číslo záložního hovoru
- Rozšíření volání zálohování
- Volání zálohy ověřeno
- Výsledek volání zálohy
- Celkový ověřený
- Celkový výsledek
- Výsledky
- Ověřuje
- Výsledek
- Zahajuje se IP adresa.
- Zařízení
- Token zařízení
- Typ zařízení
- Verze mobilní aplikace
- Verze operačního systému
- Výsledek
- Použití kontroly oznámení

Aktivace (pokusí se aktivovat účet v mobilní aplikaci Microsoft Authenticator):
- Uživatelské jméno
- Account Name
- Timestamp
- Získat výsledek aktivačního kódu
- Aktivace proběhla úspěšně
- Chyba aktivace
- Výsledek stavu aktivace
- Název zařízení
- Typ zařízení
- Verze aplikace
- Token OATH povolen

Bloky (slouží k určení blokovaného stavu a pro vytváření sestav):

- Zablokovat časové razítko
- Blokovat podle uživatelského jména
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Formát telefonního čísla
- Linka
- Vyčistit rozšíření
- Blokované
- Důvod blokování
- Časové razítko dokončení
- Důvod dokončení
- Uzamčení účtu
- Výstraha týkající se podvodů
- Výstraha týkající se podvodů není blokovaná.
- Jazyk

Vynechané funkce (používané pro vytváření sestav):

- Obejít časové razítko
- Vynechat sekundy
- Nepoužívat uživatelské jméno
- Uživatelské jméno
- Kód země
- Telefonní číslo
- Formát telefonního čísla
- Linka
- Vyčistit rozšíření
- Důvod pro obejití
- Časové razítko dokončení
- Důvod dokončení
- Použito použití

Změny (slouží k synchronizaci změn uživatelů s MFA serverem nebo Azure AD):

- Změnit časové razítko
- Uživatelské jméno
- Nové směrové číslo země
- Nové telefonní číslo
- Nové rozšíření
- Nový záložní kód země
- Nové záložní telefonní číslo
- Nové rozšíření zálohování
- Nový PIN kód
- Vyžaduje se změna kódu PIN
- Starý token zařízení
- Nový token zařízení

## <a name="gather-data-from-mfa-server"></a>Shromažďování dat z MFA serveru

Pro MFA Server verze 8,0 nebo vyšší může následující postup správcům exportovat všechna data pro uživatele:

- Přihlaste se k serveru MFA, přejděte na kartu **Uživatelé** , vyberte daného uživatele a klikněte na tlačítko **Upravit** . Pořídit snímky obrazovky (Alt-Print Screen) na jednotlivých kartách a poskytnout tak uživateli aktuální nastavení MFA.
- Z příkazového řádku serveru MFA spusťte následující příkaz, který mění cestu podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` na vytvoření souboru ve formátu JSON.
- Správci můžou použít i operaci GetUserGdpr sady SDK webové služby jako možnosti Exportovat všechny informace cloudové služby MFA shromážděné pro daného uživatele nebo začlenit do rozsáhlejšího řešení pro vytváření sestav.
- Vyhledejte `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` a vyhledejte všechny zálohy " \<username> " (včetně uvozovek ve vyhledávání), abyste našli všechny výskyty přidávaného nebo měněného záznamu uživatele.
   - Tyto záznamy můžou být omezené (ale neodstraňují se) tak, že zrušíte kontrolu **"protokolovat změny uživatelů" v uživatelském** rozhraní MFA serveru, v části protokolování, na kartě soubory protokolů.
   - Pokud je protokol syslog nakonfigurovaný a v části **uživatelské** rozhraní MFA serveru, část protokolování, karta syslog, je možné shromažďovat položky protokolu z protokolu syslog.
- Další výskyty uživatelského jména v souboru MultiFactorAuthSvc. log a dalších souborech protokolu serveru MFA, které souvisí s pokusy o ověření, se považují za provoz a duplikují se na informace poskytované pomocí MultiFactorAuthGdpr.exe exportu nebo sady SDK pro webové služby GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Odstranit data z MFA serveru

Z příkazového řádku serveru MFA spusťte následující příkaz, který mění cestu podle vaší instalace `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` a odstraní všechny informace o cloudové službě MFA shromážděné pro tohoto uživatele.

- Data zahrnutá do exportu se odstraní v reálném čase, ale může trvat až 30 dní, než se provozní nebo duplikovaná data úplně odeberou.
- Správci můžou použít i operaci DeleteUserGdpr sady SDK webové služby jako možnost odstranit všechny informace o cloudové službě MFA shromážděné pro daného uživatele nebo začlenit do rozsáhlejšího řešení pro vytváření sestav.

## <a name="gather-data-from-nps-extension"></a>Shromáždění dat z rozšíření serveru NPS

K vytvoření žádosti o export použijte [portál ochrany osobních údajů společnosti Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Informace o MFA jsou zahrnuté do exportu, což může trvat hodiny nebo dny, než se dokončí.
- Výskyty v protokolech událostí AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh a AzureMfa/AuthZ/AuthZOptCh se považují za provozní a duplikují se na informace uvedené v exportu.

## <a name="delete-data-from-nps-extension"></a>Odstranit data z rozšíření serveru NPS

Pomocí [portálu ochrany osobních údajů Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost o uzavření účtu a odstraňte všechny informace o cloudové službě MFA shromažďované pro tohoto uživatele.

- Úplné odebrání dat může trvat až 30 dnů.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Shromáždění dat z Windows serveru 2016 Azure MFA AD FS Adapter

K vytvoření žádosti o export použijte [portál ochrany osobních údajů společnosti Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) . 

- Informace o MFA jsou zahrnuté do exportu, což může trvat hodiny nebo dny, než se dokončí.
- Výskyty uživatelského jména v protokolech událostí trasování nebo ladění AD FS (Pokud je povoleno) se považují za provozní a duplikují se na informace uvedené v exportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Odstranění dat z Windows serveru 2016 Azure MFA AD FS Adapter

Pomocí [portálu ochrany osobních údajů Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost o uzavření účtu a odstraňte všechny informace o cloudové službě MFA shromažďované pro tohoto uživatele.

- Úplné odebrání dat může trvat až 30 dnů.

## <a name="gather-data-for-azure-mfa"></a>Shromažďování dat pro Azure MFA

K vytvoření žádosti o export použijte [portál ochrany osobních údajů společnosti Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Informace o MFA jsou zahrnuté do exportu, což může trvat hodiny nebo dny, než se dokončí.

## <a name="delete-data-for-azure-mfa"></a>Odstranit data pro Azure MFA

Pomocí [portálu ochrany osobních údajů Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) vytvořte žádost o uzavření účtu a odstraňte všechny informace o cloudové službě MFA shromažďované pro tohoto uživatele.

- Úplné odebrání dat může trvat až 30 dnů.

## <a name="next-steps"></a>Další kroky

[Generování sestav MFA serveru](howto-mfa-reporting.md)
