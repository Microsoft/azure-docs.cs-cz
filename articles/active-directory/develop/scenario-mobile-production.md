---
title: Příprava mobilní aplikace – volání webových rozhraní API pro produkci | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API. (Příprava aplikací pro produkční prostředí.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121030"
---
# <a name="prepare-mobile-apps-for-production"></a>Příprava mobilních aplikací na produkci

Tento článek poskytuje podrobné informace o tom, jak zlepšit kvalitu a spolehlivost mobilní aplikace, než ji přesunete do produkčního prostředí.

## <a name="handle-errors"></a>Ošetření chyb

Při přípravě mobilní aplikace na produkční prostředí mohou nastat některé chybové podmínky. Hlavní případy, které obdržíte, jsou tiché selhání a záložní interakce. Mezi další podmínky, které byste měli zvážit, patří situace bez sítě, výpadky služeb, požadavky na souhlas správce a další případy, které jsou specifické pro konkrétní scénář.

Pro každý typ knihovny Microsoft Authentication Library (MSAL) můžete najít ukázkový kód a obsah wiki, který popisuje, jak zpracovat chybové podmínky:

- [MSAL wiki pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki pro iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Zmírnění a zkoumání problémů

Pro lepší diagnostiku problémů ve vaší aplikaci Shromážděte data. Informace o typech dat, která můžete shromažďovat, najdete v tématu [protokolování v aplikacích MSAL](./msal-logging.md).

Zde jsou některé návrhy pro shromažďování dat:

- Uživatelé můžou požádat o pomoc, když budou mít problémy. Osvědčeným postupem je zaznamenat a dočasně ukládat protokoly. Zadejte umístění, kam mohou uživatelé nahrávat protokoly. MSAL poskytuje rozšíření protokolování pro zachycení podrobných informací o ověřování.

- Pokud je telemetrie k dispozici, povolte ji prostřednictvím MSAL, abyste mohli shromažďovat data o tom, jak se uživatelé přihlásí k vaší aplikaci.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Další ukázky najdete v tématu [desktopové a mobilní veřejné klientské aplikace](sample-v2-code.md#desktop-and-mobile-public-client-apps).