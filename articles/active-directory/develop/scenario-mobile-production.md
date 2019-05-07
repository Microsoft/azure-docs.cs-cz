---
title: Mobilní aplikace, že volání webových rozhraní API (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Informace o sestavení mobilní aplikace, která volá webové rozhraní API (Přesun do produkčního prostředí)
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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074947"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilní aplikace, která volá webové rozhraní API – Přesun do produkčního prostředí

Tento článek poskytuje podrobné informace o vylepšení kvality a spolehlivost vaší aplikace pro přesun do produkčního prostředí.

## <a name="handling-errors-in-mobile-applications"></a>Zpracování chyb v mobilních aplikacích.

Různé toky, které jsme zatím zvýrazněné existuje široká škála chybové stavy, které mohou nastat. Primární scénáře pro zpracování je bezobslužné selhání a nouzového řešení ověření interakce. Existují další podmínky, které byste měli také zvážit pro produkční prostředí, včetně situací žádné sítě, výpadky služeb, vyžaduje souhlas správce a ostatních případech specifické pro scénář.

Každý knihovna MSAL je ukázkový kód a wiki obsah, který se věnuje hlouběji do zpracování těchto podmínek.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Knihovna MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Zmírnění a zkoumání problémů

Shromažďování dat vám pomůže diagnostikovat problémy s aplikace. Další podrobnosti o typu dat můžete shromažďovat, najdete v článku wikiwebu každý MSAL platformy.

- Uživatelé můžou požádat o pomoc při zjištění problému. Osvědčeným postupem je zachytit a dočasně ukládat protokoly, uživatelům umožní nahrát je někde. Knihovna MSAL poskytuje protokolování rozšíření budou zachyceny podrobné informace o ověřeného
- Pokud je k dispozici, povolte telemetrii prostřednictvím MSAL pro shromažďování dat o tom, jak se uživatel přihlašuje vaší aplikace.

## <a name="testing-your-app"></a>Testování vaší aplikace

Nezapomeňte otestovat aplikaci [integrace kontrolním seznamu](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Další postup

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
