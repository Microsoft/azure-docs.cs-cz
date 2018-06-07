---
title: 'Azure AD Connect: Řešení potíží s předávací ověřování | Microsoft Docs'
description: Tento článek popisuje postup řešení potíží s Azure Active Directory (Azure AD) předávací ověřování.
services: active-directory
keywords: Řešení potíží s Azure AD Connect předávací ověřování, nainstalovat službu Active Directory, požadovaných součástí pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 3296ee114d3e285e77070995156ef6a242c1fc87
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592193"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Řešení potíží s předávací ověřování Azure Active Directory

Tento článek vám pomůže najít řešení potíží s informace o běžných problémech týkající se Azure AD předávací ověřování.

>[!IMPORTANT]
>Pokud se setkávají problémů přihlášení uživatele pomocí předávacího ověřování, nemusíte zakázat funkci nebo odinstalovat agenty předávací ověřování bez nutnosti výhradně cloudový účet globálního správce, aby. Další informace o [přidání účtu globálního správce jenom pro cloud](../active-directory-users-create-azure-portal.md). Tento krok je velmi důležité a zajistí, že nezůstanete z vašeho klienta.

## <a name="general-issues"></a>Běžné problémy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Zkontrolujte stav funkce a ověřování agentů

Zajistěte, aby funkci předávací ověřování stále **povoleno** na vašeho klienta a stav agentů ověřování zobrazuje **Active**a ne **neaktivní**. Stav můžete zkontrolovat přechodem na **Azure AD Connect** okno na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Azure Centrum pro správu služby Active Directory – okno Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Centrum pro správu – okno předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Zobrazující se uživatelům přihlášení chybové zprávy

Pokud uživatele nelze se přihlásit do aplikace pomocí předávacího ověřování, se může zobrazit jedna z těchto chyb uživatelsky orientovaný na přihlašovací obrazovce Azure AD: 

|Chyba|Popis|Řešení
| --- | --- | ---
|AADSTS80001|Nelze se připojit ke službě Active Directory|Zkontrolujte, zda jsou servery agenta členy stejné doménové struktuře AD jako uživatele, jejichž hesla se musí ověřit a je bylo možné se připojit ke službě Active Directory.  
|AADSTS8002|Připojování ke službě Active Directory došlo k vypršení časového limitu|Zkontrolujte, že služby Active Directory je k dispozici a reaguje na požadavky od agentů.
|AADSTS80004|Uživatelské jméno předaný agent byl neplatný|Zajistěte, aby že se uživatel pokouší se přihlásit pomocí správné uživatelské jméno.
|AADSTS80005|Ověření došlo k neočekávané výjimku WebException|K přechodné chybě. Opakujte žádost. Pokud ji dále nedaří, obraťte se na podporu společnosti Microsoft.
|AADSTS80007|Došlo k chybě komunikuje se službou Active Directory|Zkontrolujte protokol agenta pro další informace a ověřte, že služby Active Directory funguje podle očekávání.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Chyba přihlášení důvodů v Centru pro správu Azure Active Directory (musí licencí Premium)

Pokud váš klient má licenci Azure AD Premium s ním spojená, můžete také prohlédnout [přihlašovací aktivita sestavy](../active-directory-reporting-activity-sign-ins.md) na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory Centrum pro správu – sestava přihlášení](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Přejděte na **Azure Active Directory** -> **přihlášení** na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) a klikněte na aktivitu přihlášení příslušného uživatele. Vyhledejte **kód chyby SIGN-IN** pole. Mapování hodnotu tohoto pole na příčinu selhání a řešení v následující tabulce:

|Kód chyby přihlášení|Důvod selhání přihlášení|Řešení
| --- | --- | ---
| 50144 | Vypršela platnost hesla uživatele pro Active Directory. | Resetovat heslo uživatele ve vaší místní službě Active Directory.
| 80001 | Není k dispozici žádný ověřovací agent. | Instalace a registrace agenta ověřování.
| 80002 | Vypršel časový limit žádosti o ověření hesla ověřovacího agenta. | Zkontrolujte, jestli je dosažitelný z agenta ověřování služby Active Directory.
| 80003 | Ověřovací agent přijal neplatnou odpověď. | Pokud tento problém je konzistentně reprodukovatelnou napříč více uživatelů, zkontrolujte konfiguraci služby Active Directory.
| 80004 | V žádosti o přihlášení byl použit nesprávný hlavní název uživatele (UPN). | Požádejte uživatele se přihlásit pomocí správné uživatelské jméno.
| 80005 | Ověřovací agent: Došlo k chybě. | Došlo k přechodné chybě. Zkuste to znovu později.
| 80007 | Ověřovací agent se nemohl připojit k Active Directory. | Zkontrolujte, jestli je dosažitelný z agenta ověřování služby Active Directory.
| 80010 | Ověřovací agent nebyl schopen dešifrovat heslo. | Pokud problém konzistentně reprodukovatelnou, nainstalujte a zaregistrujte novou ověření agenta. A odinstalovat stávající. 
| 80011 | Ověřovací agent nebyl schopen získat dešifrovací klíč. | Pokud problém konzistentně reprodukovatelnou, nainstalujte a zaregistrujte novou ověření agenta. A odinstalovat stávající.

## <a name="authentication-agent-installation-issues"></a>Problémy instalace agenta ověřování

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďování protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a kontaktujte Microsoft Support s tímto problémem.

## <a name="authentication-agent-registration-issues"></a>Problémy s ověřením agenta registrace

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registrace ověření agenta se nezdařila z důvodu blokované porty

