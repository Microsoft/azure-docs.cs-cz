---
title: Univerzální platforma Windows hlediska (Microsoft Authentication Library for .NET) | Azure
description: Přečtěte si o konkrétních otázkách při použití Univerzální platforma Windows s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 263264742088a0012ea844946e13cffbab634b29
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532490"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET s ohledem na konkrétní Univerzální platforma Windows
U UWP máte několik důležitých informací, které je potřeba vzít v úvahu při používání MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Vlastnost UseCorporateNetwork
V platformě `PublicClientApplication` WinRT má následující logickou vlastnost ``UseCorporateNetwork``. Tato vlastnost umožňuje aplikacím pro Win 8.1 a UWP těžit z integrovaného ověřování systému Windows (a proto SSO s přihlášeným uživatelem s operačním systémem), pokud je uživatel přihlášený pomocí účtu ve federovaném tenantovi služby Azure AD. Když nastavíte tuto vlastnost, MSAL.NET využívá WAB (Web Authentication Broker).

> [!IMPORTANT]
> Nastavení této vlastnosti na hodnotu true předpokládá, že vývojář aplikace povolil v aplikaci integrované ověřování systému Windows (IWA). Pro toto:
> - V nástroji pro vaši aplikaci UWP povolte na kartě Možnosti následující možnosti: ``Package.appxmanifest``
>   - Podnikové ověřování
>   - Privátní sítě (klient & Server)
>   - Sdílený uživatelský certifikát

Služba IWA není ve výchozím nastavení povolená, protože aplikace požadující podnikové ověřování nebo uživatelské certifikáty sdílené uživatele vyžadují, aby se do Windows Storu přijala vyšší úroveň ověřování, a ne všichni vývojáři můžou chtít vyšší výkon. úroveň ověřování.

Základní implementace na platformě UWP (WAB) nefunguje správně v podnikových scénářích, ve kterých je povolený podmíněný přístup. Příznakem je, že se uživatel pokusí přihlásit pomocí Windows Hello a je navržený pro výběr certifikátu, ale:

- certifikát pro PIN kód se nenašel,
- nebo si ho uživatel zvolí, ale nikdy se mu nezobrazuje výzva k zadání kódu PIN.

Alternativním řešením je použití alternativní metody (uživatelské jméno/heslo + ověřování pro telefon), ale toto prostředí není dobré.

## <a name="troubleshooting"></a>Řešení potíží

Někteří zákazníci oznámili, že v některých specifických podnikových prostředích došlo k následující chybě při přihlašování:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

vzhledem k tomu, že ví, že mají připojení k Internetu a kteří pracují s veřejnou sítí.

Alternativním řešením je zajistit, aby systém WAB (podkladová součást systému Windows) umožňoval privátní síť. Můžete to udělat nastavením klíče registru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Podrobnosti najdete v tématu [zprostředkovatel webového ověřování – Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Další postup
Další podrobnosti jsou k dispozici v následujících ukázkách:

Ukázka | Platforma | Popis 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Klientská aplikace Univerzální platforma Windows využívající msal.net a přístup k Microsoft Graph pro uživatele, který ověřuje pomocí koncového bodu Azure AD v 2.0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak pomocí MSAL ověřovat MSA a Azure AD prostřednictvím koncového bodu AAD v 2.0 a přistupovat k Microsoft Graph s výsledným tokenem. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
