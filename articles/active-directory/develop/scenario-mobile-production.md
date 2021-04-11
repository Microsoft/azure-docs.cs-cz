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
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675953"
---
# <a name="prepare-mobile-apps-for-production"></a>Příprava mobilních aplikací na produkci

Tento článek poskytuje podrobné informace o tom, jak zlepšit kvalitu a spolehlivost mobilní aplikace, než ji přesunete do produkčního prostředí.

## <a name="handle-errors"></a>Ošetření chyb

Při přípravě mobilní aplikace na produkční prostředí mohou nastat některé chybové podmínky. Hlavní případy, které obdržíte, jsou tiché selhání a záložní interakce. Mezi další podmínky, které byste měli zvážit, patří situace bez sítě, výpadky služeb, požadavky na souhlas správce a další případy, které jsou specifické pro konkrétní scénář.

Pro každý typ knihovny Microsoft Authentication Library (MSAL) můžete najít ukázkový kód a obsah wiki, který popisuje, jak zpracovat chybové podmínky:

- [MSAL wiki pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki pro iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

Další ukázky najdete v tématu [desktopové a mobilní veřejné klientské aplikace](sample-v2-code.md#desktop-and-mobile-public-client-apps).