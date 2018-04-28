---
title: Azure Active Directory Identity Protection playbook | Microsoft Docs
description: Zjistěte, jak Azure AD Identity Protection umožňuje omezit možnost útočník zneužít ohroženými identity nebo zařízení a zabezpečit identity nebo zařízení, která byla dříve by mohly vzbuzovat podezření nebo známé došlo k narušení.
services: active-directory
keywords: ochrany identit Azure active directory, cloud app discovery,. Správa aplikací, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 210d097f0719725a0ecf145ce536875a383b04e6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure seznam strategií ochrany identit Active Directory

Tato playbook vám pomůže:

* Naplnění dat v prostředí Identity Protection simulace rizikových událostech a ohrožení zabezpečení
* Nastavit zásady podmíněného přístupu na základě riziko a otestovat vliv těchto zásad


## <a name="simulating-risk-events"></a>Simulaci rizikových událostí

Tato část vám poskytne kroky pro simulaci následující typy událostí rizika:

* Přihlášení z anonymních IP adres (snadno)
* Přihlášení z neznámých míst (střední)
* Nemožná cesta do netypických míst (obtížné)

Další události riziko nelze simulated zabezpečeným způsobem.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Další informace o této události riziko najdete v tématu [přihlášení z anonymních IP adres](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Dokončení následující postup vyžaduje použití:

- [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en) k simulaci anonymních IP adres. Možná budete muset použít virtuální počítač, pokud vaše organizace omezuje pomocí prohlížeče Tor.
- Testovací účet, který dosud není registrován u služby Multi-Factor authentication.

**Pro simulaci přihlášení z anonymních IP adresy, proveďte následující kroky**:

1. Pomocí [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en), přejděte na [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Zadejte přihlašovací údaje účtu, který se má zobrazit v **přihlášení z anonymních IP adres** sestavy.

Přihlášení se zobrazí na řídicím panelu ochrany identit v rámci 10 až 15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Další informace o této události riziko najdete v tématu [přihlášení z neznámých míst](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations). 

Pro simulaci neznámých míst, budete muset přihlásit z umístění a zařízení, které testovací účet nebyl přihlášení z před.

Následující postup používá nově vytvořený:

- Připojení VPN, aby simuloval nové umístění.

- Virtuální počítač, aby simuloval nového zařízení.

Dokončení následující postup vyžaduje, abyste uživatelský účet, který se má použít:

- Minimálně 30 dnů přihlášení historie.
- Povolit službu Multi-Factor authentication.


**Pro simulaci přihlášení z neznámého umístění, proveďte následující kroky**:

1. Při přihlášení pomocí účtu test, nezdaří ověřovací test MFA není předáním ověřovací test MFA.
2. Pomocí nové síť VPN, přejděte do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) a zadejte přihlašovací údaje účtu testu.
   

Přihlášení se zobrazí na řídicím panelu ochrany identit v rámci 10 až 15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Nemožná cesta do netypických umístění

Další informace o této události riziko najdete v tématu [Impossible dostavit do netypických umístění](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations). 

Simulaci podmínku neuskutečnitelná cesta je složité, protože algoritmus využívá strojové učení k odstraňování plevele na hodnotu false pozitivních třeba neuskutečnitelná cesta ze známé zařízení, nebo přihlášení z sítě VPN, které se používají jinými uživateli v adresáři. Algoritmus navíc vyžaduje historii přihlášení 14 dnů a 10 přihlášení uživatele, před zahájením generování rizikových událostech. Z důvodu složitých strojového učení modely a výše uvedených pravidel je pravděpodobné, že následující kroky nevedou k riziko událostí. Můžete chtít replikovat tyto kroky pro více účtů Azure AD k publikování této události riziko.


**Pro simulaci nemožná cesta do netypických umístění, proveďte následující kroky**:

1. Pomocí standardní prohlížeč, přejděte do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Zadejte přihlašovací údaje účtu, pomocí kterého chcete generovat událost riziko neuskutečnitelná cesta pro.
3. Změna uživatelského agenta. Můžete změnit uživatelský agent v aplikaci Internet Explorer z nástrojů pro vývojáře, nebo změnit váš uživatelský agent v Firefox nebo Chrome pomocí doplňku přepínači uživatelského agenta.
4. Změníte IP adresu. Můžete změnit IP adresu pomocí sítě VPN, rozšíření Tor nebo otáčí nový počítač v Azure v různých datových center.
5. Přihlaste se do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) pomocí stejných přihlašovacích údajů jako před a během několika minut po předchozí přihlášení.

