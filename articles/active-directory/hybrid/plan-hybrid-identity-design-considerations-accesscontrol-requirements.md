---
title: Požadavky na řízení přístupu k návrhu hybridní identity Azure | Microsoft Docs
description: Pokrývá pilíře identity a identifikaci požadavků na přístup k prostředkům pro uživatele v hybridním prostředí.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "60295139"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na řízení přístupu pro vaše řešení hybridní identity
Když organizace navrhuje své řešení hybridní identity, může také využít tuto příležitost ke kontrole požadavků na přístup k prostředkům, které plánuje, aby je uživatelé mohli zpřístupnit. Přístup k datům překračuje všechny čtyři pilíře identity, které jsou:

* Správa
* Authentication
* Autorizace
* Auditování

Následující části budou zahrnovat ověřování a autorizaci ve více podrobnostech, správě a auditování jsou součástí životního cyklu hybridní identity. Další informace o těchto možnostech najdete v tématu [určení úloh správy hybridních identit](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) .

> [!NOTE]
> Přečtěte si [čtyři pilíře správy identity-identity ve stáří hybridního IT,](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) kde najdete další informace o každém z těchto pilířů.
> 
> 

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Existují různé scénáře pro ověřování a autorizaci. Tyto scénáře budou mít specifické požadavky, které musí splnit řešení hybridní identity, které společnost hodlá přijmout. Scénáře týkající se komunikace B2B (Business to Business) můžou přidat další výzvu pro správce IT, protože budou muset zajistit, aby metoda ověřování a autorizace používaná organizací mohla komunikovat se svými obchodními partnery. Během navrhování pro požadavky na ověřování a autorizaci se ujistěte, že máte zodpovězené následující otázky:

* Bude vaše organizace ověřovat a autorizovat jenom uživatele, kteří se nacházejí v jejich systému správy identit?
  * Existují nějaké plány pro scénáře B2B?
  * Pokud ano, už víte, které protokoly (SAML, OAuth, Kerberos nebo certifikáty) se budou používat k propojení obou firem?
* Bude řešení hybridní identity, které hodláte přijmout, podporovat tyto protokoly?

Dalším důležitým bodem, který je třeba zvážit, je místo, kde se nachází úložiště ověřování, které budou používat uživatelé a partneři, a model správy, který se má použít. Vezměte v úvahu následující dvě základní možnosti:

* Centralizované: v tomto modelu mohou být přihlašovací údaje, zásady a Správa uživatele centralizované místně nebo v cloudu.
* Hybridní: v tomto modelu budou přihlašovací údaje, zásady a Správa uživatele centralizované místně a replikované v cloudu.

Model, který bude vaše organizace přijmout, se bude lišit v závislosti na jejich obchodních požadavcích, a chcete zodpovědět následující otázky, které vám pomůžou určit, kde se bude systém správy identit nacházet, a režim správy, který se má použít:

* Má vaše organizace aktuálně místní správu identit?
  * Pokud ano, naplánujte si je?
  * Existují nějaké požadavky na předpisy nebo požadavky na dodržování předpisů, které vaše organizace musí dodržovat, kde se má nacházet systém správy identit?
* Používá vaše organizace jednotné přihlašování pro aplikace umístěné místně nebo v cloudu?
  * Pokud ano, bude se tento proces týkat i přijetí modelu hybridních identit?

## <a name="access-control"></a>Řízení přístupu
Zatímco ověřování a autorizace jsou základními prvky, které umožňují přístup k podnikovým datům prostřednictvím ověření uživatele, je také důležité řídit úroveň přístupu, kterou tito uživatelé budou mít, a úroveň přístupu správců budou mít prostředky, které spravují. Vaše řešení hybridní identity musí být schopné poskytnout podrobný přístup k prostředkům, delegování a základnímu řízení přístupu role. Ujistěte se, že je na řízení přístupu zodpovězená následující otázka:

* Má vaše společnost více než jednoho uživatele se zvýšenými oprávněními ke správě vašeho systému identity?
  * Pokud ano, potřebuje každý uživatel stejnou úroveň přístupu?
* Potřebuje vaše společnost pro správu konkrétních prostředků delegovat přístup pro uživatele?
  * Pokud ano, jak často k tomu dochází?
* Potřebuje vaše společnost integrovat možnosti řízení přístupu mezi místními a cloudovým prostředky?
* Potřebuje vaše společnost omezit přístup k prostředkům podle určitých podmínek?
* Má vaše společnost nějakou aplikaci, která potřebuje přístup k některým prostředkům vlastním ovládacím prvkům?
  * Pokud ano, kde se nacházejí tyto aplikace (místně nebo v cloudu)?
  * Pokud ano, kde se nacházejí tyto cílové prostředky (místně nebo v cloudu)?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) překročí dostupné možnosti a výhody a nevýhody jednotlivých možností.  Tím, že odpovíte na tyto otázky, vyberete, která možnost bude nejlépe vyhovovat vašim potřebám vaší firmy.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)

