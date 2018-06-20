---
title: Osvědčené postupy pro podmíněný přístup v Azure Active Directory | Microsoft Docs
description: Další informace o věcí, které byste měli vědět, a co je, že byste neměli dělat při konfiguraci zásad podmíněného přístupu.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 88e6b1a64d4ceff64c1ee74a297e7af0c5a27379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232332"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Osvědčené postupy pro podmíněný přístup v Azure Active Directory

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), jak oprávněným uživatelům přístup můžete řídit cloudových aplikací. Tento článek poskytuje informace o:

- Věcí, které byste měli vědět 
- Co je by se to při konfiguraci zásad podmíněného přístupu. 

Tento článek předpokládá, že jste obeznámeni pojmy a terminologii uvedených v [co je podmíněného přístupu v Azure Active Directory?](active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Co potřebné k tomu, aby zásada fungovat?

Když vytvoříte novou zásadu, nejsou žádné uživatele, skupiny, aplikace nebo vybrané řízení přístupu.

![Cloudové aplikace](./media/active-directory-conditional-access-best-practices/02.png)


Chcete-li vaše zásady fungovat, je nutné nakonfigurovat:


|Co           | Postupy                                  | Proč|
|:--            | :--                                  | :-- |
|**Cloudové aplikace** |Musíte vybrat jednu nebo více aplikací.  | Cílem zásad podmíněného přístupu je vám umožňují řídit způsob Autorizovaní uživatelé můžou používat cloudové aplikace.|
| **Uživatelé a skupiny** | Je nutné vybrat alespoň jeden uživatel nebo skupina, který má oprávnění pro přístup k vybrané cloudové aplikace. | Zásady podmíněného přístupu, který nemá žádné uživatele a skupiny přiřazené, je neaktivní. |
| **Řízení přístupu** | Musíte vybrat aspoň jedno přístupu řízení. | Pokud vaše podmínky jsou splněny, procesor zásad je potřeba vědět, co dělat.|




## <a name="what-you-should-know"></a>Důležité informace

### <a name="how-are-assignments-evaluated"></a>Jak se vyhodnocují přiřazení?

Všechna přiřazení jsou logicky **spojeny**. Pokud máte více než jeden přiřazení nakonfigurován, je nutné splnit všechna přiřazení k aktivaci zásadu.  

Pokud potřebujete nakonfigurovat umístění podmínku, která platí pro všechna připojení z vnějšku podnikové sítě:

- Zahrnout **všech umístění**
- Vyloučit **všechny důvěryhodné IP adresy**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co dělat, když jsou zamčené mimo portál pro správu Azure AD?

Pokud je uzamčen portálu Azure AD z důvodu nesprávné nastavení v zásadách podmíněného přístupu:

- Ověřte, zda jsou ostatními správci ve vaší organizaci, které ještě nejsou blokované. Správce s přístupem k portálu Azure můžete zakázat zásadu, která je ovlivňující přihlašovacích údajů. 

- Pokud žádná z správci ve vaší organizaci můžete aktualizovat zásady, budete muset odeslat žádost o podporu. Podporu společnosti Microsoft můžete zkontrolovat a aktualizovat zásady podmíněného přístupu, které brání přístupu.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co se stane, pokud máte v portálu Azure classic a portálu Azure, které jsou nakonfigurované zásady?  

Obě zásady se vynucují službou Azure Active Directory a uživatel získá přístup jenom v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co se stane, pokud máte zásady v portálu Intune Silverlight a portálu Azure?

Obě zásady se vynucují službou Azure Active Directory a uživatel získá přístup jenom v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co se stane, když mám několik zásad pro stejného uživatele nakonfigurované?  

Pro každé přihlášení Azure Active Directory vyhodnotí všechny zásady a zajistí, že jsou splněny všechny požadavky před udělil přístup pro uživatele.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Podmíněný přístup funguje s Exchange ActiveSync?

Ano, pomocí protokolu Exchange ActiveSync v zásadách podmíněného přístupu.






## <a name="what-you-should-avoid-doing"></a>Co byste neměli dělat

Rozhraní framework podmíněného přístupu vám poskytne skvělé konfigurace flexibilitu. Ale flexibilitu také znamená, že byste měli pečlivě zkontrolovat každou zásadu konfigurace před uvolněním, aby se zabránilo nežádoucí výsledky. V tomto kontextu, měli byste věnovat zvláštní pozornost, jako by to ovlivnilo kompletní sady přiřazení **všichni uživatelé / skupiny / cloudových aplikací**.

Ve vašem prostředí neměli byste následující konfigurace:


**Pro všechny uživatele všech cloudových aplikací:**

- **Blokovat přístup** – tato konfigurace zablokuje celé organizace, která není výborný vhodné.

- **Vyžaduje kompatibilní zařízení** – pro uživatele, který nezaregistrovali svoje zařízení, ale tato zásada blokuje veškerý přístup, včetně přístupu k portálu služby Intune. Pokud jste správce bez registrovaných zařízení, zablokuje vás tyto zásady z získali zpět do portálu Azure, změna zásad.

- **Vyžadovat připojení k doméně** – tento blok zásad přístupu se taky může blokovat přístup pro všechny uživatele ve vaší organizaci, pokud ještě nemáte zařízení připojených k doméně.


**Pro všechny uživatele, všechny cloudové aplikace, všechny platformy zařízení:**

- **Blokovat přístup** – tato konfigurace zablokuje celé organizace, která není výborný vhodné.


## <a name="how-should-you-deploy-a-new-policy"></a>Jak se nasadit nové zásady?

Jako první krok, by měly vyhodnotit zásadu pomocí [co když nástroj](active-directory-conditional-access-whatif.md).

Jakmile budete připraveni k nasazení nových zásad do vašeho prostředí, měli byste to provést ve fázích:

1. Použít zásady pro malou skupinu uživatelů a ověřte, zda že se chová podle očekávání. 

2.  Když rozšiřujete zásadu, která obsahovala další uživatele, i nadále vyloučit ze zásad všechny správce. To zajišťuje, že správci stále mít přístup a můžete aktualizovat zásadu, pokud se vyžaduje změnu.

3. Použít zásady pro všechny uživatele pouze, pokud je to skutečně nutné. 

Jako nejlepší postup vytvořte uživatelský účet, který je:

- Vyhrazený pro správu zásad 
- Vyloučen ze všech zásad


## <a name="policy-migration"></a>Migrace zásad

Vezměte v úvahu migrace zásad, které jste dosud nevytvořili na portálu Azure, protože:

- Scénáře, které nelze zpracovat před můžete vyřešit.

- Můžete snížit počet zásad, které budete muset spravovat konsolidovat.   

- Můžete spravovat všechny zásady podmíněného přístupu v jednom centrálním místě.

- Portál Azure classic byl vyřazen.   


Další informace najdete v tématu [migraci na portálu Azure classic zásad](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).
