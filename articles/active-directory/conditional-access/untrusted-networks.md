---
title: Vyžadovat MFA z nedůvěryhodných sítí – Azure Active Directory
description: Naučte se konfigurovat zásady podmíněného přístupu v Azure Active Directory (Azure AD) pro pokusy o přístup z nedůvěryhodných sítí.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077672"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Postupy: vyžadování MFA pro přístup z nedůvěryhodných sítí s podmíněným přístupem   

Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k zařízením, aplikacím a službám odkudkoli. Vaši uživatelé mají přístup k vašim cloudovým aplikacím nejen ze sítě vaší organizace, ale také z jakéhokoli nedůvěryhodného umístění v Internetu. Běžným postupem přístupu z nedůvěryhodných sítí je vyžadovat službu Multi-Factor Authentication (MFA).

Tento článek obsahuje informace, které potřebujete ke konfiguraci zásad podmíněného přístupu, které vyžadují MFA pro přístup z nedůvěryhodných sítí. 

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte zkušenosti se [základními koncepty](overview.md) podmíněného přístupu. 

## <a name="scenario-description"></a>Popis scénáře

Abyste mohli vyvážit rovnováhu mezi zabezpečením a produktivitou, stačí, když budete vyžadovat jenom heslo pro přihlášení ze sítě vaší organizace. Nicméně pro přístup z nedůvěryhodného umístění v síti existuje zvýšené riziko, že se přihlášení legitimních uživatelů neprovede. Chcete-li tento problém vyřešit, můžete zablokovat přístup z nedůvěryhodných sítí. Alternativně můžete také vyžadovat vícefaktorové ověřování (MFA) a získat další jistotu, že byl proveden pokus legitimním vlastníkem účtu. 

Pomocí podmíněného přístupu Azure AD můžete tento požadavek vyřešit pomocí jediné zásady, která udělí přístup: 

- Pro vybrané cloudové aplikace
- Pro vybrané uživatele a skupiny  
- Vyžadování Multi-Factor Authentication 
- Kdy přístup pochází: 
   - Nedůvěryhodné umístění

## <a name="implementation"></a>Implementace

Výzvou k tomuto scénáři je přeložit *přístup z nedůvěryhodného síťového umístění* do podmínky podmíněného přístupu. V zásadách podmíněného přístupu můžete nakonfigurovat [podmínku umístění](location-condition.md) pro řešení scénářů, které se vztahují k umístěním v síti. Podmínka umístění umožňuje vybrat pojmenovaná umístění, což jsou logické skupiny rozsahů IP adres, zemí a oblastí.  

Vaše organizace obvykle vlastní jeden nebo více rozsahů adres, například 199.30.16.0-199.30.16.15.
Pojmenované umístění můžete nakonfigurovat pomocí:

- Zadání tohoto rozsahu (199.30.16.0/28) 
- Přiřazení popisného názvu, jako je **podniková síť** 

Místo toho, abyste se pokoušeli definovat, která všechna umístění nejsou důvěryhodná, můžete:

- Zahrnout libovolné umístění 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Snímek obrazovky s podoknem umístění Azure a D s možností konfigurovat nastavenou na hodnotu Ano, zobrazit kartu zahrnout a vybranou možnost Libovolná poloha a zvýrazněná." border="false":::

- Vyloučit všechna důvěryhodná umístění 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Snímek obrazovky s podoknem umístění Azure a D s možností konfigurovat nastavenou na hodnotu Ano, zobrazit kartu zahrnout a vybranou možnost Libovolná poloha a zvýrazněná." border="false":::

## <a name="policy-deployment"></a>Nasazení zásad

S přístupem popsaným v tomto článku teď můžete nakonfigurovat zásady podmíněného přístupu pro nedůvěryhodná umístění. Abyste se ujistili, že vaše zásada funguje podle očekávání, doporučuje se před jejich vyzkoušením do produkčního prostředí otestovat. V ideálním případě použijte testovacího tenanta a ověřte, zda vaše nové zásady fungují tak, jak mají.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](./overview.md)
