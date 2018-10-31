---
title: Podmínky služby a o ochraně osobních údajů pro aplikace Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat podmínky služby a o ochraně osobních údajů pro aplikace zaregistrované k používání služby Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2195a43665f99e8388b13fc6dc3e528496bd3901
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242683"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Podmínky služby a o ochraně osobních údajů pro registrované aplikace Azure Active Directory

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
| Platná schémata | Protokoly HTTP a HTTPS</br>Doporučujeme, abyste HTTPS |
| Maximální délka    | 2 048 znaků                       |

Příklady: `https://myapp.com/terms-of-service` a `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Přidání odkazů na podmínky služby a o ochraně osobních údajů – příkaz

Jakmile jsou připravené podmínkami služby a o ochraně osobních údajů, můžete přidat odkazy k těmto dokumentům ve své aplikaci pomocí jedné z těchto metod:

* [Na webu Azure portal](#registered-in-azure-portal)
* [V portálu pro registraci aplikací nebo Centrum pro vývojáře](#registered-in-app-reg-portal)
* [Pomocí aplikace objektu JSON](#app-object-json)
* [Pomocí MSGraph beta verze rozhraní REST API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Pokud jste vaši aplikaci zaregistrovali na webu Azure portal

Pokud jste vaši aplikaci zaregistrovali na webu Azure portal, postupujte podle těchto kroků.

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/).
2. Přejděte **registrace aplikací** části a vyberte svou aplikaci.
3. Otevřít **vlastnosti** části aplikace.
4. Vyplňte **URL podmínek služby** a **adresa URL prohlášení o ochraně osobních údajů** pole.
5. Uložte provedené změny.

    ![Oddíl properties aplikace s podmínkami adresy URL služby a o ochraně osobních údajů – příkaz](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Pokud jste vaši aplikaci zaregistrovali na portál pro registraci aplikace

Pokud jste zaregistrovali aplikaci v portálu pro registraci aplikací nebo Centrum pro vývojáře, postupujte podle těchto kroků.

1. Přihlaste se k [portál pro registraci aplikací](https://apps.dev.microsoft.com/).
2. Vyberte vaši aplikaci a přejděte **profilu** oddílu.
3. Vyplňte **URL podmínek služby** a **adresa URL prohlášení o ochraně osobních údajů** pole.
4. Uložte provedené změny.

    ![Části profilu aplikace s podmínkami adresy URL služby a o ochraně osobních údajů – příkaz](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

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