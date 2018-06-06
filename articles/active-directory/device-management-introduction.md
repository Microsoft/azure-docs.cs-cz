---
title: Úvod do správy zařízení v Azure Active Directory | Microsoft Docs
description: Zjistěte, jak Správa zařízení vám může pomoct získat kontrolu nad zařízení, která přistupují k prostředkům ve vašem prostředí.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 6384f63139eb64522409c5922e187afd878ebb09
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714132"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Úvod do správy zařízení v Azure Active Directory

První mobilní, cloudové první světě Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízení, aplikacím a službám odkudkoli. S jak narůstá počet zařízení – PŘINESTE si vlastní zařízení (), včetně Odborníci v oblasti IT potýkají s dva dosáhnout cíle:

- Umožnit koncovým uživatelům k dosažení produktivity. kdykoli a kdekoli
- Ochrana podnikových prostředků kdykoli

Pomocí zařízení jsou vaši uživatelé získání přístupu k firemním majetkem. Pokud chcete ochránit vaše podnikové prostředky, jako správce IT, budete chtít mít řízení těchto zařízení. Díky tomu můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

Správa zařízení je také základem pro [podmíněného přístupu na základě zařízení](active-directory-conditional-access-policy-connected-applications.md). Pomocí podmíněného přístupu podle zařízení můžete zajistit, že přístup k prostředkům ve vašem prostředí je možné jenom s spravovaných zařízení.   

Tento článek vysvětluje, jak funguje správa zařízení ve službě Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Získávání zařízení pod kontrolou Azure AD

Chcete-li získat zařízení pod kontrolou Azure AD, máte dvě možnosti:

- Registrace 
- Připojení

**Registrace** zařízení do služby Azure AD umožňuje spravovat identity zařízení. Když je zařízení registrováno, poskytne mu nástroj registrace zařízení služby Azure AD zařízení s identitou, která se používá k ověření zařízení, když se uživatel přihlásí ke službě Azure AD. Identity můžete použít k povolení nebo zakázání zařízení.

V kombinaci s řešením správy mobilních zařízení, jako je například Microsoft Intune, budou atributy zařízení ve službě Azure AD jsou aktualizované o další informace o zařízení. To vám umožňuje vytvořit pravidla podmíněného přístupu, která vynucují, aby přístup měla pouze taková zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů.  Další informace o registraci zařízení v Microsoft Intune najdete v tématu registrace zařízení pro správu v Intune.

**Připojení** zařízení je rozšíření pro registraci zařízení. To znamená, se poskytuje veškeré výhody registrace zařízení a kromě toho také změní stav místní zařízení. Změna stavu místní umožňuje uživatelům přihlásit se do zařízení pomocí organizace pracovní nebo školní účet místo osobní účet.

## <a name="azure-ad-registered-devices"></a>Azure AD registrované zařízení   

Cílem zařízení zaregistrovat Azure AD je poskytnout podporu **PŘINESTE si vlastní zařízení ()** scénář. V tomto scénáři může uživatel přístup k prostředkům vaší organizace Azure Active Directory řídí použití osobních zařízení.  

![Azure AD registrované zařízení](./media/device-management-introduction/03.png)

Přístup je založena na pracovní nebo školní účet, který byl zadán v zařízení.  
Například Windows 10 umožňuje uživatelům přidat pracovní nebo školní účet osobní počítače, tabletu nebo telefonu.  
Když uživatel přidal pracovní nebo školní účet, zařízení je zaregistrované s Azure AD a volitelně zaregistrované v systému pro správu (MDM) mobilních zařízení, který byl nakonfigurován vaší organizace. Uživatelé vaší organizace můžete přidat pracovní nebo školní účet osobní zařízení pohodlně:

- Při přístupu k aplikaci pracovní první
- Ručně pomocí **nastavení** nabídky v případě systému Windows 10 

Pro Windows 10, iOS, Android a systému macOS můžete konfigurovat Azure AD, které registrované zařízení.

## <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Pro zjednodušení je cílem Azure AD, které jsou připojené k zařízení:

- Zařízení ve vlastnictví pracovní nasazení systému Windows 
- Přístup k organizační aplikacím a prostředkům z libovolného zařízení Windows

![Azure AD registrované zařízení](./media/device-management-introduction/02.png)


Jsou těchto cílů dosáhnout tím, že poskytuje uživatelům samoobslužné služby prostředí pro získání zařízení ve vlastnictví pracovní řídí služby Azure AD.  
**Azure AD Join** je určená pro organizace, které chcete být první cloudu nebo jenom pro cloud. Neexistuje žádné omezení velikosti nebo typu organizace, které můžete nasadit připojení ke službě Azure AD. Azure AD Join funguje i i v hybridním prostředí a můžete povolit přístup k místním aplikacím a prostředkům.

Implementace zařízení připojených k Azure AD poskytuje následující výhody:

- **Jednotného přihlašování (SSO)** vaše Azure spravovaných SaaS aplikace a služby. Vaši uživatelé nevidíte další ověřování výzvy při přístupu k pracovním prostředkům. Funkce jednotného přihlašování je i když nejsou připojeni k síťové doméně, která je k dispozici.