Přihlášení se zobrazí v řídicím panelu ochrany identit v rámci 2 – 4 hodiny.

## <a name="simulating-vulnerabilities"></a>Simulaci ohrožení zabezpečení
Ohrožení zabezpečení jsou slabá místa v prostředí Azure AD, které může zneužít objektu actor chybný. Aktuálně jsou prezentované 3 typy ohrožení zabezpečení v Azure AD Identity Protection, které využít další funkce služby Azure AD. Tyto chyby zabezpečení se zobrazí na řídicím panelu ochrany identit automaticky po tyto funkce jsou nastavené.

* Azure AD [vícefaktorového ověřování](authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 


## <a name="testing-security-policies"></a>Testování zásady zabezpečení

Tato část poskytuje kroky pro testování riziko uživatele a nastavení zásad zabezpečení riziko přihlášení.


### <a name="user-risk-security-policy"></a>Zásada zabezpečení riziko uživatelů

Další informace najdete v tématu [zásada zabezpečení uživatelů riziko](active-directory-identityprotection.md#user-risk-security-policy).

![Riziko uživatele](./media/active-directory-identityprotection-playbook/02.png "Playbook")


**K otestování zásady uživatele rizika zabezpečení, proveďte následující kroky**:

1. Přihlaste se do [ https://portal.azure.com ](https://portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho klienta.
2. Přejděte na **ochrany identit**. 
3. Na **Azure AD Identity Protection** klikněte na tlačítko **riziko zásady uživatele**.
4. V **přiřazení** vyberte požadovanou uživatelům (a skupiny) a úroveň rizika uživatele.

    ![Riziko uživatele](./media/active-directory-identityprotection-playbook/03.png "Playbook")

5. V části ovládací prvky, vyberte požadované řízení přístupu (například vyžadovat změny hesla).
5. Jako **vynutit zásady**, vyberte **vypnout**.
6. Zvýšení oprávnění uživatele riziko testovací účet, například jeden z rizikových událostech simulaci několikrát.
7. Počkejte několik minut a potom ověřte, zda úrovni uživatele pro vaše uživatele je střední. Pokud ne, simulovat další riziko události pro uživatele.
8. Jako **vynutit zásady**, vyberte **na**.
9. Nyní můžete otestovat podmíněného přístupu na základě riziko uživatele po přihlášení uživatele pomocí úroveň rizika zvýšenými oprávněními.
    
    

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení riziko přihlášení

Další informace najdete v tématu [zásada zabezpečení uživatelů riziko](active-directory-identityprotection.md#user-risk-security-policy).

![Přihlášení riziko](./media/active-directory-identityprotection-playbook/01.png "Playbook")


**K testování znaménkem v zásadách riziko, proveďte následující kroky:**

1. Přihlaste se do [ https://portal.azure.com ](https://portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho klienta.

2. Přejděte na **Azure AD Identity Protection**.

3. V hlavním **Azure AD Identity Protection** klikněte na tlačítko **zásad přihlašování riziko**. 

4. V **přiřazení** vyberte požadovanou uživatelům (a skupiny) a přihlášení úroveň rizika.

    ![Přihlášení riziko](./media/active-directory-identityprotection-playbook/04.png "Playbook")


5. V **ovládací prvky** vyberte požadovanou řízení přístupu (například **vyžadovat vícefaktorové ověřování**). 

6. Jako **vynutit zásady**, vyberte **na**.

7. Klikněte na **Uložit**.

8. Nyní můžete otestovat podmíněného přístupu na základě riziko přihlášení po přihlášení pomocí rizikové relace (například pomocí prohlížeče Tor). 

 




## <a name="see-also"></a>Další informace najdete v tématech

- [Ochrany identit Azure Active Directory](active-directory-identityprotection.md)

