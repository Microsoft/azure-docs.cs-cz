---
title: Ochrana osobních údajů uživatelů a Azure AD bezproblémové jednotného přihlašování | Microsoft Docs
description: Tento článek se zabývá bezproblémové jednotného přihlašování k Azure Active Directory (Azure AD) a GDPR dodržování předpisů.
services: active-directory
keywords: Co je Azure AD Connect, GDPR, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a4fc779cdfb177a9817049fd7b62b0014e141ce0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34592404"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Ochrana osobních údajů uživatelů a Azure AD bezproblémové jednotné přihlašování

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled


Azure AD bezproblémové SSO vytvoří následující typ protokolu, která může obsahovat osobní údaje: 

- Soubory protokolu trasování Azure AD Connect.

Zlepšení ochrany osobních údajů uživatele pro bezproblémové jednotné přihlašování dvěma způsoby:

1.  Na žádost extrahovat data pro osoby a odstranění dat z osoba ze zařízení.
2.  Zkontrolujte, že žádná data se uchovávají za 48 hodin.

Důrazně doporučujeme druhou možnost, jako je jednodušší implementaci a údržbu. Viz následující pokyny pro každý typ protokolu:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstraňte soubory protokolů trasování Azure AD Connect

Zkontrolujte obsah **%ProgramData%\AADConnect** složku a odstraňte trasování protokolu obsah (**trasování -\*.log** soubory) této složky do 48 hodin po instalaci nebo upgradu Azure AD Connect nebo úprava konfigurace bezproblémové jednotné přihlašování, jako tato akce vytvoří předmětem GDPR data.

>[!IMPORTANT]
>Neodstraňujte **PersistedState.xml** souborů v této složce, jako jsou třeba tento soubor se používá k udržení stavu předchozí instalace služby Azure AD Connect a používá se při upgradu instalace probíhá. Tento soubor, nikdy neobsahuje žádná data o osoby a nikdy měla by být odstraněna.

Můžete zkontrolovat a odstraňte tyto soubory protokolu trasování pomocí Průzkumníka Windows, nebo můžete použít následující skript prostředí PowerShell můžete provádět potřebné akce:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript v souboru se ". PS1 "rozšíření. Spusťte tento skript podle potřeby.

Další informace o souvisejících s požadavky na Azure AD Connect GDPR najdete v tématu [v tomto článku](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Všimněte si o protokoly řadiče domény

Pokud je povoleno protokolování auditu, může tento produkt generovat protokolů zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu, přečtěte si to [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další postup
* [Zkontrolujte nastavení zásad Microsoft Privacy na Centrum zabezpečení](https://www.microsoft.com/trustcenter)
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro vyplnění žádosti o nové funkce.
