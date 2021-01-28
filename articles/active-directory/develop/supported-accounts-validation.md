---
title: Rozdíly v ověřování podle podporovaných typů účtů | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi ověřováním různých vlastností pro různé podporované typy účtů při registraci aplikace pomocí platformy Microsoft identity.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: eea2e587a075d774a25f479ec61575a002b57f75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937807"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Rozdíly v ověřování podle podporovaných typů účtů (signInAudience)

Při registraci aplikace s platformou Microsoft identity pro vývojáře budete požádáni, abyste vybrali, které typy účtů vaše aplikace podporuje. V objektu aplikace a manifestu je tato vlastnost `signInAudience` .

Mezi tyto možnosti patří:

- **AzureADMyOrg**: pouze účty v adresáři organizace, ve kterém je aplikace zaregistrovaná (jeden tenant).
- **AzureADMultipleOrgs**: účty v jakémkoli organizačním adresáři (více tenantů).
- **AzureADandPersonalMicrosoftAccount**: účty v jakémkoli adresáři organizace (multi-tenant) a osobní účty Microsoft (například Skype, Xbox a Outlook.com).

U registrovaných aplikací můžete najít hodnotu pro podporované typy účtů v části **ověřování** aplikace. Můžete ji také najít pod `signInAudience` vlastností v **manifestu**.

Hodnota, kterou vyberete pro tuto vlastnost, má vliv na jiné vlastnosti objektu aplikace. Výsledkem je, že pokud změníte tuto vlastnost, možná budete muset nejdřív změnit další vlastnosti.

V následující tabulce jsou uvedeny rozdíly v ověřování různých vlastností pro různé podporované typy účtů.

| Vlastnost | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` a `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identifikátor URI ID aplikace ( `identifierURIs` )  | Musí být v tenantovi jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs  | Musí být globálně jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs | Musí být globálně jedinečný. <br><br> schémata urn://nejsou podporovaná. <br><br> Zástupné znaky, fragmenty a řetězce dotazů nejsou podporované. <br><br> Maximální délka 120 znaků <br><br> Maximálně 50 identifierURIs |
| Certifikáty ( `keyCredentials` ) | Symetrický podpisový klíč | Symetrický podpisový klíč | Šifrovací a asymetrický podpisový klíč | 
| Tajné klíče klienta ( `passwordCredentials` ) | Bez omezení * | Bez omezení * | Pokud je povoleno liveSDK: maximum ze 2 klientských tajných klíčů | 
| Identifikátory URI pro přesměrování ( `replyURLs` ) | Další informace najdete v tématu [omezení a omezení adresy URL pro přesměrování/adresa URL pro odpověď](reply-url.md) . | | | 
| Oprávnění rozhraní API ( `requiredResourceAccess` ) | Bez omezení * | Bez omezení * | Maximální počet 50 prostředků na aplikaci a 30 oprávnění na prostředek (např. Microsoft Graph). Celkový limit 200 na aplikaci (prostředky × oprávnění). | 
| Obory definované tímto rozhraním API ( `oauth2Permissions` ) | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů |  Maximální délka názvu rozsahu 40 znaků <br><br> Je definovaných maximálně 100 oborů. | 
| Autorizované klientské aplikace ( `preAuthorizedApplications` ) | Bez omezení * | Bez omezení * | Celkem maximum 500 <br><br> Je definovaných maximálně 100 klientských aplikací. <br><br> Maximálně 30 oborů definovaných na klienta | 
| appRoles | Podporováno <br> Bez omezení * | Podporováno <br> Bez omezení * | Nepodporováno | 
| Adresa URL pro odhlášení front-Channel | https://localhost je povoleno <br><br> `http` schéma není povolené. <br><br> Maximální délka 255 znaků | https://localhost je povoleno <br><br> `http` schéma není povolené. <br><br> Maximální délka 255 znaků | <br><br> https://localhost je povoleno, http://localhost pro MSA se nezdařila <br><br> Maximální délka 255 znaků <br><br> `http` schéma není povolené. <br><br> Zástupné znaky se nepodporují. | 

* V rámci všech vlastností kolekce objektu aplikace je globální limit o 1000 položek.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [registraci aplikace](app-objects-and-service-principals.md).
- Přečtěte si o [manifestu aplikace](reference-app-manifest.md).
