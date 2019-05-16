---
title: Mobilní aplikace, že volání webových rozhraní API (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Další informace o vytváření mobilních aplikací, že volání webových rozhraní API (Přesun do produkčního prostředí)
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
ms.openlocfilehash: 5cc8b7c86495c2a60b07bef0a825e3872f787520
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550411"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Mobilní aplikace, která volá webové rozhraní API – Přesun do produkčního prostředí

Tento článek obsahuje podrobnosti o tom, jak zlepšit kvalitu a spolehlivost vaší aplikace před přesunutím do produkčního prostředí.

## <a name="handling-errors-in-mobile-applications"></a>Zpracování chyb v mobilních aplikacích.

Počet chybové stavy můžete ve vaší aplikaci vyskytnout v tomto okamžiku. Hlavní scénáře pro zpracování jsou tiché selhání a náhrad na interakci ze strany. Další podmínky, které byste měli zvážit pro produkční prostředí patří situace žádné sítě, výpadky služeb, požadavky pro vyjádření souhlasu správce a ostatních případech specifické pro scénář.

Každý knihovna MSAL obsahuje ukázkový kód a wiki obsah, který popisuje způsob zpracování těchto podmínek:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Knihovna MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Zmírnění a zkoumání problémů

Chcete-li diagnostikovat problémy ve vaší aplikaci, pomáhá shromažďovat data. Informace o typech dat můžete shromažďovat, najdete v článku wikiwebů platforma MSAL.

- Uživatelé můžou požádat o pomoc, když narazí na problémy. Osvědčeným postupem je zachytit a dočasně ukládat protokoly a zadejte umístění, kde uživatelé můžou nahrávat. Knihovna MSAL poskytuje protokolování rozšíření budou zachyceny podrobné informace o ověřování.
- Pokud je k dispozici, povolte telemetrická data prostřednictvím MSAL pro shromažďování dat o jak jsou uživatelé přihlášení do vaší aplikace.

## <a name="next-steps"></a>Další postup

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
