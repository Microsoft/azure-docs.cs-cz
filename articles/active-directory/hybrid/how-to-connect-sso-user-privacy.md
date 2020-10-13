---
title: Ochrana osobních údajů uživatelů a bezproblémové jednoduché Sign-On služby Azure AD | Microsoft Docs
description: Tento článek se zabývá Azure Active Directory (Azure AD) bez problémů s jednotným přihlašováním a dodržováním předpisů GDPR.
services: active-directory
keywords: Co je Azure AD Connect, GDPR, požadované součásti pro Azure AD, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278594"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Ochrana osobních údajů uživatelů a bezproblémové jednotné přihlašování Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled


Bezproblémové jednotné přihlašování Azure AD vytvoří následující typ protokolu, který může obsahovat osobní údaje: 

- Azure AD Connect soubory protokolu trasování.

Zvyšte ochranu osobních údajů uživatelů pro bezproblémové přihlašování dvěma způsoby:

1.  Na vyžádání rozbalte data pro osobu a odeberte data z této osoby z instalací.
2.  Zajistěte, aby žádná data nezůstala déle než 48 hodin.

Tuto druhou možnost důrazně doporučujeme, protože je snazší ji implementovat a udržovat. Pro každý typ protokolu se podívejte na následující pokyny:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranit Azure AD Connect soubory protokolu trasování

Ověřte obsah složky **%ProgramData%\AADConnect** a odstraňte obsah protokolu trasování (soubory**Trace- \* . log** ) této složky během 48 hodin od instalace nebo upgradování Azure AD Connect nebo úpravou bezproblémové konfigurace jednotného přihlašování, protože tato akce může vytvářet data, která jsou pokrytá GDPR.

>[!IMPORTANT]
>Neodstraňujte soubor **PersistedState.xml** v této složce, protože tento soubor slouží k údržbě stavu předchozí instalace Azure AD Connect a používá se v případě, že je dokončena instalace upgradu. Tento soubor nikdy nebude obsahovat žádná data o osobě a neměl by se nikdy odstranit.

Tyto soubory protokolu trasování můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete použít následující skript PowerShellu k provedení nezbytných akcí:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript do souboru s příponou. PS1 "rozšíření. Spusťte tento skript podle potřeby.

Další informace o souvisejících požadavcích Azure AD Connect GDPR najdete v [tomto článku](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Poznámka o protokolech řadičů domény

Pokud je povoleno protokolování auditu, může tento produkt generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu najdete v tomto [článku](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Další kroky

* [Přečtěte si téma zásady ochrany osobních údajů Microsoftu na webu Trust Center.](https://www.microsoft.com/trustcenter)
  - [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce