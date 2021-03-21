---
title: 'Azure AD Connect: řešení potíží s předávacím ověřováním | Microsoft Docs'
description: Tento článek popisuje, jak řešit potíže s předávacím ověřováním Azure Active Directory (Azure AD).
services: active-directory
keywords: Řešení potíží se Azure AD Connect předávacího ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762838"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Řešení potíží s předávacím ověřováním služby Azure Active Directory

Tento článek vám pomůže najít informace o řešení běžných problémů týkajících se předávacího ověřování Azure AD.

>[!IMPORTANT]
>Pokud máte k problémům přihlašování uživatelů při předávacím ověřování, zakažte funkci nebo odinstalujte agenty předávacího ověřování, aniž byste měli účet globálního správce jenom pro Cloud, abyste se mohli vrátit. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../fundamentals/add-users-azure-active-directory.md). Provedení tohoto kroku je kritické a zajistí, že se nezamknete ze svého tenanta.

## <a name="general-issues"></a>Běžné problémy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Zkontroluje stav agentů a funkcí ověřování.

Ujistěte se, že je ve vašem tenantovi stále **povolená** funkce předávacího ověřování a že stav agentů ověřování zobrazuje **aktivní** a není **neaktivní**. Stav můžete ověřit tak, že v [centru pro správu Azure Active Directory](https://aad.portal.azure.com/)kliknete na okno **Azure AD Connect** .

![Centrum pro správu Azure Active Directory – okno Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Okno pro správu Azure Active Directory – okno předávacího ověřování](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Chybové zprávy přihlašování uživatelů

Pokud se uživatel nemůže přihlásit pomocí předávacího ověřování, může se na obrazovce přihlášení k Azure AD zobrazit jedna z následujících uživatelských chyb: 

|Chyba|Popis|Řešení
| --- | --- | ---
|AADSTS80001|Nelze se připojit ke službě Active Directory|Zajistěte, aby byly servery agenta členy stejné doménové struktury služby AD, jako uživatelé, jejichž hesla je potřeba ověřit, a že se můžou připojit ke službě Active Directory.  
|AADSTS8002|Došlo k vypršení časového limitu při připojování ke službě Active Directory|Zkontrolujte, jestli je k dispozici služba Active Directory a reaguje na žádosti od agentů.
|AADSTS80004|Uživatelské jméno předané agentovi nebylo platné.|Ujistěte se, že se uživatel pokouší přihlásit se správným uživatelským jménem.
|AADSTS80005|Ověřování zjistilo nepředvídatelné WebException|Přechodná chyba. Opakujte požadavek. Pokud se i nadále nedaří, obraťte se na podporu Microsoftu.
|AADSTS80007|Při komunikaci se službou Active Directory došlo k chybě.|Další informace naleznete v protokolech agenta a ověřte, zda služba Active Directory pracuje podle očekávání.

### <a name="users-get-invalid-usernamepassword-error"></a>Uživatelé získají neplatné uživatelské jméno nebo heslo. 

K tomu může dojít, když je místní uživatelské jméno (UPN) uživatele jiné než uživatelské jméno uživatele v cloudu.

Chcete-li ověřit, že se jedná o problém, nejprve otestujte správné fungování agenta předávacího ověřování:


1. Vytvořte testovací účet.  
2. Importujte modul PowerShell na počítači agenta:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Spusťte příkaz vyvolat PowerShell: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Když se zobrazí výzva k zadání přihlašovacích údajů, zadejte stejné uživatelské jméno a heslo, které se použijí k přihlášení k nástroji ( https://login.microsoftonline.com) .

Pokud se zobrazí stejná chyba uživatelského jména a hesla, znamená to, že agent předávacího ověřování funguje správně a problém může být, že místní hlavní název uživatele (UPN) není směrovatelný. Další informace najdete v tématu [Konfigurace alternativního přihlašovacího ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

> [!IMPORTANT]
> Pokud se Azure AD Connect Server nepřipojí k doméně, vyžaduje se požadavek uvedený v článku [Azure AD Connect: požadavky](./how-to-connect-install-prerequisites.md#installation-prerequisites), nejedná se o problém s platným uživatelským jménem nebo heslem.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Důvody neúspěšného přihlášení v centru pro správu Azure Active Directory (potřebuje licenci Premium)

Pokud má tenant přidruženou licenci Azure AD Premium, můžete se také podívat na [sestavu aktivita přihlášení](../reports-monitoring/concept-sign-ins.md) v [centru pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory – sestava přihlášení](./media/tshoot-connect-pass-through-authentication/pta4.png)

Přejděte na **Azure Active Directory**  ->  **přihlášení** v [centru pro správu Azure Active Directory](https://aad.portal.azure.com/) a klikněte na přihlašovací aktivitu konkrétního uživatele. Vyhledejte pole **kód chyby přihlášení** . Namapujte hodnotu tohoto pole na důvod selhání a rozlišení pomocí následující tabulky:

|Kód chyby přihlášení|Důvod neúspěšného přihlášení|Řešení
| --- | --- | ---
| 50144 | Vypršela platnost hesla uživatele pro Active Directory. | Resetujte heslo uživatele v místní službě Active Directory.
| 80001 | Není k dispozici žádný ověřovací agent. | Nainstalujte a zaregistrujte ověřovacího agenta.
| 80002 | Vypršel časový limit žádosti o ověření hesla ověřovacího agenta. | Ověřte, zda je služba Active Directory dosažitelná z ověřovacího agenta.
| 80003 | Ověřovací agent přijal neplatnou odpověď. | Pokud se problém opakuje opakovaně u více uživatelů, ověřte konfiguraci služby Active Directory.
| 80004 | V žádosti o přihlášení byl použit nesprávný hlavní název uživatele (UPN). | Požádejte uživatele, aby se přihlásil pomocí správného uživatelského jména.
| 80005 | Ověřovací agent: Došlo k chybě. | Přechodná chyba. Zkuste to později.
| 80007 | Ověřovací agent se nemohl připojit k Active Directory. | Ověřte, zda je služba Active Directory dosažitelná z ověřovacího agenta.
| 80010 | Ověřovací agent nebyl schopen dešifrovat heslo. | Pokud je problém stále reprodukovatelný, nainstalujte a zaregistrujte nového ověřovacího agenta. A odinstalujte stávající. 
| 80011 | Ověřovací agent nebyl schopen získat dešifrovací klíč. | Pokud je problém stále reprodukovatelný, nainstalujte a zaregistrujte nového ověřovacího agenta. A odinstalujte stávající.
| 80014 | Požadavek na ověření odpověděl po překročení maximálního uplynulého času. | Vypršel časový limit ověřovacího agenta. Otevřete lístek podpory s kódem chyby, ID korelace a časovým razítkem, abyste získali další podrobnosti o této chybě.

>[!IMPORTANT]
>Agenti předávacího ověřování ověřují uživatele služby Azure AD tím, že ověřují jejich uživatelská jména a hesla proti službě Active Directory voláním [rozhraní Win32 LogonUser API](/windows/win32/api/winbase/nf-winbase-logonusera). Výsledkem je, že pokud jste v Active Directory nastavili nastavení přihlásit se ke službě Active Directory tak, aby se omezil přístup k přihlašování do pracovních stanic, budete muset přidat servery, které hostují agenty předávacího ověřování, do seznamu "přihlašování do" serverů. Nedaří se to provést, zablokuje uživatelům přihlášení k Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problémy s instalací ověřovacího agenta

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromáždění protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a kontaktování podpora Microsoftu s vaším problémem.

## <a name="authentication-agent-registration-issues"></a>Problémy s registrací ověřovacího agenta

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registrace ověřovacího agenta se nezdařila z důvodu blokovaných portů.

Ujistěte se, že server, na kterém je nainstalovaný agent ověřování, může komunikovat s našimi adresami URL a porty služby, které jsou [tady](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)uvedené.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registrace ověřovacího agenta se nezdařila z důvodu chyb autorizace tokenu nebo účtu.

Ujistěte se, že používáte pouze cloudový účet globálního správce pro všechny Azure AD Connect nebo samostatné operace instalace a registrace ověřovacího agenta. Došlo k známému problému s účty globálního správce s povolenou MFA. dočasně vypněte MFA (jenom k dokončení operací), jako alternativní řešení.

### <a name="an-unexpected-error-occurred"></a>Došlo k neočekávané chybě.

[Shromáždění protokolů agenta](#collecting-pass-through-authentication-agent-logs) ze serveru a kontaktování podpora Microsoftu s vaším problémem.

## <a name="authentication-agent-uninstallation-issues"></a>Problémy odinstalace ověřovacího agenta

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Zpráva upozornění při odinstalaci Azure AD Connect

Pokud máte ve vašem tenantovi povolené předávací ověřování a pokusíte se o odinstalaci Azure AD Connect, zobrazí se následující upozornění: "Uživatelé se nebudou moci přihlásit ke službě Azure AD, pokud nemáte jiné agenty předávacího ověřování nainstalované na jiných serverech."

Před odinstalováním Azure AD Connect zajistěte, aby byla instalace [vysoce dostupná](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) , aby nedocházelo k přerušení přihlášení uživatelů.

## <a name="issues-with-enabling-the-feature"></a>Problémy s povolením funkce

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Povolení této funkce se nezdařilo, protože nebyly k dispozici žádné agenti ověřování.

Aby bylo možné v tenantovi povolit předávací ověřování, musíte mít aspoň jednoho aktivního ověřovacího agenta. Ověřovacího agenta můžete nainstalovat buď instalací Azure AD Connect nebo samostatného agenta ověřování.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Povolení této funkce se nezdařilo z důvodu blokovaných portů.

Ujistěte se, že server, na kterém je nainstalovaná Azure AD Connect, může komunikovat s našimi adresami URL služby a porty uvedenými [tady](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Povolení této funkce se nepovedlo kvůli chybám autorizace tokenu nebo účtu.

Při povolování této funkce se ujistěte, že používáte jenom cloudový globální účet správce. Existuje známý problém s globálním účtem správce s povolenou službou Multi-Factor Authentication (MFA). dočasně vypněte MFA (jenom k dokončení operace), jako alternativní řešení.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Shromažďování protokolů agenta předávacího ověřování

V závislosti na typu problému, který budete možná potřebovat, je nutné na různých místech vyhledat protokoly předávacího agenta ověřování.

### <a name="azure-ad-connect-logs"></a>Protokoly Azure AD Connect

Chyby související s instalací najdete v protokolech Azure AD Connect v **protokolu%ProgramData%\AADConnect\trace- \* . log**.

### <a name="authentication-agent-event-logs"></a>Protokoly událostí agenta ověřování

V případě chyb souvisejících s agentem ověřování otevřete Prohlížeč událostí aplikace na serveru a zaškrtněte v části **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Pro podrobnou analýzu Povolte protokol "session" (klikněte pravým tlačítkem myši v aplikaci Prohlížeč událostí, abyste našli tuto možnost). Nespouštějte agenta ověřování s tímto protokolem povoleným během normálního provozu. používá se jenom pro řešení potíží. Obsah protokolu se zobrazí až po opětovném zakázání protokolu.



### <a name="detailed-trace-logs"></a>Podrobné protokoly trasování

Pokud chcete řešit chyby při přihlašování uživatelů, vyhledejte protokoly trasování v **%ProgramData%\MICROSOFT\AZURE AD Connect Authentication \\ Agent\Trace**. Mezi tyto protokoly patří důvody, proč přihlášení konkrétního uživatele neuspělo pomocí funkce předávacího ověřování. Tyto chyby jsou také namapovány na důvody neúspěšného přihlášení, které jsou uvedeny v předchozí tabulce důvodů neúspěšných přihlášení. Následuje příklad položky protokolu:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Podrobné informace o chybě (' 1328 ' v předchozím příkladu) získáte otevřením příkazového řádku a spuštěním následujícího příkazu (Poznámka: nahraďte ' 1328 ' skutečným číslem chyby, které vidíte v protokolech.):

`Net helpmsg 1328`

![Předávací ověřování](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Protokoly řadiče domény

Pokud je povoleno protokolování auditu, najdete další informace v protokolech zabezpečení řadičů domény. Jediným způsobem, jak zadat dotaz na žádosti o přihlášení odeslané pomocí předávacích agentů ověřování, je následující:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Čítače sledování výkonu

Jiným způsobem, jak monitorovat agenty ověřování, je sledovat konkrétní čítače sledování výkonu na každém serveru, na kterém je nainstalovaný agent ověřování. Použijte následující globální čítače (**# PTA Authentication**, **#PTA neúspěšná ověřování** a **#PTA úspěšných ověření**) a čítače chyb (**# PTA chyby ověřování**):

![Čítače sledování výkonu předávacího ověřování](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Předávací ověřování poskytuje vysokou dostupnost pomocí více ověřovacích agentů, _nikoli_ vyrovnávání zatížení. V závislosti na konfiguraci neobdrží _všechny_ agenti ověřování zhruba _stejný_ počet požadavků. Je možné, že konkrétní agent ověřování vůbec nepřijímá žádný provoz.
