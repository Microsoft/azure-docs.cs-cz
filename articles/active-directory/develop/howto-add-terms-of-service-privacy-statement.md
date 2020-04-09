---
title: Smluvní podmínky a prohlášení o zásadách ochrany osobních údajů pro aplikace | Azure
description: Zjistěte, jak můžete nakonfigurovat podmínky služby a prohlášení o zásadách ochrany osobních údajů pro aplikace registrované k používání Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 40e7a05505bc501c1c622e627a6d97cc57db1cfa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884235"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Postup: Konfigurace smluvních podmínek a prohlášení o zásadách ochrany osobních údajů pro aplikaci

Vývojáři, kteří vytvářejí a spravují aplikace, které se integrují s Azure Active Directory (Azure AD) a účty Microsoft, by měli obsahovat odkazy na smluvní podmínky aplikace a prohlášení o zásadách ochrany osobních údajů. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou uživatelům k dispozici prostřednictvím uživatelského souhlasu. Pomáhají uživatelům vědět, že mohou vaší aplikaci důvěřovat. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou obzvláště důležité pro aplikace s více klienty, které jsou používány více adresáři nebo jsou k dispozici pro libovolný účet Microsoft.

Jste zodpovědní za vytváření smluvních podmínek a dokumentů prohlášení o zásadách ochrany osobních údajů pro vaši aplikaci a za poskytování adres URL těchto dokumentů. U víceklientských aplikací, které tyto odkazy neposkytují, se v prostředí se souhlasem uživatele pro vaši aplikaci zobrazí výstraha, která může uživatele odradit od souhlasu s vaší aplikací.

> [!NOTE]
> * Aplikace s jedním tenantem se nezobrazí výstraha.
> * Pokud jeden nebo oba dva odkazy chybí, aplikace zobrazí upozornění.

## <a name="user-consent-experience"></a>Zkušenosti se souhlasem uživatele

Následující příklady ukazují, že prostředí souhlasu uživatele je při konfiguraci podmínek služby a prohlášení o zásadách ochrany osobních údajů a pokud tyto odkazy nejsou nakonfigurovány.

![Snímky obrazovky s prohlášením o zásadách ochrany osobních údajů a bez poskytovaných podmínek ochrany osobních údajů](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formátování odkazů na smluvní podmínky a dokumenty prohlášení o zásadách ochrany osobních údajů

Než přidáte odkazy na smluvní podmínky aplikace a dokumenty prohlášení o zásadách ochrany osobních údajů, ujistěte se, že adresy URL dodržují tyto pokyny.

| Pokyn     | Popis                           |
|---------------|---------------------------------------|
| Formát        | Platná adresa URL                             |
| Platná schémata | Protokoly HTTP a HTTPS<br/>Doporučujeme HTTPS |
| Maximální délka    | 2048 znaků                       |

Příklady: `https://myapp.com/terms-of-service` a`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Přidání odkazů na smluvní podmínky a prohlášení o zásadách ochrany osobních údajů

Když jsou podmínky služby a prohlášení o zásadách ochrany osobních údajů připraveny, můžete přidat odkazy na tyto dokumenty v aplikaci pomocí jedné z těchto metod:

* [Prostřednictvím portálu Azure](#azure-portal)
* [Použití objektu aplikace JSON](#app-object-json)
* [Použití rozhraní Microsoft Graph API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Použití webu Azure Portal
Postupujte podle těchto kroků na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do části **Registrace aplikací** a vyberte aplikaci.
3. Otevřete podokno **Branding.**
4. Vyplňte pole **URL smluvních podmínek** a **prohlášení o zásadách ochrany osobních údajů.**
5. Uložte provedené změny.

    ![Vlastnosti aplikace obsahují adresy URL prohlášení o zásadách služby a prohlášení o zásadách ochrany osobních údajů.](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Použití objektu aplikace JSON

Pokud dáváte přednost úpravě objektu aplikace JSON přímo, můžete použít editor manifestu na portálu Azure nebo portálu pro registraci aplikací a zahrnout odkazy na smluvní podmínky vaší aplikace a prohlášení o zásadách ochrany osobních údajů.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Použití rozhraní Microsoft Graph API

Chcete-li programově aktualizovat všechny aplikace, můžete pomocí rozhraní Microsoft Graph API aktualizovat všechny aplikace tak, aby obsahovaly odkazy na smluvní podmínky a dokumenty prohlášení o zásadách ochrany osobních údajů.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Dávejte pozor, abyste nepřepsali žádné již existující hodnoty, `supportUrl` `marketingUrl`které jste přiřadili k některému z těchto polí: , a`logoUrl`
> * Rozhraní Api Microsoft Graphu funguje jenom při přihlášení pomocí účtu Azure AD. Osobní účty Microsoft nejsou podporovány.
