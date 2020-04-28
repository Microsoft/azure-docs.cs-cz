---
title: Migrace do knihovny Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi Microsoft Authentication Library (MSAL) a Azure AD Authentication Library (ADAL) a postupem migrace na MSAL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164929"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrace aplikací do knihovny Microsoft Authentication Library (MSAL)

K ověřování entit Azure AD a žádosti o tokeny od Azure AD se používají knihovny Microsoft Authentication Library (MSAL) i knihovna ADAL (Azure AD Authentication Library). Až do té doby se většina vývojářů pracovala s Azure AD for Developers Platform (verze 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že vyžaduje tokeny pomocí služby Azure AD Authentication Library (ADAL). Pomocí MSAL:

- K ověření širší sady identit Microsoftu (identity Azure AD a účtů Microsoft a sociálních i místních účtů prostřednictvím Azure AD B2C) můžete používat koncový bod platformy Microsoft Identity Platform.
- Vaši uživatelé získají nejlepší možnosti jednotného přihlašování.
- Vaše aplikace může povolit postupný souhlas a podpora podmíněného přístupu je jednodušší.
- Přináší vám výhody inovace.

**MSAL je teď doporučovanou knihovnou ověřování, která se má použít s platformou Microsoft Identity**. V ADAL nebudou implementovány žádné nové funkce. Úsilí se zaměřuje na zlepšení MSAL.

Následující články popisují rozdíly mezi knihovnami MSAL a ADAL a umožňují migraci na MSAL:
- [Migrace na MSAL.NET](msal-net-migration.md)
- [Migrace na MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrace na MSAL.Android](migrate-android-adal-msal.md)
- [Migrace na MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrace na MSAL pro Python](migrate-python-adal-msal.md)
- [Migrace na MSAL pro Javu](migrate-adal-msal-java.md)
- [Migrace aplikací pro Xamarin pomocí zprostředkovatelů do MSAL.NET](msal-net-migration-ios-broker.md)
