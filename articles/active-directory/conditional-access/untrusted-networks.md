---
title: Jak – zásady podmíněného přístupu konfigurovat Azure Active Directory pro pokusy o přístup z nedůvěryhodných sítích | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu v Azure Active Directory (Azure AD) k pro pokusy o přístup z nedůvěryhodných sítích.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d170cdb42f7e3a368a50279f8a311a4ae94689df
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601805"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Postupy: Konfigurace zásad podmíněného přístupu pro nedůvěryhodným sítím pokouší o přístup   

Ve světě upřednostňujícím mobilní a cloud na prvním Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízení, aplikacím a službám odkudkoli. V důsledku toho vaše mohou uživatelé vašich cloudových aplikací nejen v síti vaší organizace, ale také z libovolného místa a nedůvěryhodné sítě Internet. S [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), můžete určit, jak Autorizovaní uživatelé můžou přistupovat k vašim cloudovým aplikacím. Jeden běžné požadavky v tomto kontextu je potřeba zkontrolovat pokusů o přístup iniciovaná nedůvěryhodným sítím. Tento článek obsahuje informace, na kterých je nutné nakonfigurovat zásady podmíněného přístupu, která zpracovává tento požadavek. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s: 

- Základní koncepty podmíněný přístup Azure AD 
- Konfigurace zásad podmíněného přístupu na webu Azure Portal

Přečtěte si:

- [Co je podmíněný přístup v Azure Active Directory](../active-directory-conditional-access-azure-portal.md) – přehled podmíněného přístupu 

- [Rychlý start: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md) – Pokud chcete získat nějaké zkušenosti s konfigurací zásad podmíněného přístupu. 


## <a name="scenario-description"></a>Popis scénáře

Do hlavní větve rovnováhu mezi zabezpečením a produktivitu, může být dostačující pro budete potřebovat jenom uživatelů ověřit pomocí hesla. Když je proveden pokus o přístup z umístění nedůvěryhodné sítě, jsou však zvýšené riziko, že přihlášení nejsou prováděné oprávněným uživatelům. Jak tuto situaci řešit, můžete blokovat pokusy o přístup z nedůvěryhodných sítích. Alternativně můžete také vyžadovat vícefaktorové ověřování (MFA) a získat zpět další záruku, že byl proveden pokus o oprávněný vlastníkem účtu. 

Pomocí podmíněného přístupu Azure AD abyste mohli vyřešit tento požadavek se jedna zásada, která uděluje přístup: 

- Pro vybrané cloudové aplikace

- Pro vybrané uživatele a skupiny  

- Vyžadování vícefaktorového ověřování 

- Když je proveden pokus o přístup z: 

    - Umístění, která není důvěryhodná


## <a name="considerations"></a>Požadavky

Výzvy v tomto scénáři je převod *když je proveden pokus o přístup z umístění, která není důvěryhodná* do podmínka podmíněného přístupu. V zásadách podmíněného přístupu, můžete nakonfigurovat [podmínka umístění](location-condition.md) k řešení scénářů, které se vztahují k umístění v síti. Podmínka umístění vám umožní vybrat pojmenovaná umístění, které představují logické skupiny rozsahů adres IP, zemích a oblastech.  

Obvykle vaše organizace vlastní jeden nebo více rozsahů adres, například 199.30.16.0 - 199.30.16.24.
Můžete nakonfigurovat pojmenovaných umístění podle:

- Zadání tohoto rozsahu (199.30.16.0/24) 

- Přiřazení popisný název, jako **podnikové sítě** 


Namísto pokusu o definovat, jaké jsou všechna místa, které nejsou důvěryhodné, můžete:

- Zahrnout 

    ![Podmíněný přístup](./media/untrusted-networks/02.png)

- Vyloučit všechny důvěryhodné umístění 

    ![Podmíněný přístup](./media/untrusted-networks/01.png)



## <a name="implementation"></a>Implementace

S přístupem uvedených v tomto článku teď můžete nakonfigurovat zásady podmíněného přístupu pro nedůvěryhodných umístěních. Vždy byste měli testovat vaše zásady před zavedením do produkčního prostředí, abyste měli jistotu, že funguje podle očekávání. V ideálním případě byste měli počáteční testy s testovacím tenantem provést Pokud je to možné. Další informace najdete v tématu [by měl nasazení nových zásad](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Další postup

Pokud chcete další informace o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)