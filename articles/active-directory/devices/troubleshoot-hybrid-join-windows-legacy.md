---
title: Zařízení s nižší úrovně připojená k řešení potíží s hybridní služby Azure Active Directory | Dokumentace Microsoftu
description: Řešení potíží s hybridní služby Azure Active Directory zařízení připojená k nižší úrovně
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7054946a15ca27f4b5a36d3d2ac712cba2fd68d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185124"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Zařízení s nižší úrovně připojená k řešení potíží s hybridní služby Azure Active Directory 

V tomto článku se vztahuje pouze na následujících zařízeních: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Pro Windows 10 nebo Windows Server 2016, najdete v článku [zařízení s Windows 10 a Windows serveru 2016 připojená k hybridní Poradce při potížích s Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

Tento článek předpokládá, že máte [nakonfigurované hybridní služby Azure Active Directory zařízení připojená k](hybrid-azuread-join-plan.md) pro podporu následujících scénářů:

- Podmíněný přístup podle zařízení


Tento článek poskytuje pokyny o tom, jak vyřešit potenciální problémy při řešení potíží.  

**Co byste měli vědět:** 

- Připojení k hybridní službě Azure AD pro starší verze Windows zařízení funguje trochu jinak než nemá ve Windows 10. Mnoho zákazníků není dobré si uvědomit, že potřebují služby AD FS (u federovaných domén) nebo bezproblémové jednotné přihlašování nakonfigurovali (pro spravované domény).

- Pro zákazníky s federovaných domén pokud bod připojení služby (SCP) byla nakonfigurovaná tak, aby odkazovala na název spravované domény (například contoso.onmicrosoft.com, ne contoso.com), pak hybridní připojení k Azure AD pro zařízení s Windows nižší úrovně se nebude fungovat.

- Maximální počet zařízení na uživatele v současné době platí také pro zařízení připojená k Azure AD hybridní nižší úrovně. 

- Více než jednou ve službě Azure AD, kdy více uživatelů domény přihlášení zařízení připojených k Azure AD hybridní nižší úrovně se zobrazí stejné fyzické zařízení.  Například pokud *jdoe* a *jharnett* přihlášení k zařízení, samostatných registrací (DeviceID) se vytvoří pro každý z nich ve **uživatele** Karta informace. 

- Můžete také získat několik záznamů pro zařízení na kartě informace o uživateli z důvodu přeinstalaci operačního systému nebo ruční opětovné registraci.

- Počáteční registraci / připojení k zařízení umožňují provést pokus o přihlášení nebo uzamčení nebo odemčení. Může dojít k 5 minut, než aktivované úloh služby Plánovač úloh. 

- Ujistěte se, že [KB4284842](https://support.microsoft.com/help/4284842) je nainstalována v případě Windows 7 SP1 nebo Windows Server 2008 R2 SP1. Tato aktualizace brání selhání budoucích ověřování kvůli ztrátám zákazníka přístup k chráněné klíče po změně hesla.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Načíst stav registrace 

**Pokud chcete ověřit stav registrace:**  

1. Přihlásit se pomocí uživatelského účtu, který se má provést připojení k Azure AD hybridní.

2. Otevřete příkazový řádek 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Tento příkaz zobrazí dialogové okno, které vám poskytne podrobné informace o stavu připojení.

![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Vyhodnocení hybridního stav připojení k Azure AD 

Pokud zařízení nebyla připojená k hybridní Azure AD, pokuste se provést připojení k hybridní službě Azure AD. Kliknutím na tlačítko "Join". Pokud se nezdaří pokus provést připojení k hybridní službě Azure AD, zobrazí se podrobnosti o chybě.


**Nejčastější problémy jsou:**

- A správně nakonfigurovaný. Služba AD FS nebo služby Azure AD nebo problémy sítě

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
    - Autoworkplace.exe nedokáže bezobslužné ověření pomocí Azure AD nebo AD FS. To může být způsobeno chybějícími nebo špatně nakonfigurovaný. Služba AD FS (u federovaných domén) nebo chybějící nebo nesprávně nakonfigurované Azure AD bezproblémové jednotné přihlašování (pro spravované domény) nebo problémů se sítí. 
    
     - Je možné, že vícefaktorové ověřování (MFA) je povolený a nakonfigurovaný pro tohoto uživatele a WIAORMUTLIAUTHN není nakonfigurovaná na serveru služby AD FS. 
     
     - Další možností je, že tuto stránku zjišťování domovské sféry čeká pro interakci s uživatelem, což zabrání **autoworkplace.exe** tiše vyžádat token.
     
     - Je možné, že adresy URL služby Azure AD a AD FS chybí v zóně intranetu IE na straně klienta.
     
     - Problémy se síťovým připojením možná nepůjde **autoworkplace.exe** dosažení služby AD FS nebo adresy URL Azure AD. 
     
     - **Autoworkplace.exe** vyžaduje, aby měl s přímým přístupem na dohled z klienta orgnanization místní klient řadič domény služby AD, což znamená, že připojení k hybridní službě Azure AD úspěšné, jenom když je klient připojen k intranetu organizace .
     
     - Vaše organizace používá Azure AD bezproblémové jednotné přihlašování, `https://autologon.microsoftazuread-sso.com` nebo `https://aadg.windows.net.nsatc.net` nejsou k dispozici v nastavení aplikace Internet Explorer intranetu zařízení, a **povolit aktualizace stavového řádku prostřednictvím skriptu** není povolená pro zónu intranetu.

- Nejste přihlášení jako uživatel domény

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Existuje několik různých důvodů, proč tato situace může nastat:
    
    - Přihlášený uživatel není uživatelem domény (například místní uživatel). Hybridní připojení k Azure AD na zařízeních s nižší úrovně je podporována pouze pro uživatele domény.
    
    - Klient není možné se připojit k řadiči domény.    

- Bylo dosaženo kvóty

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Služba nereaguje 

    ![Připojení k pracovní ploše pro Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Informace o stavu můžete také najít v protokolu událostí v části: **Aplikace a služby Log\Microsoft Workplace Join**
  
**Nejběžnější příčiny selhání hybridní službě Azure AD join jsou:** 

- Počítač není připojený k interní síti vaší organizace nebo k síti VPN s připojením k místní řadič domény služby AD.

- Jste přihlášeni k počítači pomocí účtu místního počítače. 

- Problémy s konfigurací služby: 

  - Server služby AD FS není nakonfigurován pro podporu **WIAORMULTIAUTHN**. 

  - Doménová struktura počítače nemá žádný bod připojení služby objekt, který odkazuje na název ověřené domény ve službě Azure AD 
  
  - Nebo pokud spravované domény, pak bezproblémového jednotného přihlašování nebyl nakonfigurován nebo nepracuje.

  - Uživatel dosáhl limitu počtu zařízení. 

## <a name="next-steps"></a>Další postup

Dotazy, najdete v článku [nejčastější dotazy ke správě zařízení](faq.md)  
