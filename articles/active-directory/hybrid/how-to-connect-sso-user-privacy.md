---
title: Ochrana osobních údajů uživatelů a bezproblémové jednotné přihlašování azure a služby Azure AD | Dokumenty společnosti Microsoft
description: Tento článek se zabývá Azure Active Directory (Azure AD) bezproblémové jednotné přihlašovač a dodržování GDPR.
services: active-directory
keywords: co je Azure AD Connect, GDPR, požadované komponenty pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242108"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Ochrana osobních údajů uživatelů a bezproblémové jednotné přihlašování Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled


Azure AD Bezproblémové jednotné přihlašování vytvoří následující typ protokolu, který může obsahovat osobní data: 

- Soubory protokolu trasování služby Azure AD Connect.

Zlepšete ochranu osobních údajů uživatelů pro bezproblémové jednotné přihlašování dvěma způsoby:

1.  Na požádání extrahujte data pro osobu a odstraňte data z této osoby z zařízení.
2.  Ujistěte se, že žádná data nejsou uchovávána po 48 hodinách.

Důrazně doporučujeme druhou možnost, protože je snadnější implementovat a udržovat. Viz následující pokyny pro každý typ protokolu:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranění souborů protokolu trasování služby Azure AD Connect

Zkontrolujte obsah složky **%ProgramData%\AADConnect** a odstraňte obsah protokolu trasování **(trace-\*.log** files) této složky do 48 hodin od instalace nebo upgradu služby Azure AD Connect nebo po úpravě bezproblémové konfigurace jednotného přihlašování, protože tato akce může vytvářet data pokrytá nařízením GDPR.

>[!IMPORTANT]
>Neodstraňujte soubor **PersistedState.xml** v této složce, protože tento soubor se používá k udržení stavu předchozí instalace služby Azure AD Connect a používá se při dokončení instalace upgradu. Tento soubor nikdy nebude obsahovat žádná data o osobě a nikdy by neměl být odstraněn.

Tyto soubory protokolu trasování můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete k provedení nezbytných akcí použít následující skript prostředí PowerShell:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript do souboru s ". PS1" rozšíření. Podle potřeby spusťte tento skript.

Další informace o souvisejících požadavcích gdpr azure a apřipojení na jdete v [tomto článku](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Poznámka k protokolům řadiče domény

Pokud je protokolování auditu povoleno, může tento produkt generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu naleznete v tomto [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další kroky

* [Projděte si zásady ochrany osobních údajů společnosti Microsoft v Centru zabezpečení](https://www.microsoft.com/trustcenter)
  - [**Poradce při potížích**](tshoot-connect-sso.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Pro podání nových žádostí o funkce.