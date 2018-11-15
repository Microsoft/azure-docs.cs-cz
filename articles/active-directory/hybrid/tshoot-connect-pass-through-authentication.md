---
title: 'Azure AD Connect: Řešení potíží s předávací ověřování | Dokumentace Microsoftu'
description: Tento článek popisuje postup řešení potíží s Azure Active Directory (Azure AD) předávací ověřování.
services: active-directory
keywords: Řešení potíží s Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6172195a9914d841e480cd7ebbf9566616911378
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686190"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Řešení potíží s Azure Active Directory předávací ověřování

Tento článek vám pomůže najít řešení potíží s informace o běžných problémech týkajících se předávacího ověřování Azure AD.

>[!IMPORTANT]
>Pokud máte uživatele problémy s přihlašováním pomocí předávacího ověřování, není zakázat funkci nebo bez nutnosti výhradně cloudový účet globálního správce, aby odinstalace agentů předávací ověřování. Další informace o [přidání výhradně cloudový účet globálního správce](../active-directory-users-create-azure-portal.md). Provedením tohoto kroku je velmi důležité a zajišťuje, že nezůstanete zamknutí mimo vašeho tenanta.

## <a name="general-issues"></a>Obecné problémy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Zkontrolovat stav funkce a agentů ověřování

Ujistěte se, že funkce předávací ověřování je stále **povoleno** vašeho tenanta a stavu agentů ověřování ukazuje **aktivní**a ne **neaktivní**. Stav můžete zkontrolovat tak, že přejdete **Azure AD Connect** okna na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Azure Centrum pro správu služby Active Directory – okno Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory Centrum pro správu – okno předávací ověřování](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Přístupných přihlášení chybové zprávy

Pokud uživatel nemůže přihlásit do aplikace pomocí předávacího ověřování, jim může zobrazit jedna z následujících chyb přístupných na obrazovce pro přihlášení k Azure AD: 

|Chyba|Popis|Řešení
| --- | --- | ---
|AADSTS80001|Nelze se připojit ke službě Active Directory|Ujistěte se, že agent servery jsou členy stejné doménové struktuře AD jako uživatelé, jejichž hesla je nutné ověřit a budou moct připojit ke službě Active Directory.  
|AADSTS8002|Připojování ke službě Active Directory došlo k vypršení časového limitu|Zkontrolujte, že služba Active Directory je k dispozici a reaguje na požadavky od agentů.
|AADSTS80004|Uživatelské jméno předané do agenta nebyla platná|Zajistěte, aby že se uživatel pokouší se přihlásit pomocí správné uživatelské jméno.
|AADSTS80005|Ověření došlo k neočekávané o výjimku WebException|O přechodnou chybu. Zkuste požadavek. Pokud bude nadále selhávat, kontaktujte podporu Microsoftu.
|AADSTS80007|Došlo k chybě při komunikaci se službou Active Directory|V protokolech agenta pro další informace a ověřte, že služby Active Directory funguje podle očekávání.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Důvody selhání přihlášení v Centru pro správu Azure Active Directory (vyžaduje licence Premium)

Pokud váš tenant licenci Azure AD Premium s ním spojená, můžete také prohlédnout [sestavy aktivit přihlašování](../reports-monitoring/concept-sign-ins.md) na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory Centrum pro správu – přihlášení](./media/tshoot-connect-pass-through-authentication/pta4.png)

Přejděte do **Azure Active Directory** -> **přihlášení** na [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) a klikněte na tlačítko aktivit přihlašování konkrétního uživatele. Hledat **Přihlašovací chyba kódu** pole. Mapování hodnotu pole Důvod selhání a řešení v následující tabulce:

|Kód chyby přihlášení|Příčina chyby přihlášení|Řešení
| --- | --- | ---
| 50144 | Vypršela platnost hesla uživatele pro Active Directory. | Resetovat heslo uživatele ve vašem místním Active Directory.
| 80001 | Není k dispozici žádný ověřovací agent. | Instalace a registrace agenta ověřování.
| 80002 | Vypršel časový limit žádosti o ověření hesla ověřovacího agenta. | Zkontrolujte, jestli je dostupný z ověřovacího agenta služby Active Directory.
| 80003 | Ověřovací agent přijal neplatnou odpověď. | Pokud se jedná o reprodukovatelné konzistentně napříč více uživatelů, zkontrolujte konfiguraci služby Active Directory.
| 80004 | V žádosti o přihlášení byl použit nesprávný hlavní název uživatele (UPN). | Požádejte uživatele k přihlášení pomocí pověření správné uživatelské jméno.
| 80005 | Ověřovací agent: Došlo k chybě. | Došlo k přechodné chybě. Zkuste to znovu později.
| 80007 | Ověřovací agent se nemohl připojit k Active Directory. | Zkontrolujte, jestli je dostupný z ověřovacího agenta služby Active Directory.
| 80010 | Ověřovací agent nebyl schopen dešifrovat heslo. | Pokud se jedná o konzistentně reprodukovatelnou, instalace a registrace bude nový Agent ověřování. A odinstalovat stávající. 
| 80011 | Ověřovací agent nebyl schopen získat dešifrovací klíč. | Pokud se jedná o konzistentně reprodukovatelnou, instalace a registrace bude nový Agent ověřování. A odinstalovat stávající.

## <a name="authentication-agent-installation-issues"></a>Problémy instalace agentů ověřování

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďování protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a contact Microsoft Support s vaším problémem.

