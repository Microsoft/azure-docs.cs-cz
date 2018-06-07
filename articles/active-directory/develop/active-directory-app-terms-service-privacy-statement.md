---
title: Podmínky služby a ochrana osobních údajů příkazu pro aplikace Azure AD | Microsoft Docs
description: Zjistěte, jak můžete nakonfigurovat podmínky služby a ochrana osobních údajů příkaz pro aplikace, které jsou zaregistrované pro používání služby Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: celested
ms.reviwer: lenalepa; sureshja
ms.custom: aaddev
ms.openlocfilehash: 8abd9c35c7d979e1e1a42dd7e178d802f06c4227
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654778"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Podmínky služby a ochrana osobních údajů příkazu pro registrované aplikace Azure Active Directory

Vývojáři, kteří vytvářet a spravovat aplikace, které se integrují s Azure Active Directory (Azure AD) a účty Microsoft, by měly obsahovat odkazy na aplikace podmínky služby a ochrana osobních údajů příkazu. Podmínky služby a ochrana osobních údajů příkazu jsou prezentované uživatelům prostřednictvím možnosti souhlasu uživatele. Pomáhají uživatelům vědět, že budou důvěřovat vaší aplikace. Podmínky služby a ochrana osobních údajů příkazu jsou obzvláště důležité pro uživatelsky orientovaný víceklientské aplikace – aplikace, které jsou používány víc adresářů nebo jsou k dispozici žádný účet Microsoft.

Jste zodpovědní za vytváření příkaz dokumenty pro vaši aplikaci a pro zajištění adresy URL k těmto dokumentům podmínky služby a ochrany osobních údajů. Pro víceklientské aplikace, které se nepodařilo poskytnout tyto odkazy činnost koncového uživatele souhlasu pro vaši aplikaci se zobrazí výstraha, což může bránit uživatelům v souhlas do vaší aplikace.

> [!NOTE]
> * Jednoho klienta aplikace nebude zobrazovat výstrahy.
> * Pokud chybí jedna nebo obě dvě odkazů, aplikace se zobrazí výstraha.

## <a name="user-consent-experience"></a>Činnost koncového uživatele souhlasu

Následující příklady ukazují uživatele souhlas prostředí, když jsou nakonfigurované podmínky služby a ochrana osobních údajů příkazu a pokud nejsou nakonfigurované tyto odkazy.

![Snímky obrazovky a bez prohlášení o ochraně osobních údajů a podmínky služby](./media/active-directory-integrating-applications/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formátování odkazy na podmínky služby a o ochraně osobních údajů dokumenty – příkaz

Než přidáte odkazy na vaší aplikace podmínky služby a o ochraně osobních údajů příkaz dokumenty, zkontrolujte, zda že adresy URL, postupujte podle následujících pokynů.

| Obecné zásady     | Popis                           |
|---------------|---------------------------------------|
| Formát        | Platná adresa URL.                             |
| Platná schémata | Protokoly HTTP a HTTPS</br>Doporučujeme, abyste HTTPS |
| Maximální délka    | 2 048 znaků                       |

Příklady: `https://myapp.com/terms-of-service` a `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Přidávání odkazů na podmínky služby a ochrana osobních údajů – příkaz

Když podmínky služby a ochrana osobních údajů příkaz připravené, můžete přidat odkazy k těmto dokumentům ve vaší aplikaci pomocí jedné z těchto metod:
* [Prostřednictvím portálu Azure](#registered-in-azure-portal)
* [V portálu pro registraci aplikace nebo Centrum pro vývojáře](#registered-in-app-reg-portal)
* [Pomocí aplikace objekt JSON](#app-object-json)
* [Pomocí beta MSGraph REST API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Pokud jste aplikaci nezaregistrovali na portálu Azure
Pokud jste aplikaci nezaregistrovali na portálu Azure, použijte následující postup.

1. Přihlaste se k [portál Azure](https://portal.azure.com/).
2. Přejděte na **registrace aplikace** části a vyberte svou aplikaci.
3. Otevřete **vlastnosti** část aplikace.
4. Vyplňte **URL služby podmínky** a **adresa URL prohlášení o ochraně osobních údajů** pole.
5. Uložte provedené změny.

![Oddíl properties App s podmínkami příkaz adresy URL služby a o ochraně osobních údajů](./media/active-directory-integrating-applications/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Pokud jste aplikaci nezaregistrovali v portálu pro registraci aplikace
Pokud jste aplikaci nezaregistrovali v portálu pro registraci aplikace nebo webu Dev Center, použijte následující postup.

1. Přihlaste se k [portálu pro registraci aplikace](https://apps.dev.microsoft.com/).
2. Vyberte svou aplikaci a přejděte k položce **profil** části.
3. Vyplňte **URL služby podmínky** a **adresa URL prohlášení o ochraně osobních údajů** pole.
4. Uložte provedené změny.

![Část profilu aplikace s podmínkami příkaz adresy URL služby a o ochraně osobních údajů](./media/active-directory-integrating-applications/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Pomocí aplikace objekt JSON
Pokud dáváte přednost přímo upravit aplikaci objekt JSON, můžete v editoru manifestu v portálu Azure nebo portálu pro registraci aplikace obsahují odkazy na vaší aplikace podmínky služby a ochrana osobních údajů příkaz.

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>Pomocí beta MSGraph REST API
Prostřednictvím kódu programu aktualizovat všechny aplikace, můžete na MSGraph používání beta verze rozhraní API REST k aktualizaci všech vašich aplikací obsahují odkazy na podmínky služby a o ochraně osobních údajů příkaz dokumenty.

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
> * Dejte pozor, abyste přepíší všechny existující hodnoty, které jste přiřadili ke kterékoli z těchto polí: `supportUrl`, `marketingUrl`, a `logoUrl`
> * Beta MSGraph REST API bude fungovat pouze, když se přihlásíte pomocí účtu služby Azure AD. Osobní účty Microsoft nejsou podporovány.