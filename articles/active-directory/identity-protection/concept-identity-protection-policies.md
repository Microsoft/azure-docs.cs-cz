---
title: Zásady ochrany identitazure AD
description: Identifikace tří zásad, které jsou povoleny pomocí ochrany identity
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887413"
---
# <a name="identity-protection-policies"></a>Zásad ochrany identit

Ochrana identity služby Azure Active Directory obsahuje tři výchozí zásady, které mohou správci povolit. Tyto zásady zahrnují omezené přizpůsobení, ale platí pro většinu organizací. Všechny zásady umožňují vyloučit uživatele, jako je [například nouzový přístup nebo účty správce sklu](../users-groups-roles/directory-emergency-access.md).

![Zásad ochrany identit](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Zásady registrace Azure MFA

Ochrana identity může organizacím pomoci zavést Azure Multi-Factor Authentication (MFA) pomocí zásad podmíněného přístupu, které vyžadují registraci při přihlášení. Povolení této zásady je skvělý způsob, jak zajistit, aby noví uživatelé ve vaší organizaci zaregistrovali vícefaktorové povolení první den. Vícefaktorové ověřování je jednou z metod samonápravy pro rizikové události v rámci ochrany identity. Vlastní náprava umožňuje uživatelům provést vlastní akci, aby snížili objem volání helpdesku.

Další informace o azure multi-factor authentication najdete v článku [Jak to funguje: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Zásady rizik pro přihlášení

Ochrana identity analyzuje signály z každého přihlášení, v reálném čase i offline, a vypočítá skóre rizika na základě pravděpodobnosti, že přihlášení nebylo provedeno uživatelem. Správci mohou učinit rozhodnutí na základě tohoto signálu skóre rizika k vynucení požadavků organizace. Správci mohou blokovat přístup, povolit přístup nebo povolit přístup, ale vyžadují vícefaktorové ověřování.

Pokud je zjištěno riziko, uživatelé mohou provádět vícefaktorové ověřování pro vlastní nápravu a zavření rizikové události přihlášení, aby se zabránilo zbytečnému šumu pro správce.

> [!NOTE] 
> Uživatelé musí mít dříve registrované pro Azure Vícefaktorové ověřování před aktivací zásady rizik přihlášení.

### <a name="custom-conditional-access-policy"></a>Zásady vlastního podmíněného přístupu

Správci mohou také vytvořit vlastní zásady podmíněného přístupu, včetně rizika přihlášení jako podmínky přiřazení. Další informace o podmíněném přístupu naleznete v článku [Co je podmíněný přístup?](../conditional-access/overview.md)

![Zásady rizika přihlášení k vlastnímu podmíněnému přístupu](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Zásady rizika pro uživatele

Ochrana identity může vypočítat, co je podle ní normální pro chování uživatele a použít je k základu rozhodnutí pro jejich riziko. Uživatelské riziko je výpočet pravděpodobnosti, že identita byla ohrožena. Správci mohou učinit rozhodnutí na základě tohoto signálu skóre rizika k vynucení požadavků organizace. Správci mohou blokovat přístup, povolit přístup nebo povolit přístup, ale vyžadují změnu hesla pomocí [samoobslužné resetování hesla služby Azure AD](../authentication/howto-sspr-deployment.md).

Pokud je zjištěno riziko, mohou uživatelé provést samoobslužné resetování hesla na vlastní nápravu a uzavřít událost rizika uživatele, aby se zabránilo zbytečnému šumu pro správce.

> [!NOTE] 
> Uživatelé musí mít dříve registrované pro samoobslužné resetování hesla před aktivací zásady rizika uživatele.

## <a name="next-steps"></a>Další kroky

- [Povolení samoobslužného resetování hesla azure ad](../authentication/howto-sspr-deployment.md)

- [Povolení služby Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Povolení zásad registrace azure vícefaktorového ověřování](howto-identity-protection-configure-mfa-policy.md)

- [Povolení zásad přihlášení a rizik uživatelů](howto-identity-protection-configure-risk-policies.md)
