---
title: Úvahy UPW (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o aspektech používání univerzální platformy Windows (UPW) s Knihovnou ověřování Microsoft pro rozhraní .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132530"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Důležité informace o používání univerzální platformy Windows s MSAL.NET
Vývojáři aplikací, které používají univerzální platformu Windows (UPW) s MSAL.NET by měl zvážit koncepty tento článek představuje.

## <a name="the-usecorporatenetwork-property"></a>Vlastnost UseCorporateNetwork
Na platformě Windows Runtime (WinRT) `PublicClientApplication` má `UseCorporateNetwork`boolean vlastnost . Tato vlastnost umožňuje aplikacím pro Windows 8.1 a APLIKACÍM UPW využívat integrované ověřování systému Windows (IWA), pokud je uživatel přihlášen k účtu, který má federovaného klienta Azure Active Directory (Azure AD). Uživatelé, kteří jsou přihlášeni k operačnímu systému můžete také použít jednotné přihlašování (SSO). Když nastavíte `UseCorporateNetwork` vlastnost, MSAL.NET používá zprostředkovatele webového ověřování (WAB).

> [!IMPORTANT]
> Nastavení `UseCorporateNetwork` vlastnosti na hodnotu true předpokládá, že vývojář aplikace povolil iwa v aplikaci. Povolení IWA:
> - V aplikaci `Package.appxmanifest`UPW povolte na kartě **Možnosti** následující funkce:
>   - **Podnikové ověřování**
>   - **Privátní sítě (server & klienta)**
>   - **Certifikát sdíleného uživatele**

IWA není ve výchozím nastavení povolena, protože microsoft store vyžaduje vysokou úroveň ověření před tím, než přijme aplikace, které požadují možnosti podnikového ověřování nebo sdílených uživatelských certifikátů. Ne všichni vývojáři chtějí tuto úroveň ověření provést.

Na platformě UPW základní implementace WAB nefunguje správně v podnikových scénářích, kde je povolen podmíněný přístup. Uživatelům se zobrazí příznaky tohoto problému při pokusu o přihlášení pomocí Windows Hello. Když je uživatel vyzván k výběru certifikátu:

- Certifikát pro KÓD PIN nebyl nalezen.
- Poté, co uživatel vybere certifikát, nebudou vyzváni k zadání KÓDU PIN.

Můžete se pokusit vyhnout se tomuto problému pomocí alternativní metody, jako je uživatelské jméno-heslo a ověřování telefonu, ale prostředí není dobré.

## <a name="troubleshooting"></a>Řešení potíží

Někteří zákazníci ohlásili následující chybu přihlášení v konkrétních podnikových prostředích, ve kterých vědí, že mají připojení k Internetu a že připojení funguje s veřejnou sítí.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Tomuto problému se můžete vyhnout tím, že se ujistíte, že WAB (základní součást systému Windows) umožňuje privátní síť. Můžete to udělat nastavením klíče registru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Další informace naleznete v [tématu Zprostředkovatel webového ověřování - Šumař](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Další kroky
Následující ukázky poskytují další informace.

Ukázka | Platforma | Popis 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Klientská aplikace UPW, která používá MSAL.NET. Přistupuje k Microsoft Graphu pro uživatele, který se ověřuje pomocí koncového bodu Azure AD 2.0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-nativní-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UPW | Jednoduchá aplikace Xamarin Forms, která ukazuje, jak používat MSAL k ověření osobních účtů Microsoftu a Azure AD prostřednictvím koncového bodu Azure AD 2.0. Také ukazuje, jak získat přístup k aplikaci Microsoft Graph a zobrazuje výsledný token. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