Zkontrolujte, jestli server, na kterém byl nainstalován Agent pro ověřování může komunikovat v naší službě uvedené adresy URL a portů [zde](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registrace ověření agenta se nezdařila z důvodu chyb autorizační token nebo účtu

Ujistěte se, že používáte účet globálního správce jenom pro cloud pro Azure AD Connect nebo samostatná Agent ověřování instalace a registrace operace. Je známý problém s povoleným MFA globálního správce účty; Vypněte MFA dočasně (pouze pro dokončení operací) jako alternativní řešení.

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďování protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a kontaktujte Microsoft Support s tímto problémem.

## <a name="authentication-agent-uninstallation-issues"></a>Problémy s ověřením agenta odinstalace

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Upozornění při odinstalaci Azure AD Connect

Pokud máte předávací ověřování povoleno na vašeho klienta a odinstalaci Azure AD Connect, zobrazuje se následující upozornění: "uživatelé nebudou moci přihlásit ke službě Azure AD Pokud máte jiné agenty pro předávací ověřování nainstalovaný na Další servery."

Zajistěte, aby vaše instalace [vysoké dostupné](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) před odinstalováním Azure AD Connect, chcete-li se vyhnout narušení přihlášení uživatele.

## <a name="issues-with-enabling-the-feature"></a>Problémy s povolením této funkce

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Povolením této funkce se nezdařila, protože nebyly k dispozici žádní agenti ověřování

Musíte mít alespoň jeden aktivní Agent ověřování povolení předávací ověřování na klientovi. Můžete nainstalovat agenta ověřování instalaci Azure AD Connect nebo samostatný Agent ověřování.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Povolením této funkce se nezdařilo z důvodu blokované porty

Zkontrolujte, jestli server, na kterém je nainstalován Azure AD Connect může komunikovat v naší službě uvedené adresy URL a portů [zde](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Povolením této funkce se nezdařilo z důvodu chyb autorizační token nebo účtu

Ujistěte se, že používáte účet globálního správce jenom pro cloud při povolení funkce. Je známý problém s službou Multi-Factor authentication (MFA)-povolit globální správce účtů; Vypněte MFA dočasně (pouze pro dokončení operace) jako alternativní řešení.

## <a name="exchange-activesync-configuration-issues"></a>Problémy s konfigurací protokolu Exchange ActiveSync

Toto jsou běžné problémy, pokud nakonfigurujete podporu protokolu Exchange ActiveSync pro předávací ověřování.

### <a name="exchange-powershell-issue"></a>Problém Exchange PowerShell

Pokud se zobrazí "**parametr nebyl nalezen odpovídající název parametru 'PerTenantSwitchToESTSEnabled'\.**" Chyba při spuštění `Set-OrganizationConfig` Exchange PowerShell příkaz, obraťte se na Microsoft Support.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync nepracuje

Konfigurace trvá delší dobu se projeví – časové období závisí na vašem prostředí. Pokud tento stav trvá dlouhou dobu, obraťte se na Microsoft Support.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Shromažďování protokolů předávací ověřování agenta

V závislosti na typu problém, který může mít budete muset podívejte se na různých místech pro protokoly Agent předávací ověřování.

### <a name="azure-ad-connect-logs"></a>Protokoly služby Azure AD Connect

Pro chyby týkající se instalace, zkontrolujte protokoly Azure AD Connect v **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Protokoly událostí ověření agenta

Chyby související s agentem ověřování, otevřete Prohlížeč událostí aplikace na serveru a zkontrolujte v části **aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Podrobné analýzy povolte protokol "Relace". Nespouštět ověřování agenta, se tento protokol povolen během normálních operací; Používejte pouze pro řešení potíží. Obsah protokolu jsou viditelné pouze po zakázání protokolu je znovu.

### <a name="detailed-trace-logs"></a>Protokoly podrobné trasování

K řešení neúspěšných přihlášení uživatele, vyhledejte protokoly trasování v **%ProgramData%\Microsoft\Azure AD připojit Agent\Trace ověřování\\**. Tyto protokoly obsahovat důvodů proč konkrétního uživatele přihlášení se nezdařilo, používá funkci předávací ověřování. Tyto chyby jsou taky namapovaný na příčin selhání přihlášení uvedené v předchozím [tabulky](#sign-in-failure-reasons-on-the-Azure-portal). Následuje příklad položky protokolu:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Popisné informace o chybě ('1328' v předchozím příkladu) můžete získat otevřením až do příkazového řádku a spuštěním následujícího příkazu (Poznámka: '1328' nahraďte číslo chyby, které vidíte do protokolů):

`Net helpmsg 1328`

![Předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Řadič domény

Pokud je povoleno protokolování auditu, další informace naleznete v protokolech zabezpečení řadiče domény. Jednoduchý způsob, jak dotazovat přihlášení požadavky odeslané agenty předávací ověřování vypadá takto:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Čítače sledování výkonu

Jiné způsoby, jak sledovat ověřování agentů je sledovat čítače sledování výkonu konkrétní na každém serveru, na kterých je nainstalován Agent pro ověřování. Pomocí následujících globální čítačů (**# PTA ověřování**, **#PTA se nezdařilo ověření** a **úspěšných ověření #PTA**) a čítače chyb (**Chybám při ověřování # PTA**):

![Průchozí čítačů sledování výkonu ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Předávací ověřování poskytuje vysokou dostupnost, pomocí více ověřování agentů a _není_ Vyrovnávání zatížení. V závislosti na konfiguraci _není_ přijímat všechny agenty ověřování zhruba _rovna_ počet požadavků. Je možné konkrétní Agent ověřování vůbec obdrží žádný provoz.
