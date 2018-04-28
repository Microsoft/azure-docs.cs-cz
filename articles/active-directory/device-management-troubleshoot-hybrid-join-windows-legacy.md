---
title: Řešení potíží s hybridní Azure Active Directory nižší úrovně zařízení připojená k | Microsoft Docs
description: Řešení potíží s hybridní Azure Active Directory připojené zařízení nižší úrovně.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 43c1907bf3f9bb8eea92dc02889df24a5a0cc9e3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
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

- Maximální počet zařízení na uživatele je zaměřená na zařízení. Například pokud *jdoe* a *jharnett* Přihlaste se do zařízení, samostatné registrace (DeviceID) se vytvoří pro každou z nich **uživatele** informace o kartě.  

- Počáteční registrace / připojení k zařízení, je nakonfigurována se provést k pokusu o přihlášení nebo uzamčení nebo odemčení. Může dojít k 5 minut zpoždění aktivovány úloh služby Plánovač úloh. 

- Opětovné instalace operačního systému nebo ruční opětovná registrace může vytvořit novou registraci na Azure AD, což vede k více položek na kartě informace uživatele na portálu Azure. 

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
    
    - Autoworkplace.exe není schopen bezobslužně ověření pomocí Azure AD ani AD FS. Může to být způsobeno problémy s připojením odesílací vázané sítě na Azure AD adresy URL (zkontrolujte, zda požadavky). Mohou být také že vícefaktorové ověřování (MFA) je povolen nebo nakonfigurovaný pro tohoto uživatele a WIAORMUTLIAUTHN není nakonfigurovaný na federačním serveru (postup kontroly konfigurace). Další možností je této stránce zjišťování domovské sféry čeká pro interakci s uživatelem, což zabraňuje **autoworkplace.exe** bezobslužně získat token.
    
    - Vaše organizace používá Azure AD bezproblémové jednotné přihlašování, `https://autologon.microsoftazuread-sso.com` není k dispozici v nastavení intranetu IE zařízení, a **povolit aktualizace stavového řádku pomocí skriptu** není povolena pro zónu intranetu.

- Bylo dosaženo kvóty

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Služba nereaguje. 

    ![Připojení k síti na pracovišti pro Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Informace o stavu můžete také najít v protokolu událostí v části: **aplikace a služby Log\Microsoft-pracovišti**
  
**Nejběžnější příčiny selhání hybridní Azure AD join jsou:** 

- Váš počítač ani připojený k interní síti vaší organizace ani k síti VPN připojení k místní řadič domény AD.

- Jste přihlášeni k počítači pomocí účtu místního počítače. 

- Problémy s konfigurací služby: 

  - Federační server nakonfigurovaný pro podporu **WIAORMULTIAUTHN**. 

  - Doménové struktury vašeho počítače nemá žádný objekt spojovací bod služby, který odkazuje na název ověřené domény ve službě Azure AD 

  - Uživatel byl dosažen maximální počet zařízení. 

## <a name="next-steps"></a>Další postup

Otázky, najdete v článku [nejčastější dotazy ke správě zařízení](device-management-faq.md)  
