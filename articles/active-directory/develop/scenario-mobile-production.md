---
title: Přesunutí mobilní aplikace volající webová rozhraní API do produkčního prostředí – Microsoft Identity Platform | Azure
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (přesunout do produkčního prostředí).
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
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e267b2f398480da6949a4c85133342aad5c2ba7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919898"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilní aplikace, která volá webová rozhraní API – přesun do produkčního prostředí

Tento článek poskytuje podrobné informace o tom, jak zlepšit kvalitu a spolehlivost vaší aplikace před jejich přesunutím do produkčního prostředí.

## <a name="handling-errors-in-mobile-applications"></a>Zpracování chyb v mobilních aplikacích

V této chvíli může v aplikaci dojít k několika chybovým podmínkám. Hlavními scénáři, které je potřeba zpracovat, jsou tiché selhání a záložní interakce. Další podmínky, které byste měli zvážit v produkčním prostředí, zahrnují situace bez sítě, výpadky služeb, požadavky na souhlas správce a další případy specifické pro konkrétní scénář.

Každá knihovna MSAL má vzorový kód a obsah wiki, který popisuje, jak tyto podmínky zpracovat:

- [MSAL wiki pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki pro iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Zmírnění a zkoumání problémů

Chcete-li diagnostikovat problémy ve vaší aplikaci, pomůže shromažďovat data. Informace o druzích dat, která můžete shromažďovat, najdete v tématu MSAL Platform wiki.

- Uživatelé můžou požádat o pomoc, když nastanou problémy. Osvědčeným postupem je zachytit a dočasně ukládat protokoly a zadat umístění, kam je uživatelé můžou odeslat. MSAL poskytuje rozšíření protokolování pro zachycení podrobných informací o ověřování.
- Pokud je k dispozici, povolte telemetrii prostřednictvím MSAL ke shromáždění dat o tom, jak se uživatelé přihlásí k vaší aplikaci.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Vyzkoušejte další ukázky, které jsou k dispozici v [ukázkách | Desktopové a mobilní veřejné klientské aplikace](sample-v2-code.md#desktop-and-mobile-public-client-apps)
