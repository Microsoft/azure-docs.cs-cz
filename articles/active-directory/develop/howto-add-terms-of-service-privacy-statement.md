---
title: Podmínka služby a prohlášení o zásadách ochrany osobních údajů pro aplikace | Azure
description: Zjistěte, jak můžete nakonfigurovat licenční smlouvy a prohlášení o zásadách ochrany osobních údajů pro aplikace registrované pro použití Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 73c4931533e3926086320531a0800a572d13808c
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89535768"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Postupy: Konfigurace podmínek služby a prohlášení o zásadách ochrany osobních údajů pro aplikaci

Vývojáři, kteří sestavují a spravují aplikace, které se integrují s Azure Active Directory (Azure AD) a účty Microsoft by měly obsahovat odkazy na licenční smlouvu a prohlášení o zásadách ochrany osobních údajů aplikace. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou v souladu s uživatelským prostředím týkajícím se souhlasu uživatele. Pomůžou uživatelům znát, že můžou důvěřovat vaší aplikaci. Podmínek služby a prohlášení o zásadách ochrany osobních údajů jsou obzvláště důležité pro uživatele s více klienty – aplikace, které používá více adresářů, nebo jsou k dispozici pro všechny účet Microsoft.

Zodpovídáte za vytváření dokumentů o poskytování služeb a prohlášení o zásadách ochrany osobních údajů pro vaši aplikaci a pro poskytování adres URL těmto dokumentům. U aplikací s více klienty, které neposkytují tyto odkazy, se v prostředí pro vyjádření souhlasu uživatele s vaší aplikací zobrazí výstraha, která může zabránit uživatelům v souběžném posílání do vaší aplikace.

> [!NOTE]
> * U aplikací s jedním klientem se nezobrazí upozornění.
> * Pokud chybí jeden nebo oba dva odkazy, zobrazí se v aplikaci výstraha.

## <a name="user-consent-experience"></a>Uživatelské prostředí pro vyjádření souhlasu uživatele

Následující příklady znázorňují uživatelské prostředí pro vyjádření souhlasu uživatele, když jsou nakonfigurované podmínky a prohlášení o zásadách ochrany osobních údajů, a pokud nejsou tyto odkazy nakonfigurované.

![Snímky obrazovky s a bez prohlášení o zásadách ochrany osobních údajů a poskytovaných podmínek služby](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formátování odkazů na dokumenty k podmínkám služby a prohlášení o zásadách ochrany osobních údajů

Předtím, než přidáte odkazy na dokumenty k podmínkám služby a prohlášení o zásadách ochrany osobních údajů vaší aplikace, ujistěte se, že adresy URL následují po těchto pokynech.

| Pokyn     | Popis                           |
|---------------|---------------------------------------|
| Formát        | Platná adresa URL                             |
| Platná schémata | Protokoly HTTP a HTTPS<br/>Doporučujeme protokol HTTPS |
| Maximální délka    | 2048 znaků                       |

Příklady: `https://myapp.com/terms-of-service` a `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Přidání odkazů na podmínek služby a prohlášení o zásadách ochrany osobních údajů

Po přípravě podmínek služby a prohlášení o zásadách ochrany osobních údajů můžete do aplikace přidat odkazy na tyto dokumenty pomocí jedné z těchto metod:

* [Prostřednictvím Azure Portal](#azure-portal)
* [Použití JSON objektu aplikace](#app-object-json)
* [Používání rozhraní Microsoft Graph API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Použití webu Azure Portal
Postupujte podle následujících kroků v Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)vyberte správného tenanta AzureAD (ne B2C).
2. Přejděte do části **Registrace aplikací** a vyberte svou aplikaci.
3. Otevřete podokno **branding** .
4. Vyplňte pole **Adresa URL podmínek služby** a **Adresa URL prohlášení o zásadách ochrany osobních údajů** .
5. Uložte provedené změny.

    ![Vlastnosti aplikace obsahují adresy URL podmínek služby a prohlášení o zásadách ochrany osobních údajů.](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Použití JSON objektu aplikace

Pokud upřednostňujete úpravu JSON objektu aplikace přímo, můžete použít editor manifestu na portálu Azure Portal nebo portál pro registraci aplikací a zahrnout odkazy na podmínku služby a prohlášení o zásadách ochrany osobních údajů vaší aplikace.

1. Přejděte do části **Registrace aplikací** a vyberte svou aplikaci.
2. Otevřete podokno **manifest** .
3. CTRL + F, vyhledejte "informationalUrls". Vyplňte informace.
4. Uložte provedené změny.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Používání rozhraní Microsoft Graph API

Pokud chcete programově aktualizovat všechny vaše aplikace, můžete pomocí rozhraní Microsoft Graph API aktualizovat všechny své aplikace tak, aby obsahovaly odkazy na dokumenty o licenčních službách a prohlášení o zásadách ochrany osobních údajů.

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
> * Dejte pozor, abyste nepřepsali všechny dříve existující hodnoty, které jste přiřadili k některým z těchto polí: `supportUrl` , `marketingUrl` a. `logoUrl`
> * Rozhraní API pro Microsoft Graph funguje, jenom když se přihlašujete pomocí účtu Azure AD. Osobní účty Microsoft se nepodporují.
