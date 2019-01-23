---
title: Ochrana osobních údajů uživatelů a Azure AD bezproblémové jednotné přihlašování | Dokumentace Microsoftu
description: Tento článek se zabývá bezproblémového jednotného přihlašování Azure Active Directory (Azure AD) a nařízení GDPR dodržování předpisů.
services: active-directory
keywords: Co je Azure AD Connect, nařízení GDPR, povinné součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5035fb8d8d144114835fb1202dc9c639b9e5a8e3
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461124"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Ochrana osobních údajů uživatelů a Azure AD bezproblémové jednotné přihlašování

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled


Azure bezproblémového jednotného přihlašování AD vytvoří následující typ protokolu, který může obsahovat osobní údaje: 

- Soubory protokolu trasování Azure AD Connect.

Vylepšení ochrany osobních údajů uživatele pro bezproblémové jednotné přihlašování dvěma způsoby:

1.  Na vyžádání extrahovat data pro osobu a odstranění dat z této osoby ze zařízení.
2.  Ujistěte se, že žádná data se uchovávají za 48 hodin.

Důrazně doporučujeme druhou možnost, jak usnadňuje implementaci a údržbu. Viz následující pokyny pro jednotlivé typy protokolů:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranit soubory protokolu trasování služby Azure AD Connect

Zkontrolujte obsah **%ProgramData%\AADConnect** složku a odstraňte trasování protokolu obsah (**trasování -\*.log** soubory) této složky do 48 hodin od instalace nebo upgrade služby Azure AD Connect nebo změna konfigurace bezproblémového jednotného přihlašování, jako tuto akci mohou vytvořit data vztahuje nařízení GDPR.

>[!IMPORTANT]
>Neodstraňovat **PersistedState.xml** soubor v této složce, a tento soubor se používá k uchování stavu předchozí instalace služby Azure AD Connect se používá, když se provádí upgrade instalace. Tento soubor, nikdy neobsahuje žádná data o osobě a měl by být nikdy odstraněn.

Můžete zkontrolovat a odstranit tyto soubory protokolu trasování pomocí Průzkumníka Windows, nebo můžete použít následující skript prostředí PowerShell provádět potřebné akce:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript v souboru se ". PS1 "rozšíření. Spusťte tento skript podle potřeby.

Další informace o souvisejících požadavky GDPR připojení Azure AD, najdete v článku [v tomto článku](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Mějte na paměti o protokolech řadiče domény

Pokud je povoleno protokolování auditu, tento produkt může generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu, najdete v tomto [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další postup
* [Projděte si zásady Microsoft Privacy Trust Center](https://www.microsoft.com/trustcenter)
  - [**Řešení potíží s** ](tshoot-connect-sso.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
  - [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – k podání žádostí o nové funkce.
