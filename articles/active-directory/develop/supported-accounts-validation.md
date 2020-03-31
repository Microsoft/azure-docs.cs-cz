---
title: Rozdíly ve ověření podle podporovaných typů účtů – platforma identit Microsoftu | Azure
description: Informace o rozdílech ověření různých vlastností pro různé typy podporovaných účtů při registraci aplikace na platformě identit Microsoftu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128859"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Rozdíly ověření podle podporovaných typů účtů (signInAudience)

Při registraci aplikace s platformou identit microsoftu pro vývojáře budete vyzváni k výběru typů účtů, které vaše aplikace podporuje. V objektu aplikace a manifestu je `signInAudience`tato vlastnost .

Možnosti zahrnují následující:

- *AzureADMyOrg*: Pouze účty v adresáři organizace, kde je aplikace registrována (jeden tenant)
- *AzureADMultipleOrgs*: Účty v libovolném adresáři organizace (více klientů)
- *AzureADandPersonalMicrosoftAccount*: Účty v libovolném organizačním adresáři (víceklientských) a osobních účtech Microsoftu (například Skype, Xbox a Outlook.com)

U registrovaných aplikací můžete hodnotu podporovaných typů účtů najít v části **Ověřování** aplikace. Můžete ji také najít `signInAudience` pod vlastností v **manifestu**.

Hodnota, kterou vyberete pro tuto vlastnost, má vliv na jiné vlastnosti objektu aplikace. V důsledku toho pokud změníte tuto vlastnost, budete muset nejprve změnit další vlastnosti.

V následující tabulce naleznete rozdíly ověření různých vlastností pro různé podporované typy účtů.

| Vlastnost | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` a `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identifikátor URI ID aplikace (`identifierURIs`)  | Musí být jedinečný v tenantovi <br><br> urn:// jsou podporovány <br><br> Zástupné znaky nejsou podporovány. <br><br> Řetězce dotazu a fragmenty jsou podporovány <br><br> Maximální délka 255 znaků <br><br> Žádné omezení* počtu identifikátoruri  | Musí být globálně jedinečný <br><br> urn:// jsou podporovány <br><br> Zástupné znaky nejsou podporovány. <br><br> Řetězce dotazu a fragmenty jsou podporovány <br><br> Maximální délka 255 znaků <br><br> Žádné omezení* počtu identifikátoruri | Musí být globálně jedinečný <br><br> urn:// schémata nejsou podporována. <br><br> Zástupné znaky, fragmenty a řetězce dotazů nejsou podporovány. <br><br> Maximální délka 120 znaků <br><br> Maximálně 50 identifikátoruri |
| Certifikáty`keyCredentials`( ) | Symetrický podpisový klíč | Symetrický podpisový klíč | Šifrování a asymetrický podpisový klíč | 
| Tajemství klienta (`passwordCredentials`) | Bez omezení* | Bez omezení* | Pokud je povolena sada liveSDK: Maximálně 2 tajné klíče klientů | 
| Přesměrování identifikátorů`replyURLs`URI ( ) | Další informace najdete [v tématu Přesměrování omezení adres URL a adres URL adres URL přesměrování.](reply-url.md) | | | 
| Oprávnění rozhraní`requiredResourceAccess`API ( ) | Bez omezení* | Bez omezení* | Maximálně 30 povolených oprávnění na prostředek (např. | 
| Obory definované tímto`oauth2Permissions`rozhraním API ( ) | Maximální délka názvu oboru 120 znaků <br><br> Žádné omezení* počtu definovaných oborů | Maximální délka názvu oboru 120 znaků <br><br> Žádné omezení* počtu definovaných oborů |  Maximální délka názvu oboru 40 znaků <br><br> Bylo definováno maximálně 100 oborů | 
| Autorizované klientské aplikace (`preautorizedApplications`) | Bez omezení* | Bez omezení* | Celkem maximálně 500 <br><br> Definováno maximálně 100 klientských aplikací <br><br> Maximálně 30 oborů definovaných na klienta | 
| appRoles | Podporuje se <br> Bez omezení* | Podporuje se <br> Bez omezení* | Nepodporuje se | 
| Adresa URL odhlášení | http://localhostje povoleno <br><br> Maximální délka 255 znaků | http://localhostje povoleno <br><br> Maximální délka 255 znaků | <br><br> https://localhostje povoleno, http://localhost selže pro MSA <br><br> Maximální délka 255 znaků <br><br> Schéma HTTP není povoleno. <br><br> Zástupné znaky nejsou podporovány. | 

*Globální limit je asi 1000 položek ve všech vlastnostech kolekce na objektaplikace

## <a name="next-steps"></a>Další kroky

- Další informace o [registraci přihlášky](app-objects-and-service-principals.md)
- Další informace o [manifestu aplikace](reference-app-manifest.md)
