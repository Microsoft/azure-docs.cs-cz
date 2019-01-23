---
title: Jak vyžadovat vícefaktorové ověřování (MFA) pro přístup z nedůvěryhodných sítích s podmíněným přístupem Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu v Azure Active Directory (Azure AD) k pro pokusy o přístup z nedůvěryhodných sítích.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 7ffb3bfb4985f56e7e2e81a2a6d08a6ff7469fdb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445401"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Postup: Vyžadovat vícefaktorové ověřování pro přístup z nedůvěryhodných sítích s podmíněným přístupem   

Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízení, aplikacím a službám z libovolného místa. Vaši uživatelé můžete přístup k vašim cloudovým aplikacím pouze z vaší podnikové síti, ale také z libovolného místa a nedůvěryhodné sítě Internet. Běžné osvědčeným postupem pro přístup z nedůvěryhodných sítích je vyžadovat vícefaktorové ověřování (MFA).

Tento článek obsahuje informace, na kterých je nutné nakonfigurovat zásady podmíněného přístupu, která vyžaduje vícefaktorové ověřování pro přístup z nedůvěryhodných sítích. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s: 

- [Základní koncepty](overview.md) podmíněného přístupu Azure AD 
- [Osvědčené postupy](best-practices.md) ke konfiguraci zásad podmíněného přístupu na webu Azure Portal



## <a name="scenario-description"></a>Popis scénáře

Do hlavní větve rovnováhu mezi zabezpečením a produktivitu, může být dostatečné pro vás pouze vyžadovat heslo pro přihlášení v síti vaší organizace. Ale pro přístup z umístění nedůvěryhodné sítě, je zvýšené riziko, že přihlášení nejsou prováděné oprávněným uživatelům. Jak tuto situaci řešit, vám umožní blokovat přístup z nedůvěryhodných sítích. Alternativně můžete také vyžadovat vícefaktorové ověřování (MFA) a získat zpět další záruku, že byl proveden pokus o oprávněný vlastníkem účtu. 

Pomocí podmíněného přístupu Azure AD abyste mohli vyřešit tento požadavek se jedna zásada, která uděluje přístup: 

- Pro vybrané cloudové aplikace

- Pro vybrané uživatele a skupiny  

- Vyžadování vícefaktorového ověřování 

- Když je přístup pochází: 

    - Umístění, která není důvěryhodná


## <a name="implementation"></a>Implementace

Výzvy v tomto scénáři je převod *přístup z umístění nedůvěryhodné síti* do podmínka podmíněného přístupu. V zásadách podmíněného přístupu, můžete nakonfigurovat [podmínka umístění](location-condition.md) k řešení scénářů, které se vztahují k umístění v síti. Podmínka umístění vám umožní vybrat pojmenovaná umístění, které jsou logickými seskupeními rozsahy adres IP, zemí a oblastí.  

Obvykle vaše organizace vlastní jeden nebo více rozsahů adres, například 199.30.16.0 - 199.30.16.24.
Můžete nakonfigurovat pojmenovaných umístění podle:

- Zadání tohoto rozsahu (199.30.16.0/24) 

- Přiřazení popisný název, jako **podnikové sítě** 


Namísto pokusu o definovat, jaké jsou všechna místa, které nejsou důvěryhodné, můžete:

- Zahrnout všechny umístění 

    ![Podmíněný přístup](./media/untrusted-networks/02.png)

- Vyloučit všechny důvěryhodné umístění 

    ![Podmíněný přístup](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Nasazení zásad

S přístupem uvedených v tomto článku teď můžete nakonfigurovat zásady podmíněného přístupu pro nedůvěryhodných umístěních. Pokud chcete mít jistotu, že vaše zásady funguje podle očekávání, doporučené osvědčeným postupem je otestovat před zavedením do produkčního prostředí. V ideálním případě ověřte, zda vaše nová zásada funguje očekávaným způsobem pomocí testovacího tenanta. Další informace najdete v tématu [způsob nasazení nových zásad](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Další postup

Pokud chcete další informace o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)