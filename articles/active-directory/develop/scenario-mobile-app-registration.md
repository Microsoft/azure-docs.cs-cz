---
title: Mobilní aplikace, že volání webových rozhraní API – konfigurace kódu aplikace | Platforma identit Microsoft
description: Další informace o vytváření mobilních aplikací, že volání webových rozhraní API (konfigurace kódu aplikace)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962406"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilní aplikace, že volání webového rozhraní API – registrace aplikace

Tento článek obsahuje pokyny registrace aplikace pro vytváření mobilních aplikací.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů, které jsou podporovány v mobilních aplikacích závisí na prostředí, které chcete povolit a uživatele, které cílí vaše aplikace.

## <a name="platform-configuration-and-redirect-uris"></a>Konfigurace platformy a identifikátory URI pro přesměrování  

Při vytváření mobilních aplikací, je nejdůležitější krok registrace identifikátor URI pro přesměrování. To lze nastavit až [konfiguraci platformy tak v okně ověřování](https://aka.ms/MobileAppReg).

Toto prostředí vám umožní získat jednotné přihlašování (SSO) prostřednictvím Microsoft Authenticator (a portál společnosti Intune v Androidu) a také podpora zásad správy zařízení v aplikaci.

Pokud chcete ručně konfigurovat identifikátor URI pro přesměrování, udělat prostřednictvím Manifest aplikace. Doporučený formát je následující:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hodnota hash podpisu s Androidem můžete vygenerovat pomocí klíče vydání nebo ladícího do příkazu KeyTool.

## <a name="api-permissions"></a>Oprávnění k rozhraní API

Mobilní aplikace volat rozhraní API jménem přihlášeného uživatele. Vaše aplikace potřebuje požadovat delegovaná oprávnění, také označuje jako obory. V závislosti na požadované prostředí to můžete udělat staticky prostřednictvím webu Azure portal nebo dynamicky za běhu. Staticky registrace oprávnění umožňuje správcům snadno schválení vaší aplikace a doporučuje se.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získávání tokenu](scenario-mobile-acquire-token.md)
