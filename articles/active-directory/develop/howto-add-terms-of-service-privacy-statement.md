---
title: Podmínky služby a o ochraně osobních údajů pro aplikace | Azure
description: Zjistěte, jak nakonfigurovat podmínky služby a o ochraně osobních údajů pro aplikace zaregistrované k používání služby Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482853"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Postup: Konfigurace podmínky služby a o ochraně osobních údajů pro aplikaci

Vývojáři, kteří vytvářejí a spravovat aplikace, které se integrují s Azure Active Directory (Azure AD) a účty Microsoft, by měly zahrnovat odkazy na aplikace podmínkami služby a o ochraně osobních údajů. Podmínky služby a o ochraně osobních údajů se zobrazí uživatelům prostřednictvím prostředí pro vyjádření souhlasu uživatele. Pomáhají uživatelům vědět, že se vztah důvěryhodnosti aplikace. Podmínky služby a o ochraně osobních údajů jsou obzvláště důležité pro aplikace s více tenanty přístupných – aplikace, které jsou používány více adresářů nebo jsou k dispozici k libovolnému účtu Microsoft.

Zodpovídáte za vytvoření příkazu dokumenty pro vaši aplikaci a za poskytování adres URL k těmto dokumentům podmínky služby a o ochraně osobních údajů. Pro aplikace s více tenanty, které se nepodařilo poskytnout tyto odkazy prostředí pro vyjádření souhlasu uživatele pro vaši aplikaci se zobrazí upozornění, které mohou bránit uživatelům od vyjádření souhlasu s vaší aplikace.

> [!NOTE]
> * Aplikace pro jednoho tenanta nebude zobrazovat výstrahy.
> * Pokud chybí jeden nebo oba dva odkazy, vaše aplikace se zobrazí upozornění.

## <a name="user-consent-experience"></a>Prostředí pro vyjádření souhlasu uživatele

Následující příklady ukazují uživatel souhlas prostředí, když jsou nakonfigurované podmínky služby a o ochraně osobních údajů a pokud nejsou nakonfigurovaná pod následujícími odkazy.

![Snímky obrazovky a nemusíte prohlášení o ochraně osobních údajů a podmínky služby](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formátování odkazy na podmínky služby a o ochraně osobních údajů dokumentů – příkaz

Předtím, než přidáte odkazy na vaší aplikace podmínky služby a o ochraně osobních údajů příkaz dokumenty, ujistěte se, že adresy URL, postupujte podle následujících pokynů.

| Pokyn     | Popis                           |
|---------------|---------------------------------------|
| Formát        | Platná adresa URL.                             |
| Platná schémata | Protokoly HTTP a HTTPS<br/>Doporučujeme, abyste HTTPS |
| Maximální délka    | 2 048 znaků                       |

Příklady: `https://myapp.com/terms-of-service` a `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Přidání odkazů na podmínky služby a o ochraně osobních údajů – příkaz

Jakmile jsou připravené podmínkami služby a o ochraně osobních údajů, můžete přidat odkazy k těmto dokumentům ve své aplikaci pomocí jedné z těchto metod:

* [Na webu Azure portal](#azure-portal)
* [Pomocí aplikace objektu JSON](#app-object-json)
* [Pomocí MSGraph beta verze rozhraní REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Pomocí webu Azure portal
Postupujte podle těchto kroků na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte **registrace aplikací** části a vyberte svou aplikaci.
3. Otevřít **značky** podokně.
4. Vyplňte **URL podmínek služby** a **adresa URL prohlášení o ochraně osobních údajů** pole.
5. Uložte provedené změny.

    ![Vlastnosti aplikace obsahuje podmínky adresy URL služby a o ochraně osobních údajů – příkaz](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Pomocí aplikace objektu JSON

Pokud chcete upravit aplikaci objekt JSON přímo, vám pomůže editoru manifestu v portálu Azure portal nebo portálu pro registraci aplikace zahrnovat odkazy na podmínky služby a o ochraně osobních údajů vaší aplikace.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Pomocí MSGraph beta verze rozhraní REST API

Programově aktualizovat všechny aplikace, můžete použít MSGraph beta verze rozhraní REST API aktualizovat všechny vaše aplikace zahrnovat odkazy na podmínky služby a o ochraně osobních údajů příkaz dokumenty.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
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
> * Dejte pozor, abyste přepsat všechny existující hodnoty, které jste přiřadili ke kterékoli z těchto polí: `supportUrl`, `marketingUrl`, a `logoUrl`
> * MSGraph beta verze rozhraní REST API služby bude fungovat jenom když se přihlásíte účtu Azure AD. Osobní účty Microsoft se nepodporují.
