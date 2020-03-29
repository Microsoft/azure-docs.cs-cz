---
title: Migrace do knihovny ověřování společnosti Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou ověřování Microsoftu (MSAL) a Azure AD Authentication Library (ADAL) a jak migrovat do MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164929"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrace aplikací do knihovny ověřování společnosti Microsoft (MSAL)

Knihovna ověřování Microsoftu (MSAL) a Azure AD Authentication Library (ADAL) se používají k ověřování entit Azure AD a vyžádání tokenů z Azure AD. Až dosud většina vývojářů spolupracovala s platformou Azure AD pro vývojáře (v1.0) k ověření identit Azure AD (pracovní a školní účty) vyžádáním tokenů pomocí Azure AD Authentication Library (ADAL). Použití msal:

- Můžete ověřit širší sadu identit Microsoftu (identitazure a a účtů Microsoft a sociálních a místních účtů prostřednictvím Azure AD B2C), protože používá koncový bod platformy identit Microsoftu.
- Vaši uživatelé získají nejlepší prostředí pro jednotné přihlašování.
- Vaše aplikace může povolit přírůstkový souhlas a podpora podmíněného přístupu je jednodušší.
- Můžete těžit z inovací.

**MSAL je nyní doporučená knihovna ověření pro použití s platformou identit microsoftu**. V ADAL nebudou implementovány žádné nové funkce. Úsilí je zaměřeno na zlepšení MSAL.

Následující články popisují rozdíly mezi knihovnami MSAL a ADAL a pomáhají vám migrovat do msal:
- [Migrace na MSAL.NET](msal-net-migration.md)
- [Migrace na MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrace na MSAL.Android](migrate-android-adal-msal.md)
- [Migrace na MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrace na MSAL pro Python](migrate-python-adal-msal.md)
- [Migrace na MSAL pro Javu](migrate-adal-msal-java.md)
- [Migrace aplikací pro Xamarin pomocí zprostředkovatelů do MSAL.NET](msal-net-migration-ios-broker.md)
