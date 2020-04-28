---
title: Rozdíly v ověřování podle podporovaných typů účtů – Microsoft Identity Platform | Azure
description: Přečtěte si o rozdílech mezi ověřováním různých vlastností pro různé podporované typy účtů při registraci aplikace pomocí platformy Microsoft identity.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128859"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Rozdíly v ověřování podle podporovaných typů účtů (signInAudience)

Při registraci aplikace s platformou Microsoft identity pro vývojáře budete požádáni, abyste vybrali, které typy účtů vaše aplikace podporuje. V objektu aplikace a manifestu je `signInAudience`Tato vlastnost.

Mezi tyto možnosti patří:

- *AzureADMyOrg*: pouze účty v adresáři organizace, ve kterém je aplikace zaregistrovaná (jeden tenant)
- *AzureADMultipleOrgs*: účty v jakémkoli organizačním adresáři (více tenantů)
- *AzureADandPersonalMicrosoftAccount*: účty v jakémkoli adresáři organizace (multi-tenant) a osobní účty Microsoft (například Skype, Xbox a Outlook.com)

U registrovaných aplikací můžete najít hodnotu pro podporované typy účtů v části **ověřování** aplikace. Můžete ji také najít pod `signInAudience` vlastností v **manifestu**.

Hodnota, kterou vyberete pro tuto vlastnost, má vliv na jiné vlastnosti objektu aplikace. Výsledkem je, že pokud změníte tuto vlastnost, možná budete muset nejdřív změnit další vlastnosti.

V následující tabulce jsou uvedeny rozdíly v ověřování různých vlastností pro různé podporované typy účtů.

| Vlastnost | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` a `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identifikátor URI ID aplikace`identifierURIs`()  | Musí být v tenantovi jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs  | Musí být globálně jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs | Musí být globálně jedinečný. <br><br> schémata urn://nejsou podporovaná. <br><br> Zástupné znaky, fragmenty a řetězce dotazů nejsou podporované. <br><br> Maximální délka 120 znaků <br><br> Maximálně 50 identifierURIs |
| Certifikáty (`keyCredentials`) | Symetrický podpisový klíč | Symetrický podpisový klíč | Šifrovací a asymetrický podpisový klíč | 
| Tajné klíče klienta`passwordCredentials`() | Bez omezení * | Bez omezení * | Pokud je povoleno liveSDK: maximum ze 2 klientských tajných klíčů | 
| Identifikátory URI pro`replyURLs`přesměrování () | Další informace najdete v tématu [omezení a omezení adresy URL pro přesměrování/adresa URL pro odpověď](reply-url.md) . | | | 
| Oprávnění rozhraní API`requiredResourceAccess`() | Bez omezení * | Bez omezení * | Maximální povolený počet 30 oprávnění na prostředek (např. Microsoft Graph) | 
| Obory definované tímto rozhraním`oauth2Permissions`API () | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů |  Maximální délka názvu rozsahu 40 znaků <br><br> Je definovaných maximálně 100 oborů. | 
| Autorizované klientské aplikace (`preautorizedApplications`) | Bez omezení * | Bez omezení * | Celkem maximum 500 <br><br> Je definovaných maximálně 100 klientských aplikací. <br><br> Maximálně 30 oborů definovaných na klienta | 
| appRoles | Podporuje se <br> Bez omezení * | Podporuje se <br> Bez omezení * | Nepodporuje se | 
| Odhlašovací adresa URL | http://localhostje povoleno <br><br> Maximální délka 255 znaků | http://localhostje povoleno <br><br> Maximální délka 255 znaků | <br><br> https://localhostje povoleno, http://localhost pro MSA se nezdařila <br><br> Maximální délka 255 znaků <br><br> Schéma HTTP není povolené. <br><br> Zástupné znaky se nepodporují. | 

* V rámci všech vlastností kolekce objektu aplikace je globální limit o 1000 položek.

## <a name="next-steps"></a>Další kroky

- Další informace o [registraci aplikace](app-objects-and-service-principals.md)
- Další informace o [manifestu aplikace](reference-app-manifest.md)
