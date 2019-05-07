---
title: Informace o univerzální platformě Windows (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Další informace o konkrétní aspekty při používání univerzální platformu Windows s knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076507"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Aspekty univerzální platformu Windows pomocí MSAL.NET
V Xamarin pro iOS je třeba mít na paměti, které musí vzít v úvahu při použití MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Vlastnost UseCorporateNetwork
Na platformě WinRT `PublicClientApplication` má následující logickou vlastnost ``UseCorporateNetwork``. Tato vlastnost umožňuje aplikacím Win8.1 a UPW těžit z integrované ověřování Windows (a tedy jednotné přihlašování s uživatele přihlášeného s operačním systémem) Pokud je uživatel přihlášený pomocí účtu ve federované Azure AD tenanta. Využívá se při WAB (zprostředkovatel webového ověření). 

> [!IMPORTANT]
> Nastavení této vlastnosti na hodnotu true se předpokládá, že vývojář aplikace má povolené integrované ověřování Windows (IWA) v aplikaci. Pro toto:
> - V ``Package.appxmanifest`` pro vaši aplikaci UPW v **možnosti** kartu, povolte následující možnosti:
>   - Podnikové ověřování
>   - Privátní sítě (klient a Server)
>   - Sdílené uživatelský certifikát

IWA není ve výchozím nastavení povolené, protože aplikace vyžadující funkce podnikové ověřování nebo sdílené uživatelské certifikáty vyžadovat vyšší úroveň ověření na přijetí do Windows Store, a ne všechny vývojáře staví na provedení vyšší úroveň ověřování. 

Základní implementaci na platformě UPW (WAB) v podnikové scénáře, ve kterém byl povolený podmíněný přístup nefunguje správně. Příznakem je, že uživatel se pokusí přihlásit pomocí Windows hello a je navržené k zvolte certifikát, ale certifikát pro kód pin nebyl nalezen, nebo pokud uživatel vybere, ale nikdy zobrazit výzva k zadání kódu Pin. Alternativní řešení, je použít alternativní způsob (uživatelského jména a hesla a telefonního ověřování), ale není vhodné prostředí. 

## <a name="next-steps"></a>Další postup
Další podrobnosti jsou uvedeny v následující ukázky:

Ukázka | Platforma | Popis 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Klientská aplikace univerzální platformy Windows pomocí msal.net, přístup k Microsoft Graphu k ověřování uživatelů pomocí koncového bodu Azure AD v2.0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms která ukazuje použití MSAL k ověřování MSA a Azure AD prostřednictvím koncového bodu v2.0 AAD a přístup k Microsoft Graphu pomocí výsledný token. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|