---
title: Poradce při potížích se staršími hybridními zařízeními služby Azure Active Directory
description: Řešení potíží s hybridníslužbou Azure Active Directory, která se připojila k zařízením nižší úrovně.
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
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379116"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Řešení potíží s hybridními zařízeními Azure Active Directory, která se připojili k zařízením nižší úrovně 

Tento článek se vztahuje pouze na následující zařízení: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Informace o Windows 10 nebo Windows Serveru 2016 najdete [v tématu Poradce při potížích s hybridním Azure Active Directory, který se připojil k zařízením s Windows 10 a Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Tento článek předpokládá, že jste [nakonfigurovali hybridní zařízení Azure Active Directory připojená](hybrid-azuread-join-plan.md) k podpoře následujících scénářů:

- Podmíněný přístup založený na zařízení

Tento článek obsahuje pokyny pro řešení potíží s řešením možných problémů.  

**Co byste měli vědět:** 

- Hybridní připojení Azure AD pro zařízení s Windows nižší úrovně funguje trochu jinak než ve Windows 10. Mnoho zákazníků si neuvědomuje, že potřebují službu AD FS (pro federované domény) nebo bezproblémové jednotné přihlašovací služby (pro spravované domény).
- Pro zákazníky s federovanými doménami, pokud byl spojovací bod služby (SCP) nakonfigurován tak, aby ukazoval na název spravované domény (například contoso.onmicrosoft.com místo contoso.com), pak se hybridní připojení Azure AD pro zařízení se systémem Windows nižší úrovně nefunguje.
- Maximální počet zařízení na uživatele se aktuálně vztahuje také na hybridní zařízení azure ad připojená k nižší úrovni. 
- Stejné fyzické zařízení se zobrazí ve službě Azure AD několikrát, když se více uživatelů domény přihlásí k hybridním zařízením Azure AD, která se k nim připojila.  Například pokud *jdoe* a *jharnett* přihlášení k zařízení, samostatná registrace (DeviceID) je vytvořen pro každý z nich na kartě informace **o uživateli.** 
- Můžete také získat více položek pro zařízení na kartě informace o uživateli z důvodu přeinstalace operačního systému nebo ruční opětovné registrace.
- Počáteční registrace / spojení zařízení je nakonfigurovántak, aby provedl pokus o přihlášení nebo uzamčení / odemknutí. Může dojít k 5minutovému zpoždění, které může být spuštěno úlohou plánovače úloh. 
- Zkontrolujte, zda je [nainstalována aktualizace KB4284842](https://support.microsoft.com/help/4284842) v případě aktualizace Windows 7 SP1 nebo Windows Server 2008 R2 SP1. Tato aktualizace zabraňuje budoucím selháním ověřování z důvodu ztráty přístupu zákazníka k chráněným klíčům po změně hesla.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Načtení stavu registrace 

**Ověření stavu registrace:**  

1. Přihlaste se pomocí uživatelského účtu, který provedl hybridní připojení Azure AD.
1. Otevření příkazového řádku 
1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Tento příkaz zobrazí dialogové okno s podrobnostmi o stavu spojení.

![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Vyhodnocení stavu připojení hybridního Azure AD 

Pokud zařízení nebylo hybridní Azure AD připojen, můžete se pokusit provést hybridní připojení Azure AD kliknutím na tlačítko "Připojit". Pokud se nezdaří pokus o provedení hybridního připojení Azure AD, zobrazí se podrobnosti o selhání.

**Mezi nejčastější problémy patří:**

- Chybně nakonfigurované problémy se službou AD FS nebo Azure AD nebo sítí

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe nelze bezobslužně ověřit pomocí Azure AD nebo AD FS. Příčinou může být chybějící nebo nesprávně nakonfigurované služby AD FS (pro federované domény) nebo chybějící nebo nesprávně nakonfigurované jednotné jednotné přihlašování azure ad (pro spravované domény) nebo problémy se sítí. 
   - Je možné, že vícefaktorové ověřování (MFA) je povoleno nebo konfigurováno pro uživatele a WIAORMULTIAUTHN není nakonfigurován na serveru Služby AD FS. 
   - Další možností je, že stránka zjišťování domovské sféry (HRD) čeká na interakci s uživatelem, což zabrání **automatickému pracovišti.exe** v tichém vyžádání tokenu.
   - Je možné, že adresy URL služby AD FS a Azure AD v intranetové zóně aplikace Internet Na straně Klienta chybí.
   - Problémy s připojením k síti může být brání **autoworkplace.exe** z dosažení Služby AD FS nebo adresy URL Azure AD. 
   - **Autoworkplace.exe** vyžaduje, aby klient měl přímý zorný pohled od klienta k místnímu řadiči domény služby AD organizace, což znamená, že hybridní připojení Azure AD je úspěšné pouze v případě, že je klient připojen k intranetu organizace.
   - Vaše organizace používá Azure AD bezproblémové `https://aadg.windows.net.nsatc.net` jednotné přihlašování nebo `https://autologon.microsoftazuread-sso.com` nejsou k dispozici v nastavení intranetu IE zařízení a **povolit aktualizace stavového řádku pomocí skriptu** není povolena pro zónu Intranet.
- Nejste přihlášeni jako uživatel domény.

   ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Existuje několik různých důvodů, proč k tomu může dojít:

   - Přihlášený uživatel není uživatelem domény (například místním uživatelem). Hybridní připojení Azure AD na zařízeních nižší úrovně je podporované jenom pro uživatele domény.
   - Klient se nemůže připojit k řadiči domény.    
- Bylo dosaženo kvóty.

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Služba neodpovídá. 

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Informace o stavu najdete také v protokolu událostí v části: **Protokol aplikací a služeb\Připojení k microsoft-pracovišti**
  
**Nejběžnější příčiny neúspěšného hybridního připojení Azure AD jsou:** 

- Počítač není připojen k interní síti vaší organizace nebo k síti VPN s připojením k místnímu řadiči domény služby AD.
- Jste přihlášeni k počítači pomocí účtu místního počítače. 
- Problémy s konfigurací služby: 
   - Server služby AD FS nebyl nakonfigurován pro podporu **wiaormultiauthn**. 
   - Doménová struktura počítače nemá žádný objekt Služby Connection Point, který by ve službě Azure AD ukazoval na ověřený název domény. 
   - Nebo pokud je vaše doména spravována, pak bezproblémové jednotné přihlašovací služby nebylo nakonfigurováno nebo funguje.
   - Uživatel dosáhl limitu zařízení. 

## <a name="next-steps"></a>Další kroky

Dotazy najdete v [nejčastějších dotazech ke správě zařízení.](faq.md)  
