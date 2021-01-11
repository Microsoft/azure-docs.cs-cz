---
title: Požadavky UWP (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s důležitými informacemi o použití Univerzální platforma Windows (UWP) s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6451368baf9c047f0318eb74d53ffac075d4a184
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063446"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Důvody pro použití Univerzální platforma Windows s MSAL.NET
Vývojáři aplikací, které používají Univerzální platforma Windows (UWP) se MSAL.NET, by měly zvážit koncepty tohoto článku.

## <a name="the-usecorporatenetwork-property"></a>Vlastnost UseCorporateNetwork
Na platformě prostředí Windows Runtime (WinRT) `PublicClientApplication` má vlastnost Boolean `UseCorporateNetwork` . Tato vlastnost umožňuje Windows 8.1 aplikacím a aplikacím UWP těžit z integrovaného ověřování systému Windows (IWA), pokud je uživatel přihlášený k účtu, který má tenanta federovaného Azure Active Directory (Azure AD). Uživatelům, kteří jsou přihlášení k operačnímu systému, mohou také používat jednotné přihlašování (SSO). Když nastavíte `UseCorporateNetwork` vlastnost, MSAL.NET použije nástroj Web Authentication broker (WAB).

> [!IMPORTANT]
> Nastavení `UseCorporateNetwork` vlastnosti na hodnotu true předpokládá, že vývojář aplikace POVOLIL IWA v aplikaci. Povolení IWA:
> - V aplikaci UWP `Package.appxmanifest` na kartě **Možnosti** Povolte následující možnosti:
>   - **Podnikové ověřování**
>   - **Privátní sítě (klient & Server)**
>   - **Sdílený uživatelský certifikát**

Služba IWA není ve výchozím nastavení povolená, protože Microsoft Store vyžaduje vysokou úroveň ověřování před tím, než akceptuje aplikace, které požadují možnosti podnikového ověřování nebo sdílených uživatelských certifikátů. Ne všichni vývojáři chtějí tuto úroveň ověřování provést.

Základní implementace WAB na platformě UWP nefunguje správně v podnikových scénářích, kde je povolený podmíněný přístup. Uživatelům se při pokusu o přihlášení pomocí Windows Hello zobrazí příznaky tohoto problému. Když se uživateli zobrazí výzva k výběru certifikátu:

- Certifikát pro PIN kód se nenašel.
- Jakmile uživatel zvolí certifikát, nezobrazí se jim výzva k zadání kódu PIN.

Tomuto problému se můžete vyhnout tak, že použijete alternativní metodu, jako je uživatelské jméno – heslo a telefonní ověřování, ale prostředí není dobré.

## <a name="troubleshooting"></a>Řešení potíží

Někteří zákazníci oznámili následující chybu při přihlašování v konkrétních podnikových prostředích, ve kterých ví, že mají připojení k Internetu a že připojení funguje s veřejnou sítí.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Tomuto problému se můžete vyhnout tím, že zajistěte, aby se v WAB (podkladová součást systému Windows) nastavila privátní síť. Můžete to udělat nastavením klíče registru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Další informace najdete v tématu [zprostředkovatel webového ověřování – Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Další kroky
Následující ukázky poskytují další informace.

Ukázka | Platforma | Description 
|------ | -------- | -----------|
|[Active-Directory-dotnet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Klientská aplikace UWP, která používá MSAL.NET. Přistupuje k Microsoft Graph pro uživatele, který se ověřuje pomocí koncového bodu Azure AD 2,0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Aplikace Xamarin Forms, která ukazuje, jak používat MSAL k ověřování osobních účtů Microsoft a Azure AD prostřednictvím platformy Microsoft Identity Platform. Také ukazuje, jak získat přístup k Microsoft Graph a zobrazuje výsledný token. <br>![Diagram, který ukazuje, jak používat MSAL k ověřování osobních účtů Microsoft a Azure AD prostřednictvím platformy Microsoft Identity Platform.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
