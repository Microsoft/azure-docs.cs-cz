---
title: Rozdíly v ověřování podle podporovaných typů účtů (SignInAudience) – Microsoft Identity Platform/Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bea6e3ea93e8b630970a8d86fc246eddaf3f56c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392602"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Rozdíly v ověřování podle podporovaných typů účtů (signInAudience)

Při registraci aplikace s platformou Microsoft identity pro vývojáře budete požádáni, abyste vybrali, které typy účtů vaše aplikace podporuje. V objektu aplikace a manifestu je tato vlastnost `signInAudience`.

Mezi tyto možnosti patří:

- *AzureADMyOrg*: pouze účty v adresáři organizace, ve kterém je aplikace zaregistrovaná (jeden tenant)
- *AzureADMultipleOrgs*: účty v jakémkoli organizačním adresáři (více tenantů)
- *AzureADandPersonalMicrosoftAccount*: účty v jakémkoli adresáři organizace (multi-tenant) a osobní účty Microsoft (například Skype, Xbox a Outlook.com)

U registrovaných aplikací můžete najít hodnotu pro podporované typy účtů v části **ověřování** aplikace. Můžete ji také najít pod vlastností `signInAudience` v **manifestu**.

Hodnota, kterou vyberete pro tuto vlastnost, má vliv na jiné vlastnosti objektu aplikace. Výsledkem je, že pokud změníte tuto vlastnost, možná budete muset nejdřív změnit další vlastnosti.

V následující tabulce jsou uvedeny rozdíly v ověřování různých vlastností pro různé podporované typy účtů.

| Vlastnost | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identifikátor URI ID aplikace (`identifierURIs`)  | Musí být v tenantovi jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs  | Musí být globálně jedinečný. <br><br> urn://jsou podporovaná. <br><br> Zástupné znaky se nepodporují. <br><br> Podporované řetězce a fragmenty dotazů <br><br> Maximální délka 255 znaků <br><br> Bez omezení * počtu identifierURIs | Musí být globálně jedinečný. <br><br> schémata urn://nejsou podporovaná. <br><br> Zástupné znaky, fragmenty a řetězce dotazů nejsou podporované. <br><br> Maximální délka 120 znaků <br><br> Maximálně 50 identifierURIs |
| Certifikáty (`keyCredentials`) | Symetrický podpisový klíč | Symetrický podpisový klíč | Šifrovací a asymetrický podpisový klíč | 
| Tajné kódy klienta (`passwordCredentials`) | Bez omezení * | Bez omezení * | Pokud je povoleno liveSDK: maximum ze 2 klientských tajných klíčů | 
| Identifikátory URI pro přesměrování (`replyURLs`) | Další informace najdete v tématu [omezení a omezení adresy URL pro přesměrování/adresa URL pro odpověď](reply-url.md) . | | | 
| Oprávnění rozhraní API (`requiredResourceAccess`) | Bez omezení * | Bez omezení * | Maximální povolený počet 30 oprávnění na prostředek (např. Microsoft Graph) | 
| Obory definované tímto rozhraním API (`oauth2Permissions`) | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů | Maximální délka názvu rozsahu 120 znaků <br><br> Bez omezení * počtu definovaných oborů |  Maximální délka názvu rozsahu 40 znaků <br><br> Je definovaných maximálně 100 oborů. | 
| Autorizované klientské aplikace (`preautorizedApplications`) | Bez omezení * | Bez omezení * | Celkem maximum 500 <br><br> Je definovaných maximálně 100 klientských aplikací. <br><br> Maximálně 30 oborů definovaných na klienta | 
| appRoles | Podporuje se <br> Bez omezení * | Podporuje se <br> Bez omezení * | Nepodporuje se | 
| Adresa URL – odhlášení | http://localhost je povolený. <br><br> Maximální délka 255 znaků | http://localhost je povolený. <br><br> Maximální délka 255 znaků | <br><br> https://localhost je povolená, http://localhost pro MSA se nezdařila. <br><br> Maximální délka 255 znaků <br><br> Schéma HTTP není povolené. <br><br> Zástupné znaky se nepodporují. | 

\* V rámci všech vlastností kolekce objektu aplikace je globální limit o 1000 položek.

## <a name="next-steps"></a>Další kroky

- Další informace o [registraci aplikace](app-objects-and-service-principals.md)
- Další informace o [manifestu aplikace](reference-app-manifest.md)
