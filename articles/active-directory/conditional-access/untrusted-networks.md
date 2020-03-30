---
title: Vyžadovat vícefaktorové informace z nedůvěryhodných sítí – Azure Active Directory
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu ve službě Azure Active Directory (Azure AD) pro pokusy o přístup z nedůvěryhodných sítí.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379990"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Postup: Vyžadovat vícefaktorové povolení pro přístup z nedůvěryhodných sítí s podmíněným přístupem   

Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízením, aplikacím a službám odkudkoli. Vaši uživatelé mohou přistupovat k cloudovým aplikacím nejen ze sítě vaší organizace, ale také z jakéhokoli nedůvěryhodného umístění na internetu. Běžným osvědčeným postupem pro přístup z nedůvěryhodných sítí je vyžadovat vícefaktorové ověřování (MFA).

Tento článek poskytuje informace, které potřebujete ke konfiguraci zásady podmíněného přístupu, který vyžaduje vícefaktorové řízení pro přístup z nedůvěryhodných sítí. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s: 

- [Základní koncepty](overview.md) podmíněného přístupu Azure AD 
- [Doporučené postupy](best-practices.md) pro konfiguraci zásad podmíněného přístupu na webu Azure Portal

## <a name="scenario-description"></a>Popis scénáře

Chcete-li zvládnout rovnováhu mezi zabezpečením a produktivitou, může být dostačující, abyste vyžadovali pouze heslo pro přihlášení ze sítě vaší organizace. Pro přístup z nedůvěryhodného síťového umístění však existuje zvýšené riziko, že přihlášení nejsou prováděny legitimními uživateli. Chcete-li tento problém vyřešit, můžete blokovat přístup z nedůvěryhodných sítí. Případně můžete také vyžadovat vícefaktorové ověřování (MFA) získat zpět další ujištění, že pokus byl proveden legitimní mstinčiúčtu. 

S podmíněným přístupem Azure AD můžete tento požadavek řešit pomocí jediné zásady, která uděluje přístup: 

- K vybraným cloudovým aplikacím
- Pro vybrané uživatele a skupiny  
- Vyžadování vícefaktorového ověřování 
- Pokud přístup pochází z: 
   - Umístění, které není důvěryhodné

## <a name="implementation"></a>Implementace

Výzvou tohoto scénáře je přeložit *přístup z nedůvěryhodného síťového umístění* do podmínky podmíněného přístupu. V zásadách podmíněného přístupu můžete nakonfigurovat [podmínku umístění](location-condition.md) tak, aby řešila scénáře související se síťovými umístěními. Podmínka umístění umožňuje vybrat pojmenované umístění, což jsou logická seskupení rozsahů IP adres, zemí a oblastí.  

Vaše organizace obvykle vlastní jeden nebo více rozsahů adres, například 199.30.16.0 - 199.30.16.15.
Pojmenované umístění můžete nakonfigurovat takto:

- Určení tohoto rozsahu (199.30.16.0/28) 
- Přiřazení popisného názvu, například **podnikové sítě** 

Místo toho, abyste se pokoušeli definovat, která umístění jsou nedůvěryhodná, můžete:

- Zahrnout libovolné místo 

   ![Podmíněný přístup](./media/untrusted-networks/02.png)

- Vyloučit všechna důvěryhodná umístění 

   ![Podmíněný přístup](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Nasazení zásad

S přístupem popsaným v tomto článku můžete nyní nakonfigurovat zásady podmíněného přístupu pro nedůvěryhodná umístění. Chcete-li se ujistit, že vaše zásady fungují podle očekávání, doporučujeme osvědčeným postupem otestovat před jejich zavedením do produkčního prostředí. V ideálním případě použijte testovacího klienta k ověření, zda vaše nové zásady fungují tak, jak bylo zamýšleno. Další informace naleznete [v tématu Jak nasadit novou zásadu](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o podmíněném přístupu, přečtěte si informace [o podmíněném přístupu ve službě Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
