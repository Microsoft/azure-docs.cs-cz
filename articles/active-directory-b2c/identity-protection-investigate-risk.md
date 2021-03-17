---
title: Prověření rizika pomocí Azure Active Directory B2C Identity Protection
description: Naučte se prozkoumat rizikové uživatele a zjišťovat Azure AD B2C Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055693"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Prozkoumejte riziko pomocí Identity Protection v Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection poskytuje průběžné zjišťování rizik pro vašeho tenanta Azure AD B2C. Umožňuje organizacím zjišťovat, zkoumat a opravovat rizika založená na identitách. Identity Protection se dodává se zprávami o riziku, které se dají použít k prozkoumání rizik identity v Azure AD B2Cch klientech. V tomto článku se dozvíte, jak zkoumat a zmírnit rizika.

## <a name="overview"></a>Přehled

Azure AD B2C Identity Protection poskytuje dvě sestavy. Sestava *rizikové uživatele* je tam, kde Správci mohou zjistit, kteří uživatelé jsou ohroženi a jsou podrobnější informace o detekcích. Sestava *detekce rizik* obsahuje informace o jednotlivých detekcích rizik, včetně typu, dalších rizik aktivovaných ve stejnou dobu, pokusů o přihlášení a dalších akcí.

Každá sestava se spustí se seznamem všech zjišťování pro období zobrazené v horní části sestavy. Sestavy lze filtrovat pomocí filtrů v horní části sestavy. Správci si můžou zvolit, že si budou data stahovat, nebo k průběžnému exportu dat použít [sadu MS Graph API a Microsoft Graph PowerShell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) .

## <a name="service-limitations-and-considerations"></a>Omezení služby a požadavky

Při používání Identity Protection Vezměte v úvahu následující skutečnosti:

- Služba Identity Protection je ve výchozím nastavení zapnutá.
- Identity Protection je k dispozici pro místní i sociální identity, jako je Google nebo Facebook. Pro sociální identity musí být podmíněný přístup aktivovaný. Zjišťování je omezené, protože přihlašovací údaje účtu sociální sítě spravuje externí zprostředkovatel identity.
- V Azure AD B2C tenantech je k dispozici pouze podmnožina [Azure AD identity Protectionch detekcí rizik](../active-directory/identity-protection/overview-identity-protection.md) . Azure AD B2C podporuje následující detekce rizik:  

|Typ detekce rizika  |Popis  |
|---------|---------|
| Neobvyklá cesta     | Přihlaste se z neobvyklých míst na základě nedávných přihlášení uživatele.        |
|Anonymní IP adresa     | Přihlaste se z anonymní IP adresy (například: Browser, Anonymizer VPN).        |
|Propojená IP adresa pro malware     | Přihlaste se z propojené IP adresy s malwarem.         |
|Neznámé vlastnosti přihlášení     | Přihlaste se pomocí vlastností, které pro daného uživatele nevidíme nedávno.        |
|Správce potvrzuje ohrožení zabezpečení uživatele.    | Správce indikuje, že došlo k ohrožení zabezpečení uživatele.             |
|Sprej hesla     | Přihlaste se pomocí útoku proti rozstřiku hesla.      |
|Analýza hrozeb v Azure AD     | Zdroje analýzy interních a externích hrozeb Microsoftu identifikovaly známý vzor útoku.        |

## <a name="pricing-tier"></a>Cenová úroveň

U některých funkcí ochrany identit se vyžaduje Azure AD B2C Premium P2. V případě potřeby [změňte cenovou úroveň Azure AD B2C na Premium P2](./billing.md). Následující tabulka shrnuje funkce ochrany identit a požadovanou cenovou úroveň.  

|Funkce   |P1   |P2|
|----------|:-----------:|:------------:|
|Sestava rizikových uživatelů     |&#x2713; |&#x2713; |
|Podrobnosti sestavy rizikových uživatelů  | |&#x2713; |
|Riziková náprava sestav uživatelů    | &#x2713; |&#x2713; |
|Sestava detekce rizik   |&#x2713;|&#x2713;|
|Podrobnosti sestavy o detekci rizik  ||&#x2713;|
|Stažení sestavy |  &#x2713;| &#x2713;|
|Přístup k MS Graph API |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Prozkoumání rizikových uživatelů

Pomocí informací uvedených v sestavě rizikové uživatele můžou správci najít:

- Kteří uživatelé jsou ohroženi, došlo k nápravě rizik nebo byly vyrušeny riziko?
- Podrobnosti o detekcích
- Historie všech rizikových přihlášení
- Historie rizik
 
Správci se pak mohou rozhodnout, že budou provádět akce s těmito událostmi. Správci se můžou rozhodnout:

- Resetování hesla uživatele
- Potvrdit ohrožení uživatele
- Zavřít riziko uživatele
- Zablokovat uživatelům přihlášení
- Prozkoumat dál pomocí Azure ATP

### <a name="navigating-the-risky-users-report"></a>Navigace v sestavě rizikové uživatele

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.

1. V části **zabezpečení** vyberte **rizikové uživatele (Preview)**.

   ![Rizikoví uživatelé](media/identity-protection-investigate-risk/risky-users.png)

    Výběr jednotlivých položek rozbalí okno podrobností pod detekci. Zobrazení podrobností umožňuje správcům prozkoumat a provádět akce při každém zjišťování.

    ![Akce rizikové uživatele](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Sestava detekce rizik

Sestava zjišťování rizik obsahuje data, která lze filtrovat až do posledních 90 dnů (tři měsíce).

Pomocí informací poskytnutých sestavou zjišťování rizik můžou správci najít:

- Informace o každém zjišťování rizik včetně typu.
- Další rizika se spouštějí ve stejnou dobu.
- Umístění pokusu o přihlášení

Správci se pak mohou rozhodnout, že se vrátí do sestavy rizika nebo přihlášení uživatele, aby mohli provádět akce založené na shromažďovaných informacích.

### <a name="navigating-the-risk-detections-report"></a>Navigace v sestavě detekce rizik

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zabezpečení** vyberte možnost **detekce rizik (Preview)**.

   ![Detekce rizik](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Další kroky

- [Přidejte podmíněný přístup do toku uživatele](conditional-access-user-flow.md).