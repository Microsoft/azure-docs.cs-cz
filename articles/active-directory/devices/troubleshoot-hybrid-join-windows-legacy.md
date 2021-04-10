---
title: Řešení potíží se staršími zařízeními připojenými k hybridním Azure Active Directory
description: Řešení potíží s hybridní Azure Active Directory připojená zařízení nižší úrovně.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2611a29ffcfdeb805934eacc54181515ec44f38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578035"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Řešení potíží s modulem hybridní Azure Active Directory připojená zařízení nižší úrovně 

Tento článek se týká pouze následujících zařízení: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Informace o Windows 10 nebo Windows serveru 2016 najdete v tématu [řešení potíží se zařízeními s Windows 10 a Windows serverem 2016 s připojením k hybridním Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

V tomto článku se předpokládá, že jste [nakonfigurovali zařízení se hybridem Azure Active Directory připojená](hybrid-azuread-join-plan.md) k podpoře následujících scénářů:

- Podmíněný přístup založený na zařízeních

Tento článek poskytuje pokyny k odstraňování potíží, jak řešit potenciální problémy.  

**Co byste měli znát:** 

- Hybridní připojení ke službě Azure AD pro zařízení se systémem Windows nižší úrovně funguje mírně jinak než ve Windows 10. Mnoho zákazníků si neuvědomuje, že potřebují AD FS (pro federované domény) nebo bezproblémové nakonfigurované jednotné přihlašování (pro spravované domény).
- Bezproblémové jednotné přihlašování nefunguje v privátním režimu procházení v prohlížeči Firefox a v prohlížečích Microsoft Edge. Nefunguje ani v aplikaci Internet Explorer, pokud je prohlížeč spuštěný v rozšířeném chráněném režimu nebo pokud je povolená konfigurace rozšířeného zabezpečení.
- Pro zákazníky s federované doménami, pokud je spojovací bod služby (SCP) nakonfigurovaný tak, aby odkazoval na název spravované domény (například contoso.onmicrosoft.com namísto contoso.com), nebude připojení hybridní služby Azure AD pro zařízení s Windows nižší úrovně fungovat.
- Stejné fyzické zařízení se ve službě Azure AD objevuje vícekrát, když se na zařízení připojená k hybridní službě Azure AD přihlašuje více uživatelů domény.  Například pokud se k zařízení přihlásí *pnovak* a *jharnett* , vytvoří se pro každou z nich samostatná registrace na kartě informace o **uživateli** . 
- V případě přeinstalace operačního systému nebo ruční opětovné registrace můžete na kartě informace o uživateli získat také více položek zařízení.
- Počáteční registrace/připojení zařízení je nakonfigurované tak, aby se pokusila provést pokus buď při přihlášení, nebo při zamčení nebo odemčení. V rámci úlohy plánovače úloh může být prodleva 5 minut spuštěna. 
- V případě systému Windows 7 SP1 nebo Windows Server 2008 R2 SP1 se ujistěte, že je nainstalovaný [KB4284842](https://support.microsoft.com/help/4284842) . Tato aktualizace zabrání v budoucích selháních ověřování kvůli ztrátě přístupu zákazníka k chráněným klíčům po změně hesla.
- Připojení k hybridní službě Azure AD může selhat, jakmile se uživatel změní na hlavní název uživatele (UPN) a dojde k bezproblémovému procesu ověřování SSO. Během procesu připojování se můžete setkat s tím, že pořád posílá starý hlavní název uživatele (UPN) do služby Azure AD, pokud se nevymažou soubory cookie relace prohlížeče nebo uživatel se explicitně odhlásí a odebere starý hlavní název uživatele (UPN).

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: načtení stavu registrace 

**Ověření stavu registrace:**  

1. Přihlaste se pomocí uživatelského účtu, který provedl připojení k hybridní službě Azure AD.
1. Otevřete příkazový řádek. 
1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Tento příkaz zobrazí dialogové okno, které vám poskytne podrobné informace o stavu připojení.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Snímek obrazovky dialogového okna Workplace Join pro systém Windows. Text, který obsahuje e-mailovou adresu, uvádí, že určité zařízení je připojené k pracovišti." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: vyhodnocení stavu připojení k hybridní službě Azure AD 

Pokud se zařízení nepřipojilo k hybridní službě Azure AD, můžete se pokusit o připojení k hybridní službě Azure AD kliknutím na tlačítko "připojit". Pokud se pokus o připojení k hybridní službě Azure AD nezdaří, zobrazí se podrobnosti o selhání.

**Mezi nejběžnější problémy patří:**

- Nesprávně nakonfigurované AD FS nebo Azure AD nebo síťové problémy

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Snímek obrazovky dialogového okna Workplace Join pro systém Windows. Text hlásí, že při ověřování účtu došlo k chybě." border="false":::
    
   - Autoworkplace.exe se nemůže tiše ověřit pomocí Azure AD nebo AD FS. Příčinou může být chybějící nebo nesprávně nakonfigurované AD FS (pro federované domény) nebo chybějící nebo nesprávně nakonfigurovaná služba Azure AD bez problémů s jedním Sign-On (u spravovaných domén) nebo problémy se sítí. 
   - Může to být, že pro uživatele je povolený nebo nakonfigurovaný Multi-Factor Authentication (MFA) a WIAORMULTIAUTHN není nakonfigurovaný na serveru AD FS. 
   - Další možností je, že stránka zjišťování domovské sféry (HRD) čeká na interakci uživatele, což brání **autoworkplace.exe** v tichém požadavku na token.
   - Je možné, že AD FS a adresy URL služby Azure AD chybí v intranetové zóně IE na klientovi.
   - Kvůli problémům s připojením k síti můžou **autoworkplace.exe** dostat AD FS nebo adresy URL služby Azure AD. 
   - **Autoworkplace.exe** vyžaduje, aby klient měl přímý pohled z klienta na místní řadič domény služby AD v organizaci, což znamená, že se hybridní připojení k Azure AD zdaří jenom v případě, že je klient připojený k intranetu organizace.
   - Vaše organizace používá bezproblémové jednotné přihlašování Azure AD `https://autologon.microsoftazuread-sso.com` nebo `https://aadg.windows.net.nsatc.net` se nezobrazuje v nastavení intranetu v zařízení pro Internet Explorer a **povoluje aktualizace stavového řádku prostřednictvím skriptu** není pro zónu intranetu povolené.
- Nejste přihlášení jako uživatel domény.

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Snímek obrazovky dialogového okna Workplace Join pro systém Windows. Text hlásí, že při ověřování účtu došlo k chybě." border="false":::

   Existuje několik různých důvodů, proč k tomu může dojít:

   - Přihlášený uživatel není uživatel domény (například místní uživatel). Připojení k hybridní službě Azure AD na zařízeních nižší úrovně je podporováno pouze pro uživatele domény.
   - Klient se nemůže připojit k řadiči domény.    
- Byla dosažena kvóta.

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Snímek obrazovky dialogového okna Workplace Join pro systém Windows. Text hlásí chybu, protože uživatel dosáhl maximálního počtu připojených zařízení." border="false":::

- Služba neodpovídá 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Snímek obrazovky dialogového okna Workplace Join pro systém Windows. Text hlásí, že došlo k chybě, protože server neodpověděl." border="false":::

Informace o stavu můžete najít také v protokolu událostí v části **aplikace a služby Log\Microsoft-Workplace JOIN** .
  
**Nejběžnějšími příčinami neúspěšného připojení k hybridní službě Azure AD:** 

- Váš počítač není připojený k interní síti vaší organizace nebo k síti VPN s připojením k místnímu řadiči domény AD.
- K počítači jste přihlášení pomocí účtu místního počítače. 
- Problémy s konfigurací služby: 
   - AD FS server nebyl nakonfigurován pro podporu **WIAORMULTIAUTHN**. 
   - Doménová struktura vašeho počítače nemá žádný objekt spojovacího bodu služby, který odkazuje na váš ověřený název domény ve službě Azure AD. 
   - Nebo pokud je vaše doména spravovaná, pak se bezproblémové jednotné přihlašování nenakonfigurovalo nebo nepracuje.
   - Uživatel dosáhl limitu počtu zařízení. 

## <a name="next-steps"></a>Další kroky

Otázky najdete v tématu [Nejčastější dotazy ke správě zařízení](faq.md) .  
