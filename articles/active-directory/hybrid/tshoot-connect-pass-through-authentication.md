---
title: 'Azure AD Connect: Poradce při potížích s předávacím ověřováním | Dokumenty společnosti Microsoft'
description: Tento článek popisuje, jak řešit řešení potíží s předávacím ověřováním služby Azure Active Directory (Azure AD).
services: active-directory
keywords: Poradce při potížích s předávacím ověřováním služby Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, Jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456125"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Řešení potíží s předávacím ověřováním služby Azure Active Directory

Tento článek vám pomůže najít informace o řešení potíží o běžných problémech týkajících se předávacího ověřování Azure AD.

>[!IMPORTANT]
>Pokud se potýkáte s problémy s přihlášením uživatele s předávacím ověřováním, nezakažte tuto funkci ani neodinstalujte předávací agenty ověřování, aniž byste měli účet globálního správce pouze pro cloud, na který byste se měli vrátit. Přečtěte si [o přidání účtu globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Provedení tohoto kroku je důležité a zajišťuje, že se nedostanete uzamčenz vašeho tenanta.

## <a name="general-issues"></a>Běžné problémy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Kontrola stavu funkce a agentů ověřování

Ujistěte se, že předávací ověřování funkce je stále **povolena** na vašem tenantovi a stav agenty ověřování zobrazuje **aktivní**a není **neaktivní**. Stav můžete zkontrolovat tak, že přejdete do okna **Azure AD Connect** v [Centru pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory – okno Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centrum pro správu služby Azure Active Directory – okno předávacího ověřování](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Chybové zprávy přihlášení směřující k uživateli

Pokud se uživatel nemůže přihlásit k používání předávacího ověřování, může se mu na přihlašovací obrazovce Azure AD zobrazit jedna z následujících chyb uživatelských položek: 

|Chyba|Popis|Řešení
| --- | --- | ---
|AADSTS80001|Nelze se připojit ke službě Active Directory.|Ujistěte se, že servery agentů jsou členy stejné doménové struktury služby AD jako uživatelé, jejichž hesla je třeba ověřit, a mohou se připojit ke službě Active Directory.  
|AADSTS8002|Při připojování ke službě Active Directory došlo k časovému uzlová času.|Zkontrolujte, zda je služba Active Directory k dispozici a odpovídá na požadavky agentů.
|AADSTS80004|Uživatelské jméno předané agentovi nebylo platné.|Ujistěte se, že se uživatel pokouší přihlásit pomocí správného uživatelského jména.
|AADSTS80005|Ověření bylo zjištěno nepředvídatelné webové výjimky|Přechodná chyba. Opakujte požadavek. Pokud se nezdaří i nadále, obraťte se na podporu společnosti Microsoft.
|AADSTS80007|Při komunikaci se službou Active Directory došlo k chybě.|Další informace získáte v protokolech agenta a ověřte, zda služba Active Directory funguje podle očekávání.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Důvody selhání přihlášení v Centru pro správu Služby Azure Active Directory (vyžaduje licenci Premium)

Pokud má váš tenant přidruženou licenci Azure AD Premium, můžete se taky podívat na [sestavu přihlašovacích aktivit](../reports-monitoring/concept-sign-ins.md) v [Centru pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Služby Azure Active Directory – sestava přihlášení](./media/tshoot-connect-pass-through-authentication/pta4.png)

Přejděte na přihlašovací údaje **služby Azure Active Directory** -> v [Centru pro správu Služby Azure Active Directory](https://aad.portal.azure.com/) a klikněte na aktivitu přihlášení konkrétního uživatele.**Sign-ins** Vyhledejte pole **KÓD CHYBY PŘIHLÁŠENÍ.** Namapujte hodnotu tohoto pole na důvod selhání a rozlišení pomocí následující tabulky:

|Kód chyby přihlášení|Důvod selhání přihlášení|Řešení
| --- | --- | ---
| 50144 | Vypršela platnost hesla uživatele pro Active Directory. | Resetujte heslo uživatele v místní službě Active Directory.
| 80001 | Není k dispozici žádný ověřovací agent. | Nainstalujte a zaregistrujte agenta ověřování.
| 80002 | Vypršel časový limit žádosti o ověření hesla ověřovacího agenta. | Zkontrolujte, zda je služba Active Directory dostupná od agenta ověřování.
| 80003 | Ověřovací agent přijal neplatnou odpověď. | Pokud je problém konzistentně reprodukovatelný pro více uživatelů, zkontrolujte konfiguraci služby Active Directory.
| 80004 | V žádosti o přihlášení byl použit nesprávný hlavní název uživatele (UPN). | Požádejte uživatele, aby se přihlásil pomocí správného uživatelského jména.
| 80005 | Ověřovací agent: Došlo k chybě. | Přechodná chyba. Zkuste to později.
| 80007 | Ověřovací agent se nemohl připojit k Active Directory. | Zkontrolujte, zda je služba Active Directory dostupná od agenta ověřování.
| 80010 | Ověřovací agent nebyl schopen dešifrovat heslo. | Pokud je problém konzistentně reprodukovatelný, nainstalujte a zaregistrujte nového agenta ověřování. A odinstalovat aktuální. 
| 80011 | Ověřovací agent nebyl schopen získat dešifrovací klíč. | Pokud je problém konzistentně reprodukovatelný, nainstalujte a zaregistrujte nového agenta ověřování. A odinstalovat aktuální.

>[!IMPORTANT]
>Předávací agenti ověřování ověřují uživatele služby Azure AD ověřením jejich uživatelských jmen a hesel ve službě Active Directory voláním [rozhraní API Přihlašujícího uživatele Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx). V důsledku toho pokud jste ve službě Active Directory nastavili nastavení "Přihlášení do", abyste omezili přístup k přihlášení pracovní stanice, budete muset přidat servery hostující agenty ověřování průchodu také do seznamu serverů "Přihlášení k". Pokud tak neučiníte, zablokujete uživatelům přihlášení do služby Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problémy s instalací agenta ověřování

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďujte protokoly agentů](#collecting-pass-through-authentication-agent-logs) ze serveru a obraťte se na podporu společnosti Microsoft s vaším problémem.

## <a name="authentication-agent-registration-issues"></a>Problémy s registrací agenta ověřování

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registrace agenta ověřování se nezdařila z důvodu blokovaných portů

Ujistěte se, že server, na kterém byl nainstalován agent ověřování, může komunikovat s adresami URL a porty našich služeb, které [jsou zde](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)uvedeny .

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registrace agenta ověřování se nezdařila z důvodu chyb autorizace tokenu nebo účtu.

Ujistěte se, že používáte účet globálního správce pouze pro cloud pro všechny operace instalace a registrace Azure AD Connect nebo samostatného agenta ověřování. Existuje známý problém s účty globálního správce s povolenou pomocí mfa. dočasně vypněte vícefaktorové plánování (pouze k dokončení operací) jako řešení.

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďujte protokoly agentů](#collecting-pass-through-authentication-agent-logs) ze serveru a obraťte se na podporu společnosti Microsoft s vaším problémem.

## <a name="authentication-agent-uninstallation-issues"></a>Problémy s odinstalací agenta ověřování

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Varovná zpráva při odinstalaci služby Azure AD Connect

Pokud máte v tenantovi povolené předávací ověřování a pokusíte se odinstalovat Azure AD Connect, zobrazí se následující varovná zpráva: "Uživatelé se nebudou moct přihlásit k Azure AD, pokud nemáte nainstalované jiné agenty předávacího ověřování jiných serverů."

Ujistěte se, že vaše nastavení je [vysoce dostupné](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) před odinstalací Azure AD Connect, aby se zabránilo přerušení přihlášení uživatele.

## <a name="issues-with-enabling-the-feature"></a>Problémy s povolením funkce

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Povolení funkce se nezdařilo, protože nebyli k dispozici žádní agenti ověřování.

Chcete-li povolit předávací ověřování ve vašem tenantovi, musíte mít alespoň jednoho aktivního agenta ověřování. Agenta pro ověřování můžete nainstalovat buď instalací služby Azure AD Connect, nebo samostatného agenta ověřování.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Povolení funkce se nezdařilo z důvodu blokovaných portů

Ujistěte se, že server, na kterém je nainstalována služba Azure AD Connect, může komunikovat s adresami URL a porty našich služeb, [které jsou zde](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)uvedeny .

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Povolení funkce se nezdařilo z důvodu chyb autorizace tokenu nebo účtu

Ujistěte se, že při povolení funkce používáte účet globálního správce pouze pro cloud. Existuje známý problém s účty globálního správce s vícefaktorovým ověřováním (MFA). dočasně vypněte vícefaktorové řízení (pouze k dokončení operace) jako řešení.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Shromažďování předávacích protokolů agenta ověřování

V závislosti na typu problému, který můžete mít, je třeba hledat na různých místech pro předávací protokoly agenta ověřování.

### <a name="azure-ad-connect-logs"></a>Protokoly Azure AD Connect

Chyby související s instalací naleznete v protokolech služby Azure AD Connect na adrese **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Protokoly událostí agenta ověřování

V případě chyb souvisejících s agentem ověřování otevřete na serveru aplikaci Prohlížeč událostí a zkontrolujte v části **Protokoly aplikací a služeb\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Chcete-li získat podrobnou analýzu, povolte protokol "Session" (kliknutím pravým tlačítkem myši v aplikaci Prohlížeč událostí tuto možnost vyhledejte). Nespouštějte agenta ověřování s tímto protokolem povoleným během normálních operací. používat pouze pro řešení potíží. Obsah protokolu jsou viditelné pouze po protokolu je zakázánznovu.

### <a name="detailed-trace-logs"></a>Podrobné protokoly trasování

Chcete-li odstranit selhání přihlášení uživatele, vyhledejte protokoly trasování na adrese **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Tyto protokoly obsahují důvody, proč se připřihlášení konkrétního uživatele nezdařilo pomocí funkce předávacího ověřování. Tyto chyby jsou také mapovány na důvody selhání přihlášení uvedené v předchozí tabulce důvodů selhání přihlášení. Následuje ukázková položka protokolu:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Popisné podrobnosti o chybě (1328 v předchozím příkladu) můžete získat otevřením příkazového řádku a spuštěním následujícího příkazu (Poznámka: Nahraďte příkaz 1328 skutečným číslem chyby, které vidíte v protokolech):

`Net helpmsg 1328`

![Předávací ověřování](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Protokoly řadiče domény

Pokud je protokolování auditu povoleno, další informace naleznete v protokolech zabezpečení řadičů domény. Jednoduchý způsob, jak dotaz ovat žádosti o přihlášení odeslané předávacími agenty ověřování, je následující:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Čítače sledování výkonu

Dalším způsobem, jak sledovat agenty ověřování, je sledovat konkrétní čítače sledování výkonu na každém serveru, na kterém je nainstalován agent ověřování. Použijte následující globální čítače (**# PTA ověřování**, **#PTA neúspěšná ověřování** a #PTA úspěšné **ověřování**) a čítače chyb (**# chyby ověřování PTA**):

![Čítače sledování výkonu předávacího ověřování](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Předávací ověřování poskytuje vysokou dostupnost pomocí více agentů ověřování a _nikoli_ vyrovnávání zatížení. V závislosti na konfiguraci _ne_ všichni agenti ověřování obdrží zhruba _stejný_ počet požadavků. Je možné, že konkrétní agent ověřování neobdrží žádný provoz vůbec.
