---
title: Jak vyžadovat – spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu podle zařízení služby Azure Active Directory (Azure AD), které vyžadují spravovaných zařízení pro přístup k aplikaci cloudu.
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
ms.openlocfilehash: 5d67abe003afc6f5aec420f4668efa5df7d8dbc4
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414463"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Postupy: Vyžadovat spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem

Ve světě upřednostňujícím mobilní a cloud na prvním Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k aplikacím a službám odkudkoli. Autorizovaní uživatelé měli přístup k vašim cloudovým aplikacím širokou škálu zařízení včetně mobilních a také osobní zařízení. Mnoho prostředí však mít aspoň pár aplikací, které by měl mít přístup jenom zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení jsou také známé jako spravovaná zařízení. 

Tento článek vysvětluje, jak nakonfigurovat zásady podmíněného přístupu, které vyžadují spravovaných zařízení pro přístup k určitým cloudových aplikací ve vašem prostředí. 


## <a name="prerequisites"></a>Požadavky

Vyžadování spravovaných zařízení pro cloudové aplikace access ties **podmíněný přístup Azure AD** a **správu zařízení Azure AD** společně. Pokud nejste obeznámeni s jedním z těchto oblastí ještě, přečtěte si následující témata, nejdřív:

- **[Podmíněný přístup ve službě Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  – Tento článek obsahuje koncepční přehled podmíněného přístupu a související terminologie.

- **[Úvod do správy zařízení ve službě Azure Active Directory](device-management-introduction.md)**  – Tento článek obsahuje přehled různých možností, budete muset získat zařízení pod správou organizace kódu. 


## <a name="scenario-description"></a>Popis scénáře

Zvládnutí rovnováhu mezi zabezpečením a produktivity představuje výzvu. Růst počtu podporovaných zařízení pro přístup k prostředkům cloudu pomáhá zvýšit produktivitu vašich uživatelů. Na druhou stranu pravděpodobně nebudete chtít určitých prostředků ve vašem prostředí, ke kterým přistupují zařízení s úrovní Neznámý ochrany. Pro ovlivněné prostředky je potřeba povolit, že uživatelé mohou pouze k nim přistupovat pomocí spravovaných zařízení. 

Pomocí podmíněného přístupu Azure AD abyste mohli vyřešit tento požadavek se jedna zásada, která uděluje přístup:

- Pro vybrané cloudové aplikace

- Pro vybrané uživatele a skupiny

- Vyžaduje spravované zařízení


## <a name="managed-devices"></a>Spravovaná zařízení  

Jednoduše řečeno, spravovaná zařízení jsou zařízení, která jsou pod *nějaké* organizační ovládacího prvku. Ve službě Azure AD je předpokladem pro spravovaná zařízení, že byl zaregistrován u služby Azure AD. Registrace zařízení vytvoří identitu zařízení v podobě objekt zařízení. Tento objekt se používá Azure ke sledování stavu informace o zařízení. Jako správce Azure AD můžete použít už tento objekt na přepínací tlačítko (povolit nebo zakázat) stav zařízení.
  
![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Pokud chcete získat zařízení registrovaná službou Azure AD, máte tři možnosti:

- **[Azure AD registrované zařízení](devices/overview.md#azure-ad-registered-devices)**  – Pokud chcete získat osobní zařízení zaregistrované v Azure AD

- **[Zařízení připojená k Azure AD](devices/overview.md#azure-ad-joined-devices)**  – Pokud chcete získat organizační zařízení Windows 10, které není připojené k místní AD registrované v Azure AD. 

- **[Zařízení připojená k hybridní službě Azure AD](devices/overview.md#hybrid-azure-ad-joined-devices)**  – Pokud chcete získat Windows 10 nebo podporovaných zařízení nižší úrovně, které je připojené k místní AD registrované v Azure AD.

Se spravované zařízení zaregistrovaného zařízení musí být buď **zařízení připojenému k hybridní službě Azure AD** nebo **zařízení, která byla označena jako vyhovující**.  

![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Vyžadovat Azure hybridních zařízení připojených k AD

Ve své zásady podmíněného přístupu můžete vybrat **vyžadovat zařízení připojené k hybridní službě Azure AD** do stavu, že vybrané cloudové aplikace je přístupný pouze pomocí spravovaných zařízení. 

![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Toto nastavení platí jenom pro Windows 10 nebo nižší úrovně zařízení, jako jsou Windows 7 nebo Windows 8, které jsou připojeny k místní AD. Tato zařízení můžete zaregistrovat pouze s Azure AD pomocí připojení k hybridní službě Azure AD, která je [automatizovat proces](device-management-hybrid-azuread-joined-devices-setup.md) získat zařízení s Windows 10 zaregistrované. 

![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Co je hybridní služby Azure AD připojené zařízení spravované zařízení?  Pro zařízení, které jsou připojené k místní AD, se předpokládá, že se vynucuje kontrolu nad tato zařízení pomocí řešení pro správu, například **System Center Configuration Manageru (SCCM)** nebo **(zásady skupiny)Zásadyskupiny** k jejich správě. Vzhledem k tomu, že neexistuje žádná metoda pro službu Azure AD k určení, zda některé z těchto metod se nastavily pro zařízení, vyžaduje zařízení připojené k hybridní službě Azure AD je mechanismus slabými tak, aby vyžadovala spravované zařízení. Je jenom na vás jako správce a posoudit, jestli metody, které se použijí k místní doméně zařízení jsou dostatečně silné představovat spravované zařízení, pokud se tato zařízení i zařízení připojené k hybridní službě Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Možnost *vyžadovat, aby zařízení bylo označené jako vyhovující* je nejúčinnější požádat o spravované zařízení.

![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Tato možnost vyžaduje zařízení k registraci v Azure AD a také být označený jako dodržující:
         
- Intune.
- Systému pro správu (MDM) třetí strany mobilní zařízení, která spravuje zařízení s Windows 10 prostřednictvím integrace služby Azure AD. Systémy MDM třetí strany pro typy zařízení, operačního systému než Windows 10 nejsou podporovány.
 
![Podmínky na základě zařízení](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Pro zařízení, která je označena jako vyhovující můžete předpokládat, že: 

- Spravuje mobilní zařízení, která vaši pracovníci používají pro přístup k firemním datům
- Spravuje mobilní aplikace, které vaši pracovníci používají
- Informace o vaší společnosti chráněna tím, že pomáhá řídit způsob, jak vaši pracovníci přistupují a nasdílí
- Zařízení a jeho aplikace jsou kompatibilní s požadavky na zabezpečení společnosti




## <a name="next-steps"></a>Další postup

Před konfigurací zásad podmíněného přístupu podle zařízení ve vašem prostředí, byste měli podniknout podívat [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](active-directory-conditional-access-best-practices.md).

