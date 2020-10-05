---
title: Nastavení Identity Protection a podmíněného přístupu v Azure AD B2C
description: Přečtěte si, jak nakonfigurovat ochranu identit a podmíněný přístup pro vás Azure AD B2C tenanta, abyste mohli zobrazit rizikové přihlašování a jiné rizikové události a vytvářet zásady na základě detekce rizik.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5554cfcde9aba1b0e5c9c8b60e2e6a7e9a8ba378
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89271528"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Nastavení Identity Protection a podmíněného přístupu v Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection poskytuje průběžné zjišťování rizik pro vašeho tenanta Azure AD B2C. Pokud je cenová úroveň tenanta Azure AD B2C Premium P2, můžete v Azure Portal zobrazit podrobné rizikové události ochrany identity. Pomocí zásad [podmíněného přístupu](../active-directory/conditional-access/overview.md) na základě těchto detekcí rizik můžete také určit akce a vynutilit zásady organizace.

## <a name="prerequisites"></a>Předpoklady

- Váš tenant Azure AD B2C musí být [propojený s předplatným služby Azure AD](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- K použití podmíněného přístupu na základě rizik a rizika uživatele se vyžaduje Azure AD B2C Premium P2. V případě potřeby [změňte cenovou úroveň Azure AD B2C na Premium P2](https://aka.ms/exid-pricing-tier). 
- Pokud chcete spravovat Identity Protection a podmíněný přístup v tenantovi B2C, budete potřebovat účet, kterému se přiřadí role globálního správce nebo role správce zabezpečení.
- Pokud chcete tyto funkce ve vašem tenantovi používat, musíte nejdřív přepnout do cenové úrovně Azure AD B2C Premium P2.

## <a name="set-up-identity-protection"></a>Nastavení Identity Protection

Služba Identity Protection je ve výchozím nastavení zapnutá. Pokud chcete mít možnost Zobrazit rizikové události ochrany identity v tenantovi Azure AD B2C, stačí propojit klienta Azure AD B2C s předplatným Azure AD a vybrat cenovou úroveň Azure AD B2C Premium P2. V Azure Portal můžete zobrazit podrobné sestavy rizikových událostí.

### <a name="supported-identity-protection-risk-detections"></a>Podporovaná detekce rizik ochrany identity

Pro Azure AD B2C se aktuálně podporují následující detekce rizik:  

|Typ detekce rizika  |Popis  |
|---------|---------|
| Neobvyklá cesta     | Přihlaste se z neobvyklých míst na základě nedávných přihlášení uživatele.        |
|Anonymní IP adresa     | Přihlaste se z anonymní IP adresy (například: Prohlížeè Browser, Anonymizer VPN)        |
|Neznámé vlastnosti přihlášení     | Přihlaste se pomocí vlastností, které pro daného uživatele nevidíme nedávno.        |
|Propojená IP adresa pro malware     | Přihlášení z IP adresy propojené s malwarem         |
|Analýza hrozeb v Azure AD     | Zdroje analýzy interních a externích hrozeb Microsoftu identifikovaly známý vzor útoku.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Zobrazení rizikových událostí pro vašeho tenanta Azure AD B2C

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. V části **zabezpečení**vyberte **rizikové uživatele (Preview)**.

   ![Rizikoví uživatelé](media/conditional-access-identity-protection-setup/risky-users.png)

1. V části **zabezpečení**vyberte možnost **detekce rizik (Preview)**.

   ![Detekce rizik](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Přidání zásady podmíněného přístupu 

Pokud chcete přidat zásady podmíněného přístupu na základě detekce rizik ochrany identity, ujistěte se, že výchozí nastavení zabezpečení je pro vašeho tenanta Azure AD B2C zakázané, a pak vytvořte zásady podmíněného přístupu.

### <a name="to-disable-security-defaults"></a>Zakázání výchozích hodnot zabezpečení

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

3. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

4. Vyberte **vlastnosti**a pak vyberte **Spravovat výchozí nastavení zabezpečení**.

   ![Zakázat výchozí hodnoty zabezpečení](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. V části Povolit výchozí nastavení zabezpečení vyberte ne. 

   ![Nastavte přepínač Povolit výchozí hodnoty zabezpečení na ne.](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

1. V části **zabezpečení**vyberte **podmíněný přístup (Preview)**. Otevře se stránka **zásady podmíněného přístupu** . 

1. Vyberte **nové zásady** a podle dokumentace podmíněného přístupu Azure AD vytvořte novou zásadu. Například:

   - [Podmíněný přístup na základě rizik přihlašování: povolit pomocí zásad podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > Když vybíráte uživatele, pro které chcete zásadu použít, nevybírejte jenom možnost **Všichni uživatelé** , nebo se můžete sami zablokovat přihlášení.

## <a name="test-the-conditional-access-policy"></a>Test zásad podmíněného přístupu

1. Vytvořte zásadu podmíněného přístupu, jak je uvedeno výše, s následujícím nastavením:
   
   - V části **Uživatelé a skupiny**vyberte testovacího uživatele. Nevybírejte možnost **Všichni uživatelé** nebo se vám zablokuje přihlášení.
   - U **cloudových aplikací nebo akcí**zvolte **vybrat aplikace**a pak zvolte svoji aplikaci předávající strany.
   - V případě podmínek vyberte možnost **rizika přihlášení** a **Vysoká**, **střední**a **Nízká** úroveň rizika.
   - V případě **udělení**vyberte možnost **blokovat přístup**.

      ![Zvolit přístup k bloku](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Povolte zásady podmíněného přístupu, a to tak, že vyberete **vytvořit**.

1. Simulujte rizikové přihlašování pomocí [prohlížeče pro mandát](https://www.torproject.org/download/). 

1. V jwt.ms depodepsaném tokenu pro pokusy o přihlášení byste měli vidět, že přihlášení bylo blokované:

   ![Otestování blokovaného přihlášení](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Kontrola výsledků podmíněného přístupu v sestavě auditu

Kontrola výsledku události podmíněného přístupu:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.

4. V části **aktivity**vyberte **protokoly auditu**.

5. Filtrujte protokol auditu nastavením **kategorie** na **B2C** a nastavení **typu prostředku aktivity** na **IdentityProtection**. Pak vyberte **Použít**.

6. Zkontrolujte aktivitu auditu za posledních 7 dní. K dispozici jsou následující typy aktivit:

   - **Vyhodnotit zásady podmíněného přístupu**: Tato položka protokolu auditu indikuje, že při ověřování proběhlo vyhodnocení podmíněného přístupu.
   - **Opravit uživatele**: Tato položka znamená, že koncový uživatel splnil požadavky na udělení nebo požadavky zásad podmíněného přístupu, a tato aktivita byla hlášena rizikovým modulům, aby snížila riziko (zmírnění) uživatele.

7. Výběrem položky **vyhodnotit položku protokolu zásad podmíněného přístupu** v seznamu otevřete stránku **Podrobnosti o aktivitě: protokol auditu** , která zobrazuje identifikátory protokolů auditu spolu s těmito informacemi v části **Další podrobnosti** :

   - ConditionalAccessResult: Grant vyžadovaná vyhodnocením podmíněných zásad.
   - AppliedPolicies: seznam všech zásad podmíněného přístupu, ve kterých byly podmínky splněny, a zásady jsou ZAPNUTé.
   - ReportingPolicies: seznam zásad podmíněného přístupu, které byly nastaveny na režim pouze pro sestavy a kde byly splněny podmínky.

## <a name="next-steps"></a>Další kroky

[Přidejte podmíněný přístup do toku uživatele](conditional-access-user-flow.md).
