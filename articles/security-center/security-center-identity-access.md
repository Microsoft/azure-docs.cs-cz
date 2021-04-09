---
title: Doporučení zabezpečení Azure Security Center pro MFA
description: Naučte se vymáhat vícefaktorové ověřování pro vaše předplatná Azure pomocí Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631893"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Správa vynucování MFA (Multi-Factor Authentication) u předplatných

Pokud používáte jenom hesla k ověřování uživatelů, opouštíte si, že máte otevřený vektor útoku. Uživatelé často používají slabá hesla nebo je znovu používají pro více služeb. Když je povolený [vícefaktorové ověřování](https://www.microsoft.com/security/business/identity/mfa) , vaše účty jsou bezpečnější a uživatelé se můžou i nadále ověřovat pro skoro jakoukoli aplikaci s jednotným přihlašováním (SSO).

Existuje několik způsobů, jak povolit MFA pro uživatele Azure Active Directory (AD) na základě licencí, které vaše organizace vlastní. Tato stránka poskytuje podrobné informace o každém z nich v kontextu Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA a Security Center 

Security Center umístí vysokou hodnotu pro MFA. Řízení bezpečnosti, které přispívá k vašemu zabezpečenému skóre, je **povolené MFA**. 

Doporučení v ovládacím prvku povolit MFA zajistí, že budete splňovat Doporučené postupy pro uživatele vašich předplatných:

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA

Existují tři způsoby, jak povolit MFA a splňovat dvě doporučení v Security Center: výchozí nastavení zabezpečení, přiřazení podle uživatele, zásady podmíněného přístupu (CA). Každá z těchto možností je vysvětlena níže.

### <a name="free-option---security-defaults"></a>Možnost Free – výchozí hodnoty zabezpečení
Pokud používáte bezplatnou edici Azure AD, použijte [výchozí nastavení zabezpečení](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) pro povolení služby Multi-Factor Authentication ve vašem tenantovi.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA pro zákazníky Microsoft 365 Business, E3 nebo E5
Zákazníci s Microsoft 365 můžou používat **přiřazování jednotlivých uživatelů**. V tomto scénáři je Azure AD MFA buď povolený, nebo zakázaný pro všechny uživatele, a to u všech přihlašovacích událostí. Není možné povolit službu Multi-Factor Authentication pro podmnožinu uživatelů nebo v určitých situacích a spravovat je prostřednictvím portálu Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA pro zákazníky Azure AD Premium
Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 pro možnosti **zásad podmíněného přístupu (CA)** . Pokud chcete nakonfigurovat zásady certifikační autority, budete potřebovat [oprávnění klienta Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

Vaše zásada certifikační autority musí:
- vynutil MFA
- zahrnutí ID aplikace pro správu Microsoft Azure (797f4846-BA00-4fd7-ba43-dac1f8f63013) nebo všech aplikací
- nevyloučit ID aplikace pro správu Microsoft Azure

Zákazníci s **Azure AD Premium P1** můžou pomocí certifikační autority Azure AD vyzvat uživatele k ověřování službou Multi-Factor Authentication během určitých scénářů nebo událostí, aby vyhovovaly vašim obchodním požadavkům. Další licence, které zahrnují tuto funkci: Enterprise Mobility + Security E3, Microsoft 365 F1 a Microsoft 365 E3.

**Azure AD Premium P2** poskytuje nejsilnější funkce zabezpečení a vylepšené uživatelské prostředí. Tato licence přidává [podmíněný přístup na základě rizik](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) k funkcím Azure AD Premium P1. Certifikační autorita založená na rizikech přizpůsobuje vzorům vašich uživatelů a minimalizuje výzvy služby Multi-Factor Authentication. Další licence, které zahrnují tuto funkci: Enterprise Mobility + Security E5 nebo Microsoft 365 E5.

Další informace najdete v [dokumentaci k podmíněnému přístupu Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifikace účtů bez povoleného ověřování MFA (Multi-Factor Authentication)

Seznam uživatelských účtů bez povolených MFA můžete zobrazit na stránce s podrobnostmi o Security Center doporučeních nebo pomocí Azure Resource graphu.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Zobrazení účtů bez povolených MFA v Azure Portal
Na stránce s podrobnostmi o doporučení vyberte předplatné ze seznamu **zdroje** , který není v pořádku, nebo vyberte **provést akci** a seznam se zobrazí.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Zobrazení účtů bez povolených MFA pomocí Azure Resource graphu
Pokud chcete zjistit, které účty nemají povolený MFA, použijte následující dotaz na Azure Resource Graph. Dotaz vrátí všechny prostředky, které nejsou v pořádku – účty s doporučením "MFA by se měly povolit u účtů s oprávněním vlastníka v předplatném". 

1. Otevřete **Průzkumníka Azure Resource graphu**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Spouští se stránka pro doporučení pro Azure Resource Graph Explorer * *" :::

1. Zadejte následující dotaz a vyberte **Spustit dotaz**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`Vlastnost odhalí seznam ID objektů účtu pro účty, které nemají vymáhání MFA. 

    > [!NOTE]
    > Účty se zobrazují jako ID objektů, nikoli názvy účtů, aby chránily soukromí držitelů účtů.

> [!TIP]
> Alternativně můžete použít vyhodnocení metod REST API Security Center [– získat](/rest/api/securitycenter/assessments/get).


## <a name="faq---mfa-in-security-center"></a>Nejčastější dotazy – MFA v Security Center

- [Pro vymáhání MFA už používáme zásady CA. Proč pořád získáme Security Center doporučení?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [K vykonání vícefaktorového ověřování používáme nástroj vícefaktorového ověřování třetí strany. Proč pořád získáme Security Center doporučení?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Proč Security Center zobrazuje uživatelské účty bez oprávnění k předplatnému jako "vyžaduje MFA"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Vynucujeme MFA s PIM. Proč jsou účty PIM zobrazené jako nedodržující předpisy?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Můžu některé účty vyloučit nebo zrušit?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Existují nějaká omezení pro Security Center identity a ochrany přístupu?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Pro vymáhání MFA už používáme zásady CA. Proč pořád získáme Security Center doporučení?
Pokud chcete zjistit, proč se doporučení pořád generují, ověřte následující možnosti konfigurace v zásadách certifikační autority MFA:

- Účty jste zahrnuli do části **Uživatelé** v zásadách certifikační autority MFA (nebo jedné ze skupin v části **skupiny** ).
- ID aplikace pro správu Azure (797f4846-BA00-4fd7-ba43-dac1f8f63013) nebo všechny aplikace jsou uvedené v části **aplikace** v zásadách certifikační AUTORITy MFA.
- ID aplikace pro správu Azure se nevylučuje v oddílu **Apps** vaší zásady certifikační AUTORITy MFA.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>K vykonání vícefaktorového ověřování používáme nástroj vícefaktorového ověřování třetí strany. Proč pořád získáme Security Center doporučení?
Doporučení MFA Security Center nepodporují VÍCEFAKTOROVÉ nástroje třetích stran (například DUO).

Pokud jsou doporučení pro vaši organizaci nepodstatná, zvažte jejich označení jako "zmírňované", jak je popsáno v tématu [vyloučení prostředků a doporučení ze zabezpečeného skóre](exempt-resource.md). Můžete také [Zakázat doporučení](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Proč Security Center zobrazuje uživatelské účty bez oprávnění k předplatnému jako "vyžaduje MFA"?
Doporučení MFA Security Center odkazují na role [Azure RBAC](../role-based-access-control/role-definitions-list.md) a [Správce předplatného Azure Classic](../role-based-access-control/classic-administrators.md) . Ověřte, že žádný z účtů nemá některou z těchto rolí.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Vynucujeme MFA s PIM. Proč jsou účty PIM zobrazené jako nedodržující předpisy?
Security Center doporučení MFA aktuálně nepodporují účty PIM. Můžete tyto účty přidat do zásad podmíněného přístupu do části Uživatelé nebo Skupiny.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Můžu některé účty vyloučit nebo zrušit?
Možnost vyloučit některé účty, které nepoužívají MFA, se v tuto chvíli nepodporuje.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Existují nějaká omezení pro Security Center identity a ochrany přístupu?
Existují určitá omezení pro Security Center identity a ochrany přístupu:

- Doporučení pro identitu nejsou k dispozici pro předplatné s více než 600 účty. V takových případech budou tato doporučení uvedena v části nedostupná posouzení.
- Doporučení pro identitu nejsou k dispozici pro agenty správce pro poskytovatele Cloud Solution Provider (CSP).
- Doporučení identity neidentifikují účty spravované pomocí privilegovaného systému identity managementu (PIM). Pokud používáte nástroj PIM, můžou se v ovládacím prvku **Správa přístupu a oprávnění** zobrazit nepřesné výsledky.


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujícím článku:

- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)