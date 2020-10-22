---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 575c139a3b417eb9429695d3ea6be26bf5625de5
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371065"
---
# <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu

Hraniční zabezpečení se vyvinulo z hraniční sítě až po hranice identity. V tomto vývojovém prostředí je zabezpečení méně o obraně vaší sítě a další informace o správě zabezpečení aplikací, dat a uživatelů.

Když sledujete aktivity a nastavení konfigurace související s identitou, můžete proaktivně provádět aktivní akce dřív, než dojde k incidentu, nebo reaktivní akce k zastavení pokusů o útok.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Jakou ochranu identity a přístupu Security Center poskytuje? 

Azure Security Center má dva vyhrazené bezpečnostní prvky zabezpečení pro zajištění, že splňujete požadavky na identitu a zabezpečení vaší organizace: 

 - **Správa přístupu a oprávnění** – doporučujeme, abyste přijali [model přístupu s minimálními oprávněními](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) a zajistili uživatelům udělení přístupu pouze k tomu, aby mohli provádět své úlohy. Tento ovládací prvek také obsahuje doporučení pro řízení přístupu k vašim prostředkům [pomocí implementace řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) .
 
 - **Povolení MFA** – s povoleným [ověřováním MFA](https://www.microsoft.com/security/business/identity/mfa) jsou vaše účty bezpečnější a uživatelé se můžou i nadále ověřovat prakticky u všech aplikací s jednotným přihlašováním.

### <a name="example-recommendations-for-identity-and-access"></a>Příklady doporučení pro identitu a přístup

Příklady doporučení, která se můžou zobrazit v těchto dvou ovládacích prvcích na stránce s **doporučeními** pro Security Center:

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Zastaralé účty by se měly odebírat z předplatného (zastaralé účty jsou účty, které už nepotřebujete, a zablokovat přihlášení pomocí Azure Active Directory).

> [!TIP]
> Další informace o těchto doporučeních a dalších těchto ovládacích prvcích najdete v tématu [věnovaném doporučením pro identitu a přístup](recommendations-reference.md#recs-identity).

### <a name="limitations"></a>Omezení

Existují určitá omezení pro Security Center identity a ochrany přístupu:

- Doporučení pro identitu nejsou k dispozici pro předplatné s více než 600 účty. V takových případech budou tato doporučení uvedena v části nedostupná posouzení.
- Doporučení pro identitu nejsou k dispozici pro agenty správce pro poskytovatele Cloud Solution Provider (CSP).
- Doporučení identity neidentifikují účty spravované pomocí privilegovaného systému identity managementu (PIM). Pokud používáte nástroj PIM, můžou se v ovládacím prvku **Správa přístupu a oprávnění** zobrazit nepřesné výsledky.

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Multi-Factor Authentication (MFA) a Azure Active Directory 

Povolení MFA vyžaduje [oprávnění klienta Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

- Pokud máte edici Premium ve službě AD, povolte vícefaktorové ověřování pomocí [podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Pokud používáte bezplatnou edici AD, povolte **výchozí nastavení zabezpečení** , jak je popsáno v [Azure Active Directory dokumentaci](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifikace účtů bez povoleného ověřování MFA (Multi-Factor Authentication)

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


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujícím článku:

- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)