## <a name="authentication-agent-registration-issues"></a>Potíže s registrací agentů ověřování

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registrace ověřovacího agenta služby se nezdařila kvůli zablokování portů

Ujistěte se, že server, na kterém je nainstalovaný Agent ověřování může komunikovat s naší službou uvedených adres URL a portů [tady](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registrace ověřovacího agenta služby se nezdařila z důvodu chyby autorizace účtu nebo token

Ověřte, že používáte cloudový účet globálního správce pro všechny služby Azure AD Connect nebo samostatná instalace ověřovacího agenta služby a operace zápisu. Existuje známý problém s oprávněními globálního správce zapnuté MFA.; Vypněte MFA dočasně (pouze k dokončení operace) jako alternativní řešení.

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromažďování protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a contact Microsoft Support s vaším problémem.

## <a name="authentication-agent-uninstallation-issues"></a>Problémy s ověřováním agenta odinstalace

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Upozornění při odinstalaci služby Azure AD Connect

Pokud máte ve svém tenantovi povoleno předávací ověřování a odinstalaci služby Azure AD Connect, zobrazuje se následující upozornění: "uživatelé nebudou moct přihlásit ke službě Azure AD bez dalších předávací ověřování agenti nainstalovaní na ostatní servery."

Ujistěte se, že vaše instalace [s vysokou dostupností](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) před odinstalováním vyhnuli narušení funkčnosti přihlášení uživatele Azure AD Connect.

## <a name="issues-with-enabling-the-feature"></a>Potíže s povolením této funkce

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Povolením této funkce se nezdařila, protože nebyly k dispozici žádné agentů ověřování

Musíte mít aspoň jednoho aktivního agenta ověřování povolit předávací ověřování ve svém tenantovi. Můžete nainstalovat ověřovacího agenta instalaci Azure AD Connect nebo samostatný ověřovacího agenta.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Povolením této funkce se nezdařilo kvůli zablokování portů

Ujistěte se, že server, na kterém je nainstalovaný Azure AD Connect může komunikovat s naší službou uvedených adres URL a portů [tady](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Povolením této funkce se nezdařilo z důvodu chyby autorizace účtu nebo token

Ujistěte se, že používáte výhradně cloudový účet globálního správce při povolení funkce. Existuje známý problém s ověřováním Multi-Factor Authentication (MFA)-povolené účty globálních správců; Vypněte MFA dočasně (pouze pro dokončení operace) jako alternativní řešení.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Shromažďování protokolů předávací ověřování agenta

V závislosti na typu problému, který může mít budete muset podívejte se na různých místech pro protokoly předávací ověřování agenta.

### <a name="azure-ad-connect-logs"></a>Protokoly služby Azure AD Connect

Pro chyby související s instalací, zkontrolujte protokoly služby Azure AD Connect v **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Protokoly událostí ověřování agenta

Chyby související s ověřovacího agenta, otevřete Prohlížeč událostí aplikace na serveru a zkontrolujte v části **aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Pro podrobné analýzy povolte protokol "Relace" (klikněte pravým tlačítkem uvnitř prohlížeče událostí aplikace mohly najít tuto možnost). Při spuštění ověřovacího agenta pomocí tohoto protokolu povolit při běžném provozu; Používejte jenom pro řešení potíží. Obsah protokolu jsou viditelné pouze po zakázání protokolu je znovu.

### <a name="detailed-trace-logs"></a>Podrobné trasování protokolů

Chcete-li vyřešit neúspěšných přihlášení uživatele, vyhledejte protokoly trasování v **%ProgramData%\Microsoft\Azure AD Connect Agent\Trace ověřování\\**. Tyto protokoly obsahovat důvodů proč konkrétního uživatele přihlášení se nezdařilo, pomocí funkce předávací ověřování. Tyto chyby jsou také namapován na důvody selhání přihlášení, je znázorněno v předchozí tabulce důvodů selhání přihlášení. Tady je příklad položky protokolu:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Popisný podrobnosti o chybě ("1328" v předchozím příkladu) můžete získat tak, že se vám otevírají příkazového řádku a spuštěním následujícího příkazu (Poznámka: "1328" nahraďte aktuální číslo chyby, který se zobrazí v protokolech):

`Net helpmsg 1328`

![Předávací ověřování](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Řadič domény

Pokud je povoleno protokolování auditu, další informace najdete v protokolech zabezpečení řadičů domény. Jednoduchý způsob, jak dotazovat přihlášení požadavky od agentů předávací ověřování je následujícím způsobem:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Čítače výkonu

Dalším způsobem, jak monitorovat agentů ověřování je sledovat čítače výkonu specifické na každém serveru, kde je nainstalovaný Agent ověřování. Pomocí následujících globální čítačů (**ověřování # PTA**, **#PTA se nezdařilo ověření** a **úspěšné ověření zapsáno #PTA**) a čítače chyb (**Chyby ověřování # PTA**):

![Předávací ověřování výkonu monitorování čítače](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Předávací ověřování poskytuje vysokou dostupnost pomocí více agentů ověřování a _není_ Vyrovnávání zatížení. V závislosti na vaší konfiguraci _není_ všech agentů ověřování přijímat zhruba _rovná_ počet požadavků. Je možné, že konkrétní ověřovací Agent obdrží vůbec žádný provoz.
