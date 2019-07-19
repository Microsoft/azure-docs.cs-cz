---
title: Mobilní aplikace, která volá webové rozhraní API – konfigurace kódu aplikace | Platforma Microsoft identity
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (konfigurace kódu aplikace).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326126"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilní aplikace, která volá webová rozhraní API – registrace aplikace

Tento článek obsahuje pokyny k registraci aplikace pro vytvoření mobilní aplikace.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů podporované v mobilních aplikacích závisí na zkušenostech, které chcete povolit, a na uživatele, na které vaše aplikace cílí.

## <a name="platform-configuration-and-redirect-uris"></a>Konfigurace platformy a identifikátory URI pro přesměrování  

Při vytváření mobilní aplikace je nejnejdůležitějším krokem registrace identifikátor URI přesměrování. To lze nastavit pomocí [konfigurace platformy v okně ověřování](https://aka.ms/MobileAppReg).

Toto prostředí umožní vaší aplikaci získat jednotné přihlašování (SSO) prostřednictvím Microsoft Authenticator (a Portál společnosti Intune na Androidu) a taky podporovat zásady správy zařízení.

Pokud upřednostňujete ruční konfiguraci identifikátoru URI přesměrování, můžete to provést prostřednictvím manifestu aplikace. Doporučený formát je následující:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hodnota hash podpisu Androidu se dá vygenerovat pomocí vydaných nebo ladicích kláves pomocí příkazu nástroje.

## <a name="api-permissions"></a>Oprávnění API

Mobilní aplikace volají rozhraní API jménem přihlášeného uživatele. Vaše aplikace potřebuje požádat o delegovaná oprávnění, označovaná také jako obory. V závislosti na požadovaném prostředí lze to provést staticky prostřednictvím Azure Portal nebo dynamicky v době běhu. Statická registrace oprávnění umožňuje správcům snadno schválit vaši aplikaci a doporučuje se.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu](scenario-mobile-acquire-token.md)
