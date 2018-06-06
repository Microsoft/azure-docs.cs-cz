---
title: Nakonfigurujte zásady podmíněného přístupu na základě zařízení služby Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu na základě zařízení služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: afd29c83a69742d17e7cadda50c0054133458b68
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736605"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Nakonfigurujte zásady podmíněného přístupu na základě zařízení služby Azure Active Directory

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), můžete řídit způsob oprávněným uživatelům můžete přístup k prostředkům. Například můžete omezit přístup k určitým prostředkům spravovaných zařízení. Zásady podmíněného přístupu, která vyžaduje spravovaných zařízení se taky říká zásady podmíněného přístupu podle zařízení.

Toto téma vysvětluje, jak můžete nakonfigurovat zásady podmíněného přístupu na základě zařízení pro Azure AD připojené aplikace. 


## <a name="before-you-begin"></a>Než začnete

Podmíněný přístup využívající zařízení ties **podmíněného přístupu Azure AD** a **správy zařízení služby Azure AD společně**. Pokud nejste obeznámeni s jedním z těchto oblastí ještě, měli byste si přečíst v následujících tématech, nejdřív:

- **[Podmíněný přístup v Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  – Toto téma poskytuje koncepční přehled podmíněného přístupu a souvisejících technologiím.

- **[Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)**  – Toto téma poskytuje přehled různých možností budete muset připojit zařízení s Azure AD. 



## <a name="managed-devices"></a>Spravovaná zařízení  

První mobilní, cloudové první světě Azure Active Directory umožňuje jednotné přihlašování k zařízení, aplikacím a službám odkudkoli. Pro některé prostředky ve vašem prostředí, udělení přístupu ti správní uživatelé nemusí být dostatečně funkční. Kromě ti správní uživatelé můžete také může vyžadovat, aby pokusy o přístup můžete jenom pomocí spravovaného zařízení.

Spravované zařízení je zařízení, které splňuje vaše standardy zabezpečení a dodržování předpisů. Jednoduše řečeno, jsou spravovaná zařízení, zařízení, která jsou pod *nějaká* organizační ovládacího prvku. Ve službě Azure AD předpokladem pro spravované zařízení je, že byl registrován s Azure AD. Registrace zařízení se vytvoří identity zařízení v podobě objekt zařízení. Tento objekt se používají v Azure ke sledování stavu informací o zařízení. Jako správce Azure AD, jste již tento objekt můžete použít k přepnutí (povolit nebo zakázat) stav zařízení.
  
![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Chcete-li získat zařízení registrovaná službou Azure AD, máte tři možnosti:

- **[Azure AD registrované zařízení](device-management-introduction.md#azure-ad-registered-devices)**  – Pokud chcete získat osobní zařízení registrovaná službou Azure AD

- **[Zařízení připojená k Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  – Pokud chcete získat organizační zařízení Windows 10, který není připojený k místní AD registrované s Azure AD. 

- **[Zařízení připojená k hybridní Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  – Pokud chcete získat zařízením s Windows 10, který je připojen k místní AD registrované s Azure AD.

Se spravované zařízení registrovaná zařízení může být hybridní, zařízení nebo zařízení, která byla označena jako kompatibilní, připojený k Azure AD.  

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Vyžadovat hybridní Azure AD zařízení připojená k

V zásadách podmíněného přístupu můžete vybrat **vyžadují zařízení připojeného k hybridní Azure AD** do stavu, že vybrané cloudové aplikace může otevřít pouze pomocí spravovaného zařízení. 

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Toto nastavení platí pouze pro zařízení s Windows 10, které jsou připojeny k místní službě Azure AD. Tato zařízení lze zaregistrovat pouze s Azure AD pomocí připojení k hybridní Azure AD, která je [automatizované procesu](device-management-hybrid-azuread-joined-devices-setup.md) zaregistrované zařízení Windows 10. 

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Co je hybridní Azure AD zařízení připojeného k spravované zařízení?  Pro zařízení, které jsou připojeny k místní AD, se předpokládá, že se vynucuje kontrolu nad těchto zařízení pomocí řešení pro správu, jako třeba **System Center Configuration Manager (SCCM)** nebo **(zásady skupiny)Zásadyskupiny** k jejich správě. Protože neexistuje žádná metoda pro Azure AD k určení, zda některé z těchto metod použilo k zařízení, nutnosti zařízení připojeného k hybridní Azure AD je poměrně slabé mechanismus vyžaduje spravovaných zařízení. Je na vás jako správce a posoudit, jestli metody, které se použijí k místní doméně zařízení jsou dostatečně silné představovat spravované zařízení, pokud takové zařízení je také zařízení připojeného k hybridní Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Možnost *vyžadují zařízení, které chcete označit jako kompatibilní* představuje nejúčinnější způsob požádat o spravovaných zařízení.

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Tato možnost vyžaduje zařízení zaregistrovat u služby Azure AD a také být označen jako dodržuje:
         
- Intune 
- Mobilní zařízení třetích stran spravováno systém, který spravuje zařízení s Windows 10 prostřednictvím integrace Azure AD 
 
![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/46.png)



U zařízení, která je označena jako kompatibilní můžete předpokládat, že: 

- Spravuje mobilní zařízení, která pracovníky používá pro přístup k datům společnosti
- Spravuje mobilní aplikace, které používá pracovníky
- Informace o vaší společnosti je chráněn pomáhá řídit způsob pracovníky přístupy a sdílet je s
- Zařízení a jeho aplikace jsou kompatibilní s požadavky na zabezpečení společnosti




## <a name="next-steps"></a>Další postup

Před konfigurací zásad podmíněného přístupu na základě zařízení ve vašem prostředí, měli byste podniknout podívejte se na [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md).

