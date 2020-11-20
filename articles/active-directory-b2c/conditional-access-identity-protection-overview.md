---
title: Identity Protection a podmíněný přístup v Azure AD B2C
description: Přečtěte si, jak Identity Protection poskytuje přehled o rizikových přihlášeních a detekcích rizik. Zjistěte, jak a podmíněný přístup umožňuje vyhovět zásadám organizace na základě rizikových událostí ve vašich Azure AD B2Cch klientech.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4a71740c487896208a3da7bf35bb39899c56937
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952059"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection a podmíněný přístup pro Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Vylepšete zabezpečení Azure Active Directory B2C (Azure AD B2C) pomocí Azure AD Identity Protection a podmíněného přístupu. Funkce zjišťování rizik v rámci Identity Protection, včetně rizikových uživatelů a rizikových přihlášení, se automaticky zjišťují a zobrazují ve vašem tenantovi Azure AD B2C. Můžete vytvořit zásady podmíněného přístupu, které pomocí těchto zjišťování rizik určí akce a vynutily zásady organizace. Tyto možnosti společně poskytují Azure AD B2C vlastníci aplikací větší kontrolu nad rizikovým ověřováním a zásadami přístupu.
  
Pokud jste již v Azure AD obeznámeni s [identitou identity](../active-directory/identity-protection/overview-identity-protection.md) a [podmíněným přístupem](../active-directory/conditional-access/overview.md) , budou mít tyto možnosti v Azure AD B2C známé zkušenosti s drobnými rozdíly popsanými v tomto článku.

![Podmíněný přístup v tenantovi B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Aby bylo možné používat podmíněný přístup, je požadován Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Výhody Identity Protection a podmíněného přístupu pro Azure AD B2C  

Díky párování zásad podmíněného přístupu s detekcí rizik ochrany identity můžete reagovat na rizikové ověřování pomocí příslušné akce zásad.

- **Získejte novou úroveň viditelnosti rizik ověřování pro vaše aplikace a zákaznickou základnu**. Pomocí signálů z miliard měsíčních ověření napříč účtem služby Azure AD a účtem Microsoft budou algoritmy detekce rizik nyní označovat jako nízká, střední nebo vysoké riziko pro místní ověřování uživatelů nebo občanů.
- **Automaticky řeší rizika konfigurací vlastního adaptivního ověřování**. Pro zadané aplikace můžete vyžadovat, aby konkrétní sada uživatelů poskytovala druhý faktor ověřování, jako v rámci služby Multi-Factor Authentication (MFA). Nebo můžete zablokovat přístup na základě zjištěné úrovně rizika. Stejně jako u jiných Azure AD B2C prostředí můžete přizpůsobit výsledné prostředí koncových uživatelů pomocí hlasu, stylu a značky vaší organizace. V případě, že uživatel nebude moci získat přístup, můžete také zobrazit alternativy zmírnění.
- **Řízení přístupu na základě umístění, skupin a aplikací**.Podmíněný přístup se dá použít taky k řízení situací, které se nerizikují. Můžete například vyžadovat MFA pro zákazníky, kteří přistupují ke konkrétní aplikaci, nebo zablokovat přístup ze zadaných geografických oblastí.
- **Integrace s Azure AD B2Cmi uživatelskými toky a vlastními zásadami architektury prostředí identity**. Využijte svoje stávající přizpůsobené prostředí a přidejte ovládací prvky, které potřebujete k rozhraní s podmíněným přístupem. Můžete také implementovat pokročilé scénáře pro udělení přístupu, jako je například přístup založený na znalostní bázi nebo váš vlastní upřednostňovaný poskytovatel vícefaktorového ověřování.

## <a name="feature-differences-and-limitations"></a>Rozdíly a omezení funkcí

Identity Protection a podmíněný přístup v Azure AD B2C obecně fungují stejným způsobem jako v Azure AD, a to s těmito výjimkami:

- Security Center není k dispozici v Azure AD B2C.

- V Azure AD B2Cch klientech se nepodporují služby Identity Protection a Conditional Access pro ROPC toky mezi servery.

- V Azure AD B2C tenantech jsou detekce rizik v oblasti Identity Protection dostupné jenom pro místní účty B2C, a ne pro sociální identity, jako je Google nebo Facebook.

- V Azure AD B2C tenantů je k dispozici podmnožina detekce rizik ochrany identity. Viz [nastavení Identity Protection](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- Funkce dodržování předpisů pro zařízení s podmíněným přístupem není v Azure AD B2C klientech k dispozici.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrace podmíněného přístupu s uživatelskými toky a vlastními zásadami

V Azure AD B2C můžete aktivovat podmínky podmíněného přístupu z vestavěných toků uživatelů. Podmíněný přístup můžete také začlenit do vlastních zásad. Podobně jako u dalších aspektů toku uživatele B2C je zasílání zpráv s činnostmi koncových uživatelů možné přizpůsobit na základě hlasu, značky a možností zmírnění rizik ve vaší organizaci. Viz [definice technického profilu podmíněného přístupu](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Zásady podmíněného přístupu můžete spravovat také v Azure AD B2C pomocí rozhraní API Microsoft Graph. Podrobnosti najdete v dokumentaci k [podmíněnému přístupu](../active-directory/conditional-access/overview.md) a v [referenčním Microsoft Graph](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md).

## <a name="next-steps"></a>Další kroky

- [Nastavení Identity Protection a podmíněného přístupu pro Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Další informace o identitě identity v Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Další informace o podmíněném přístupu](../active-directory/conditional-access/overview.md)