---
title: Jak chcete – vyžadují spravovaná zařízení pro přístup k aplikaci cloudu s Azure Active Directory podmíněným přístupem | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu na základě zařízení služby Azure Active Directory (Azure AD), které vyžadují spravovaných zařízení pro přístup k aplikaci cloudu.
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
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 066d25e8953a2be4bd64cdd1af79b7f2a25dd5f9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035781"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Postupy: Vyžadovat pro spravovaná zařízení pro přístup k aplikaci cloudu s podmíněným přístupem

První mobilní, cloudové první světě Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k aplikacím a službám odkudkoli. Autorizovaní uživatelé můžete přístup cloudových aplikací z široké škály zařízení, včetně mobilních a také osobní zařízení. Mnoho prostředí však mít alespoň několik aplikací, které by měly být dostupné jenom zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení jsou také známé jako spravovaných zařízení. 

Tento článek vysvětluje, jak můžete konfigurovat zásady podmíněného přístupu, které vyžadují spravovaných zařízení pro přístup k určité cloudových aplikací ve vašem prostředí. 


## <a name="prerequisites"></a>Požadavky

Vyžadování spravovaných zařízení pro cloudové aplikace přístup ties **podmíněného přístupu Azure AD** a **správy zařízení služby Azure AD** společně. Pokud nejste obeznámeni s jedním z těchto oblastí ještě, měli byste si přečíst v následujících tématech, nejdřív:

- **[Podmíněný přístup v Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  – Tento článek obsahuje přehled podmíněného přístupu a souvisejících technologiím.

- **[Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)**  – Tento článek poskytuje přehled různých možností budete muset registraci zařízení v rámci organizace ovládacího prvku. 


## <a name="scenario-description"></a>Popis scénáře

Ovládnutí koncepcí rovnováhu mezi zabezpečením a produktivitu je výzvu. Jak narůstá počet podporovaných zařízení pro přístup k prostředkům cloudu pomáhá zvýšení produktivity uživatelů. Na straně překlopit pravděpodobně nechcete určitých prostředků ve vašem prostředí přístup k zařízení s úrovní neznámé ochrany. Pro příslušné zdroje byste měli vyžadovat, aby uživatelé přístup jenom k jejich použití spravovaných zařízení. 

Pomocí podmíněného přístupu Azure AD které můžete vyřešit tento požadavek s jedinou zásadu, která uděluje přístup:

- Pro vybraný cloud aplikace

- Pro vybraného uživatele a skupiny

- Vyžadování spravované zařízení


## <a name="managed-devices"></a>Spravovaná zařízení  

Jednoduše řečeno, jsou spravovaná zařízení, zařízení, která jsou pod *nějaká* organizační ovládacího prvku. Ve službě Azure AD předpokladem pro spravované zařízení je, že byl registrován s Azure AD. Registrace zařízení se vytvoří identity zařízení v podobě objekt zařízení. Tento objekt se používají v Azure ke sledování stavu informací o zařízení. Jako správce Azure AD, jste již tento objekt můžete použít k přepnutí (povolit nebo zakázat) stav zařízení.
  
![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Chcete-li získat zařízení registrovaná službou Azure AD, máte tři možnosti:

- **[Azure AD registrované zařízení](device-management-introduction.md#azure-ad-registered-devices)**  – Pokud chcete získat osobní zařízení registrovaná službou Azure AD

- **[Zařízení připojená k Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  – Pokud chcete získat organizační zařízení Windows 10, který není připojený k místní AD registrované s Azure AD. 

- **[Zařízení připojená k hybridní Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  – Pokud chcete získat zařízením s Windows 10, který je připojen k místní AD registrované s Azure AD.

Spravované zařízení stane, zaregistrované zařízení musí být buď **zařízení připojeného k hybridní Azure AD** nebo **zařízení, která byla označena jako kompatibilní**.  

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Vyžadovat hybridní Azure AD zařízení připojená k

V zásadách podmíněného přístupu můžete vybrat **vyžadují zařízení připojeného k hybridní Azure AD** do stavu, že vybrané cloudové aplikace může otevřít pouze pomocí spravovaného zařízení. 

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Toto nastavení platí pouze pro zařízení s Windows 10, které jsou připojeny k místní AD. Tato zařízení lze zaregistrovat pouze s Azure AD pomocí připojení k hybridní Azure AD, která je [automatizované procesu](device-management-hybrid-azuread-joined-devices-setup.md) zaregistrované zařízení Windows 10. 

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Co je hybridní Azure AD zařízení připojeného k spravované zařízení?  Pro zařízení, které jsou připojeny k místní AD, se předpokládá, že se vynucuje kontrolu nad těchto zařízení pomocí řešení pro správu, jako třeba **System Center Configuration Manager (SCCM)** nebo **(zásady skupiny)Zásadyskupiny** k jejich správě. Protože neexistuje žádná metoda pro Azure AD k určení, zda některé z těchto metod použilo k zařízení, nutnosti zařízení připojeného k hybridní Azure AD je poměrně slabé mechanismus vyžaduje spravovaných zařízení. Je na vás jako správce a posoudit, jestli metody, které se použijí k místní doméně zařízení jsou dostatečně silné představovat spravované zařízení, pokud takové zařízení je také zařízení připojeného k hybridní Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Možnost *vyžadují zařízení, které chcete označit jako kompatibilní* představuje nejúčinnější způsob požádat o spravovaných zařízení.

![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Tato možnost vyžaduje zařízení zaregistrovat u služby Azure AD a také být označen jako dodržuje:
         
- Intune.
- Systém správy (MDM) mobilních zařízení třetích stran, který spravuje zařízení s Windows 10 prostřednictvím integrace Azure AD. Nejsou podporovány systémy MDM třetí strany pro typy zařízení operační systém než Windows 10.
 
![Podmínek založených na zařízení](./media/active-directory-conditional-access-policy-connected-applications/46.png)



U zařízení, která je označena jako kompatibilní můžete předpokládat, že: 

- Spravuje mobilní zařízení, která pracovníky používá pro přístup k datům společnosti
- Spravuje mobilní aplikace, které používá pracovníky
- Informace o vaší společnosti je chráněn pomáhá řídit způsob pracovníky přístupy a sdílet je s
- Zařízení a jeho aplikace jsou kompatibilní s požadavky na zabezpečení společnosti




## <a name="next-steps"></a>Další postup

Před konfigurací zásad podmíněného přístupu na základě zařízení ve vašem prostředí, měli byste podniknout podívejte se na [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md).

