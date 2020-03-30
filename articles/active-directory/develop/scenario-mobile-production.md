---
title: Příprava webových api pro volání mobilních aplikací pro produkční prostředí | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API. (Příprava aplikací pro produkční prostředí.)
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
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132385"
---
# <a name="prepare-mobile-apps-for-production"></a>Příprava mobilních aplikací pro produkční prostředí

Tento článek obsahuje podrobnosti o tom, jak zlepšit kvalitu a spolehlivost mobilní aplikace před přesunem do produkčního prostředí.

## <a name="handle-errors"></a>Zpracování chyb

Při přípravě mobilní aplikace pro produkční prostředí může dojít k několika chybným stavům. Hlavní případy, které budete zpracovávat, jsou tiché selhání a záložní řešení interakce. Mezi další podmínky, které byste měli zvážit, patří situace bez sítě, výpadky služeb, požadavky na souhlas správce a další případy specifické pro scénář.

U každého typu knihovny Microsoft Authentication Library (MSAL) najdete ukázkový kód a obsah wikiwebu, který popisuje, jak zpracovat chybové stavy:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Zmírnění a zkoumání problémů

Chcete-li lépe diagnostikovat problémy v aplikaci, shromažďujte data. Informace o typech dat, které můžete shromažďovat, naleznete [v tématu Protokolování v aplikacích MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Zde jsou některé návrhy pro sběr dat:

- Uživatelé mohou požádat o pomoc, když mají problémy. Osvědčeným postupem je zachycování a dočasné ukládání protokolů. Zadejte umístění, kde mohou uživatelé nahrávat protokoly. MSAL poskytuje rozšíření protokolování pro zachycení podrobných informací o ověřování.

- Pokud telemetrie je k dispozici, povolte ji prostřednictvím MSAL shromažďovat data o tom, jak se uživatelé přihlásit k vaší aplikaci.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Další ukázky najdete v [tématu Desktopové a mobilní aplikace pro veřejné klienty](sample-v2-code.md#desktop-and-mobile-public-client-apps).
