---
title: Řešení potíží s hybridní Azure Active Directory nižší úrovně zařízení připojená k | Microsoft Docs
description: Řešení potíží s hybridní Azure Active Directory připojené zařízení nižší úrovně.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d41e83c11f33b0bcbe4ea632332f2cd8bb12313f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714108"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Nižší úrovně zařízení připojená k řešení potíží s hybridní Azure Active Directory 

V tomto článku se vztahuje pouze na následujících zařízeních: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Pro Windows 10 nebo Windows Server 2016, najdete v části [hybridní Poradce při potížích s Azure Active Directory připojené zařízení s Windows 10 a Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Tento článek předpokládá, že máte [nakonfigurované hybridní Azure Active Directory zařízení připojená k](device-management-hybrid-azuread-joined-devices-setup.md) k podporují následující scénáře:

- Podmíněný přístup využívající zařízení

- [Enterprise cestovní nastavení](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello pro firmy](active-directory-azureadjoin-passport-deployment.md) 





Tento článek poskytuje pokyny o tom, jak vyřešit potenciální problémy při řešení potíží.  

**Co byste měli vědět:** 

- Maximální počet zařízení na uživatele je zaměřená na zařízení. Například pokud *jdoe* a *jharnett* přihlášení do zařízení, samostatné registrace (DeviceID) se vytvoří pro každou z nich **uživatele** informace o kartě.  

- Počáteční registrace / připojení k zařízení, je nakonfigurována se provést k pokusu o přihlášení nebo uzamčení nebo odemčení. Může dojít k 5 minut zpoždění aktivovány úloh služby Plánovač úloh. 

- Přeinstalování operačního systému nebo ruční opětovná registrace může vytvořit nové registrace ve službě Azure AD, což vede k více položek na kartě informace uživatele na portálu Azure. 

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1: Načíst stav registrace 

**Chcete-li ověřit stav registrace:**  

1. Přihlásit se pomocí uživatelského účtu, který provedl spojení hybridní Azure AD.

2. Otevřete příkazový řádek jako správce 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Tento příkaz zobrazí dialogové okno, které vám poskytne další informace o stavu připojení.

![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2: Vyhodnoťte hybridní stav připojení k Azure AD 

Pokud připojení k Azure AD hybridní nebyla úspěšná, dialogové okno vám poskytuje podrobnosti o problému, který došlo k chybě.

**Nejběžnějším problémům, jsou:**

- Konfigurace služby AD FS nebo služby Azure AD

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Nejsou přihlášeni jako uživatel domény

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Existuje několik různých důvodů, proč k tomu může dojít:
    
    - Přihlášeného uživatele není uživatelem domény (například místního uživatele). Hybridní připojení k Azure AD na nižší úrovni. zařízení je podporována pouze pro uživatele domény.
    
    - Autoworkplace.exe není schopen bezobslužně ověření pomocí Azure AD ani AD FS. To může být způsobeno odesílací vázané problémů s připojením k adresám URL, Azure AD. Také je možné, že vícefaktorové ověřování (MFA) je povolen nebo nakonfigurovaný pro tohoto uživatele a WIAORMUTLIAUTHN není nakonfigurovaný na federačním serveru. Další možností je této stránce zjišťování domovské sféry čeká pro interakci s uživatelem, což zabraňuje **autoworkplace.exe** bezobslužně získat token.
    
    - Vaše organizace používá Azure AD bezproblémové jednotné přihlašování, `https://autologon.microsoftazuread-sso.com` nebo `https://aadg.windows.net.nsatc.net` nejsou k dispozici v nastavení intranetu IE zařízení, a **povolit aktualizace stavového řádku pomocí skriptu** není povolena pro zónu intranetu.

- Bylo dosaženo kvóty

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Služba nereaguje. 

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Informace o stavu můžete také najít v protokolu událostí v části: **aplikace a služby Log\Microsoft-pracovišti**
  
**Nejběžnější příčiny selhání hybridní Azure AD join jsou:** 

- Váš počítač není připojený k interní síti vaší organizace nebo k síti VPN připojení k místní řadič domény AD.

- Jste přihlášeni k počítači pomocí účtu místního počítače. 

- Problémy s konfigurací služby: 

  - Federační server nakonfigurovaný pro podporu **WIAORMULTIAUTHN**. 

  - Doménové struktury vašeho počítače nemá žádný objekt spojovací bod služby, který odkazuje na název ověřené domény ve službě Azure AD 

  - Uživatel byl dosažen maximální počet zařízení. 

## <a name="next-steps"></a>Další postup

Otázky, najdete v článku [nejčastější dotazy ke správě zařízení](device-management-faq.md)  
