---
title: Jak na - konfigurace Azure Active Directory zásady podmíněného přístupu pro pokusy o přístup z nedůvěryhodných sítích | Microsoft Docs
description: Zjistěte, jak nakonfigurovat tak, aby zásady podmíněného přístupu v Azure Active Directory (Azure AD) pro pokusy o přístup z nedůvěryhodných sítích.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 952a3a3952a96c7125e0b0dbe770b72c17a57101
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232335"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Postupy: Konfigurace zásad podmíněného přístupu pro nedůvěryhodným sítím pokouší o přístup   

První mobilní, cloudové první světě Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízení, aplikacím a službám odkudkoli. V důsledku toho uživatelé přístup cloudových aplikací nejen z vaší podnikové síti, ale také z libovolného místa a nedůvěryhodné sítě Internet. S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), můžete řídit způsob Autorizovaní uživatelé můžou používat vaše cloudové aplikace. Jeden požadavek na běžné v tomto kontextu je k řízení pokusů o přístup inicializována z nedůvěryhodných sítích. Tento článek vám poskytne informace, které je třeba nakonfigurovat zásadu podmíněného přístupu, která zpracovává tento požadavek. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s: 

- Základní koncepty Azure AD podmíněného přístupu 
- Konfigurace zásad podmíněného přístupu na portálu Azure

Přečtěte si:

- [Co je podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-azure-portal.md) – přehled podmíněného přístupu 

- [Rychlý úvod: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md) – Pokud chcete získat nějaké zkušenosti s konfigurace zásad podmíněného přístupu. 


## <a name="scenario-description"></a>Popis scénáře

Na hlavní server rovnováhu mezi zabezpečením a produktivitu, může být dostatečné pro vás vyžadují jenom uživatelů ověřování pomocí hesla. Když je proveden pokus o přístup z umístění nedůvěryhodné sítě, je však zvýšené riziko, že přihlášení nejsou provádí oprávněných uživatelů. Jak tuto situaci řešit, můžete zablokovat pokusy o přístup z nedůvěryhodných sítích. Alternativně můžete také vyžadovat vícefaktorové ověřování (MFA) a získat zpět další záruku toho, že byl proveden pokus o legitimní vlastníka účtu. 

Pomocí podmíněného přístupu Azure AD které můžete vyřešit tento požadavek s jedinou zásadu, která uděluje přístup: 

- Pro vybraný cloud aplikace

- Pro vybraného uživatele a skupiny  

- Vyžadování vícefaktorového ověřování 

- Když je proveden pokus o přístup z: 

    - Umístění, která není důvěryhodná


## <a name="considerations"></a>Požadavky

Výzvy v tomto scénáři je převod *když je proveden pokus o přístup z umístění, která není důvěryhodná* do stavu podmíněného přístupu. V zásadách podmíněného přístupu, můžete nakonfigurovat [umístění podmínku](active-directory-conditional-access-locations.md) adresu scénářů, které se vztahují k umístění v síti. Podmínka umístění umožňuje vybrat [s názvem umístění](active-directory-conditional-access-locations.md#named-locations), která představují logické skupiny rozsahů adres IP, jiných zemí a oblastí.  

Obvykle se vaše organizace vlastní jeden nebo více rozsahů adres, například 199.30.16.0 - 199.30.16.24.
Můžete nakonfigurovat s názvem umístění:

- Určení tento rozsah (199.30.16.0/24) 

- Přiřazení popisný název, jako **podnikové sítě** 


Namísto pokusu o definovat, co jsou všechny umístění, které nejsou důvěryhodné, můžete:

- Zahrnout 

    ![Podmíněný přístup](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Vyloučit všechny důvěryhodné umístění 

    ![Podmíněný přístup](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Implementace

S přístupem uvedených v tomto článku můžete teď konfigurovat zásadu podmíněného přístupu pro nedůvěryhodných umístěních. Vždy byste měli otestovat vaše zásady před distribucí do produkčního prostředí, abyste měli jistotu, že funguje podle očekávání. V ideálním případě byste měli počáteční testy v testovacím klientem provést Pokud je to možné. Další informace najdete v tématu [by měl nasazení nových zásad](active-directory-conditional-access-best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o podmíněného přístupu, přečtěte si téma [co je podmíněného přístupu v Azure Active Directory?](active-directory-conditional-access-azure-portal.md)