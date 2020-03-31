---
title: Požadavky na řízení přístupu návrhu hybridní identity Azure | Dokumenty společnosti Microsoft
description: Zahrnuje pilíře identity a identifikaci požadavků na přístup pro prostředky pro uživatele v hybridním prostředí.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60295139"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na řízení přístupu pro řešení hybridní identity
Když organizace navrhuje své řešení hybridní identity, mohou také tuto příležitost využít ke kontrole požadavků na přístup pro prostředky, které plánují zpřístupnit uživatelům. Přístup k datům překračuje všechny čtyři pilíře identity, které jsou:

* Správa
* Ověřování
* Autorizace
* Auditování

Následující části budou zahrnovat ověřování a autorizaci podrobněji, správa a auditování jsou součástí životního cyklu hybridní identity. Další informace o těchto funkcích naleznete [v části Určení úloh správy hybridní identity.](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

> [!NOTE]
> Přečtěte si [čtyři pilíře identity - Správa identit ve věku hybridního IT](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) pro více informací o každém z těchto pilířů.
> 
> 

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
Existují různé scénáře pro ověřování a autorizaci, tyto scénáře budou mít specifické požadavky, které musí splňovat řešení hybridní identity, které společnost přijme. Scénáře zahrnující komunikaci mezi podniky (B2B) mohou přidat další výzvu pro správce IT, protože budou muset zajistit, aby metoda ověřování a autorizace používaná organizací mohla komunikovat se svými obchodními partnery. Během procesu navrhování požadavků na ověřování a autorizaci zajistěte, aby byly zodpovězeny následující otázky:

* Ověří a autorizuje vaše organizace pouze uživatele, kteří se nacházejí v jejich systému správy identit?
  * Existují nějaké plány pro scénáře B2B?
  * Pokud ano, už víte, které protokoly (SAML, OAuth, Kerberos nebo Certifikáty) budou použity k připojení obou firem?
* Podporuje řešení hybridní identity, které chcete přijmout, tyto protokoly?

Dalším důležitým bodem, který je třeba zvážit, je, kde bude umístěno úložiště ověřování, které budou uživatelé a partneři používat, a model správy, který se má použít. Zvažte následující dvě základní možnosti:

* Centralizované: v tomto modelu lze přihlašovací údaje, zásady a správu uživatele centralizovat místně nebo v cloudu.
* Hybridní: v tomto modelu budou pověření, zásady a správa uživatele centralizované místně a replikovány v cloudu.

Model, který vaše organizace přijme, se bude lišit v závislosti na jejich obchodních požadavcích, chcete odpovědět na následující otázky, abyste zjistili, kde se bude systém správy identit nabývat, a režim správy, který chcete použít:

* Má vaše organizace aktuálně místní správu identit?
  * Pokud ano, mají v plánu si ho nechat?
  * Existují nějaké požadavky na regulaci nebo dodržování předpisů, které musí vaše organizace dodržovat a které určují, kde by měl být systém správy identit?
* Používá vaše organizace jednotné přihlašování pro aplikace umístěné místně nebo v cloudu?
  * Pokud ano, má přijetí modelu hybridní identity vliv na tento proces?

## <a name="access-control"></a>Řízení přístupu
Zatímco ověřování a autorizace jsou základními prvky umožňujícími přístup k podnikovým datům prostřednictvím ověření uživatele, je také důležité řídit úroveň přístupu, kterou budou mít tito uživatelé, a úroveň přístupu, kterou budou mít správci nad prostředky. které řídí. Vaše řešení hybridní identity musí být schopno poskytnout podrobný přístup k prostředkům, delegování a řízení přístupu na základě role. Ujistěte se, že je zodpovězena následující otázka týkající se řízení přístupu:

* Má vaše společnost ke správě systému identit více než jednoho uživatele se zvýšenými oprávněními?
  * Pokud ano, potřebuje každý uživatel stejnou úroveň přístupu?
* Bude vaše společnost muset delegovat přístup k uživatelům ke správě konkrétních prostředků?
  * Pokud ano, jak často se to stane?
* Bude vaše společnost potřebovat integrovat možnosti řízení přístupu mezi místními a cloudovými prostředky?
* Bude vaše společnost muset omezit přístup ke zdrojům v souladu s určitými podmínkami?
* Bude mít vaše společnost nějakou aplikaci, která potřebuje vlastní řízení přístup k některým prostředkům?
  * Pokud ano, kde se tyto aplikace nacházejí (místně nebo v cloudu)?
  * Pokud ano, kde se tyto cílové prostředky nacházejí (místně nebo v cloudu)?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Definovat strategii ochrany osobních údajů](plan-hybrid-identity-design-considerations-data-protection-strategy.md) půjde přes možnosti k dispozici a výhody / nevýhody každé možnosti.  Zodpovězením těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