- **Enterprise kompatibilní roaming** uživatelská nastavení napříč připojená. Uživatelé nepotřebují k připojení k účtu Microsoft (například Hotmail) zobrazíte nastavení mezi zařízeními.

- **Přístup k Windows Store pro firmy** pomocí účtu Azure AD. Uživatele můžete vybrat z inventář aplikací předem vybraná organizace.

- **Windows Hello** podporu pro zabezpečení a pohodlný přístup k pracovním prostředkům.

- **Omezení přístupu** k aplikacím z jenom zařízení, které splňují zásady dodržování předpisů.

- **Bezproblémový přístup k lokálním prostředkům** při zařízení má směrem pohledu na místní řadič domény.


Při připojení k Azure AD je primárně určený pro organizace, které nemají místní infrastrukturu Windows Server Active Directory, jistě můžete také použít ve scénářích kde:

- Pokud potřebujete získat mobilní zařízení, jako jsou tablety a telefony pod kontrolou nelze například použít připojení k místní doméně.

- Uživatelé se primárně potřebujete přístup k Office 365 nebo jiné aplikace SaaS integrované s Azure AD.

- Chcete spravovat skupinu uživatelů ve službě Azure AD místo ve službě Active Directory. To můžete použít, například sezónních pracovníků, dodavatelů nebo studenty.

- Chcete poskytovat spojující možnosti pracovní procesy ve firemní pobočky ve vzdálených pobočkách s omezenou na místní infrastrukturu.

Můžete nakonfigurovat zařízení připojených k Azure AD pro zařízení s Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Zařízení připojená k hybridní Azure AD

Pro více než deset řada organizací použili umožňující připojení k doméně do svojí místní službě Active Directory:

- Oddělení IT spravovat zařízení vlastněná pracovní z centrálního umístění.

- Uživatelé budou moct přihlašovat ke svým zařízením s jejich služby Active Directory pracovní nebo školní účty. 

Obvykle organizace s nároky místní spoléhají na imaging metody zřízení zařízení a často používají **System Center Configuration Manager (SCCM)** nebo **zásady (zásady skupiny) skupiny** ke správě je.

Pokud vaše prostředí disponuje místní AD nároky a vy chcete také výhody z možnosti poskytované službou Azure Active Directory, můžete implementovat hybridní Azure AD, které jsou připojené k zařízení. Jedná se o zařízení, které jsou obě, připojený k místní službě Active Directory a Azure Active Directory.

![Azure AD registrované zařízení](./media/device-management-introduction/01.png)


Pokud byste měli používat Azure AD hybridní připojené k zařízení:

- Máte Win32 aplikace nasazené do těchto zařízení, které používají protokol NTLM nebo Kerberos.

- Vyžadujete, aby zásady skupiny pro správu zařízení.

- Chcete nadále používat pro vytváření bitových kopií řešení pro konfiguraci zařízení pro vaši zaměstnanci.

Můžete nakonfigurovat hybridní Azure AD pro Windows 10 a zařízení nižší úrovně, jako jsou Windows 8 a Windows 7 připojené zařízení.

## <a name="summary"></a>Souhrn

Správa zařízení ve službě Azure AD můžete: 

- Zjednodušit proces přináší zařízení pod kontrolou Azure AD

- Poskytovat uživatelům snadno použitelný přístup k prostředkům vaší organizace založená na cloudu

Jako pravidlo Flash měli byste použít:

- Azure AD zaregistrovat zařízení:

    - U osobních zařízení 

    - Ručně zaregistrovat zařízení s Azure AD

- Zařízení připojená k Azure AD: 

    - Pro zařízení, které jsou vlastněny vaší organizace

    - Pro zařízení, které jsou **není** připojené k místní AD

    - Ručně zaregistrovat zařízení s Azure AD

    - Chcete-li změnit stav místní zařízení.

- Hybridní Azure AD připojené zařízení pro zařízení, které jsou připojeny k místní AD     

    - Pro zařízení, které jsou vlastněny vaší organizace

    - Pro zařízení, které jsou připojeny k místní AD

    - Na automatickou registraci zařízení s Azure AD

    - Chcete-li změnit stav místní zařízení.



## <a name="next-steps"></a>Další postup

- Chcete-li získat přehled o tom, jak spravovat zařízení na portálu Azure, přečtěte si téma [Správa zařízení pomocí portálu Azure](device-management-azure-portal.md)

- Další informace o podmíněného přístupu podle zařízení, najdete v části [konfigurovat zásady podmíněného přístupu na základě zařízení služby Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- K instalaci:
    - Azure Active Directory zaregistrován zařízení s Windows 10, najdete v části [postup konfigurace služby Azure Active Directory zaregistrované zařízení s Windows 10](device-management-azuread-registered-devices-windows10-setup.md)
    - Zařízení připojená k Azure Active Directory najdete v tématu [zařízení připojená k tom, jak nakonfigurovat služby Azure Active Directory](device-management-azuread-joined-devices-setup.md)
    - Hybridní zařízení připojených k Azure AD, najdete v části [jak ke konfiguraci hybridního připojená k Azure Active Directory zařízení](device-management-hybrid-azuread-joined-devices-setup.md).


