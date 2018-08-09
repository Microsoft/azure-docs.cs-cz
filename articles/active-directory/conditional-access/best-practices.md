---
title: Osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory | Dokumentace Microsoftu
description: Další informace o co byste měli vědět a co to je, že při konfiguraci zásad podmíněného přístupu, měli byste se vyhnout tím.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d21a6dc7a460e07fe7530b58bef887241a694b25
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628082"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory

S [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), jak ověřeného přístupu uživatele můžete řídit vašich cloudových aplikacích. Tento článek poskytuje informace o:

- Co byste měli vědět 
- Co je byste se měli vyhnout postupu při konfiguraci zásad podmíněného přístupu. 

Tento článek předpokládá, že jste obeznámeni s koncepty a terminologie uvedených v [co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Co potřebná k tomu, aby zásada fungovat?

Když vytvoříte novou zásadu, nejsou žádní uživatelé, skupiny, aplikace ani vybraných ovládacích prvků přístupu.

![Cloudové aplikace](./media/best-practices/02.png)


Aby vaše zásady fungují, je nutné nakonfigurovat:


|Co           | Jak                                  | Proč|
|:--            | :--                                  | :-- |
|**Cloudové aplikace** |Musíte vybrat jednu nebo víc aplikací.  | Cílem zásad podmíněného přístupu je vám umožňují řídit jak Autorizovaní uživatelé můžou používat cloudové aplikace.|
| **Uživatelé a skupiny** | Musíte vybrat alespoň jeden uživatel nebo skupina, která má oprávnění pro přístup k vybrané cloudové aplikace. | Zásady podmíněného přístupu, který nemá žádné uživatelé a skupiny přiřazení, je neaktivní. |
| **Řízení přístupu** | Musíte vybrat aspoň jedno přístupu ovládací prvek. | Jestli vaše podmínky splněny, procesor zásad je potřeba vědět, co dělat.|




## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="how-are-assignments-evaluated"></a>Jak se vyhodnocují přiřazení?

Všechna přiřazení jsou logicky **spojeny**. Pokud máte více než jedno přiřazení nakonfigurovaná všechna přiřazení musí být splněny pro aktivaci zásad.  

Pokud je potřeba nakonfigurovat umístění podmínky, která platí pro všechna připojení z mimo síť ve vaší organizaci:

- Zahrnout **všechna místa**
- Vyloučit **všechny důvěryhodné IP adresy**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co dělat, když jsou zamčené z portálu pro správu Azure AD?

Pokud jsou zablokována na portálu Azure AD kvůli nesprávnému nastavení zásad podmíněného přístupu:

- Ověřte, zda jsou ostatními správci ve vaší organizaci, které ještě nejsou blokované. Správce s přístupem k webu Azure portal můžete zakázat zásadu, která je dopadu na vaše přihlášení. 

- Pokud žádná z správci ve vaší organizaci můžete aktualizovat zásady, musíte odeslat žádost o podporu. Podpora společnosti Microsoft můžete zkontrolovat a aktualizovat zásady podmíněného přístupu, které brání přístupu.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co se stane, když máte na portálu Azure classic a webu Azure portal nakonfigurovat zásady?  

Obě zásady vynucují v Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co se stane, když máte zásady na portálu Silverlight pro Intune a na webu Azure portal?

Obě zásady vynucují v Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co se stane, když mám víc zásad pro stejného uživatele nakonfigurovaná?  

Pro každé přihlášení Azure Active Directory vyhodnotí všechny zásady a zajistí, že jsou splněny všechny požadavky před udělil přístup pro uživatele.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Podmíněný přístup funguje s Exchange ActiveSync?

Ano, pomocí protokolu Exchange ActiveSync v zásadách podmíněného přístupu.






## <a name="what-you-should-avoid-doing"></a>Co byste se měli vyhnout tím

Rozhraní podmíněný přístup vám poskytuje flexibilitu skvělé konfigurace. Velkou flexibilitu, ale také znamená, že byste měli pečlivě zkontrolovat každou zásadu konfigurace před uvolněním, abyste se vyhnuli dalším nežádoucí výsledky. V tomto kontextu, musí věnovat zvláštní pozornost přiřazení, jako by to ovlivnilo kompletní sady **všech uživatelů / skupin / cloudové aplikace**.

Ve vašem prostředí měli byste se vyhnout následující konfigurace:


**Pro všechny uživatele všechny cloudové aplikace:**

- **Blokovat přístup** – tato konfigurace zablokuje celou organizaci, které určitě není vhodné.

- **Vyžadovat vyhovující zařízení** – pro uživatele, který nezaregistrovali svoje zařízení, ale tato zásada blokuje veškerý přístup, včetně přístupu k portálu Intune. Pokud jste správce bez registrovaných zařízení, zablokuje vás tato zásada získat zpět na web Azure Portal ke změně zásady.

- **Vyžadovat připojení k doméně** – tento blok zásad přístupu má také potenciál k blokování přístupu pro všechny uživatele ve vaší organizaci, pokud ještě nemáte zařízení připojených k doméně.


**Pro všechny uživatele, všechny cloudové aplikace, všechny platformy zařízení:**

- **Blokovat přístup** – tato konfigurace zablokuje celou organizaci, které určitě není vhodné.


## <a name="how-should-you-deploy-a-new-policy"></a>Jak můžete nasadit nové zásady?

Jako první krok, by se měl vyhodnotit zásadu pomocí [co když nástroj](what-if-tool.md).

Jakmile budete připraveni k nasazení nových zásad do vašeho prostředí, byste měli udělat ve fázích:

1. Použít zásady pro malou skupinu uživatelů a ověřte, že se chová podle očekávání. 

2.  Když rozšiřujete zásadu, která zahrnují další uživatele, i nadále vyloučit ze zásad všechny správce. Tím se zajistí, že správci stále máte přístup a zásady můžete aktualizovat, pokud je požadována změna.

3. Zásady platí pro všechny uživatele, pouze v případě, že je to opravdu nutné. 

Jako osvědčený postup vytvoření uživatelského účtu, který je:

- Vyhrazený pro správu zásad 
- Vyloučené ze všech zásad


## <a name="policy-migration"></a>Migrace zásad

Vezměte v úvahu migrace zásad, které jste ještě nevytvořili na webu Azure Portal, protože:

- Nyní můžete řešit situace, které nelze zpracovat před.

- Můžete snížit počet zásad, které je nutné spravovat jejich sloučení.   

- Můžete spravovat všechny zásady podmíněného přístupu v jednom centrálním místě.

- Na portálu Azure classic je vyřazený.   


Další informace najdete v tématu [migrace klasických zásad na webu Azure Portal](policy-migration.md).


## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).
