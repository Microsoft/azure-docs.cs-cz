---
title: Předávací ověřování služby Azure Active Directory a ochrana osobních údajů uživatele | Dokumenty společnosti Microsoft
description: Tento článek se zabývá předávacím ověřováním Azure Active Directory (Azure AD) a dodržováním předpisů GDPR.
services: active-directory
keywords: Předávací ověřování Azure AD Connect, GDPR, požadované komponenty pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931006"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Ochrana osobních údajů uživatelů a předávací ověřování služby Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled

Předávací ověřování Azure AD vytvoří následující typ protokolu, který může obsahovat osobní data:

- Soubory protokolu trasování služby Azure AD Connect.
- Soubory protokolu trasování agenta ověřování.
- Soubory protokolu událostí systému Windows.

Zlepšete ochranu osobních údajů uživatelů pro předávací ověřování dvěma způsoby:

1.  Na požádání extrahujte data pro osobu a odstraňte data z této osoby z zařízení.
2.  Ujistěte se, že žádná data nejsou uchovávána po 48 hodinách.

Důrazně doporučujeme druhou možnost, protože je snadnější implementovat a udržovat. Níže jsou uvedeny pokyny pro každý typ protokolu:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranění souborů protokolu trasování služby Azure AD Connect

Zkontrolujte obsah složky **%ProgramData%\AADConnect** a odstraňte obsah protokolu trasování **(trace-\*.log** files) této složky do 48 hodin od instalace nebo upgradu služby Azure AD Connect nebo po úpravě předávací konfigurace ověřování, protože tato akce může vytvářet data pokrytá nařízením GDPR.

>[!IMPORTANT]
>Neodstraňujte soubor **PersistedState.xml** v této složce, protože tento soubor se používá k udržení stavu předchozí instalace služby Azure AD Connect a používá se při dokončení instalace upgradu. Tento soubor nikdy nebude obsahovat žádná data o osobě a nikdy by neměl být odstraněn.

Tyto soubory protokolu trasování můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete k provedení nezbytných akcí použít následující skript prostředí PowerShell:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript do souboru s ". PS1" rozšíření. Podle potřeby spusťte tento skript.

Další informace o souvisejících požadavcích gdpr azure a apřipojení na jdete v [tomto článku](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Odstranit protokoly událostí agenta ověřování

Tento produkt může také vytvářet **protokoly událostí systému Windows**. Chcete-li se dozvědět více, přečtěte si [tento článek](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Chcete-li zobrazit protokoly související s předávacím ověřovacím agentem, otevřete na serveru aplikaci **Prohlížeč událostí** a zkontrolujte v části **Protokoly aplikací a služeb\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Odstranit soubory protokolu trasování agenta ověřování

Měli byste pravidelně kontrolovat obsah **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** a odstranit obsah této složky každých 48 hodin. 

>[!IMPORTANT]
>Pokud je spuštěna služba Agent ověřování, nebudete moci odstranit aktuální soubor protokolu ve složce. Před pokusem o další pokus zastavte službu. Chcete-li se vyhnout chybám přihlášení uživatele, měli byste již nakonfigurovat předávací ověřování pro [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Tyto soubory můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete provést nezbytné akce pomocí následujícího skriptu:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Chcete-li naplánovat spuštění tohoto skriptu každých 48 hodin, postupujte takto:

1.  Uložte skript do souboru s ". PS1" rozšíření.
2.  Otevřete **Ovládací panely** a klikněte na **systém a zabezpečení**.
3.  V záhlaví **Nástroje pro správu** klikněte na "**Naplánovat úkoly**".
4.  V **Plánovači úloh**klikněte pravým tlačítkem myši na**položku Knihovna plánu úloh**a klikněte na příkaz Vytvořit základní**úkol...**".
5.  Zadejte název nového úkolu a klepněte na tlačítko **Další**.
6.  Vyberte možnost **"Denně"** pro **aktivační událost úkolu** a klepněte na tlačítko **Další**.
7.  Nastavte opakování na dva dny a klepněte na tlačítko **Další**.
8.  Vyberte možnost Spustit**program**jako akci a klepněte na tlačítko **Další**.
9.  Do pole Program/skript zadejte**powershell**a do pole s názvem "**Přidat argumenty (volitelné)** zadejte úplnou cestu ke skriptu, který jste vytvořili dříve, a klepněte na tlačítko **Další**.
10. Na další obrazovce se zobrazí souhrn úkolu, který se chystáte vytvořit. Ověřte hodnoty a klepnutím na **tlačítko Dokončit** vytvořte úkol:
 
### <a name="note-about-domain-controller-logs"></a>Poznámka k protokolům řadiče domény

Pokud je protokolování auditu povoleno, může tento produkt generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu naleznete v tomto [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další kroky
* [Projděte si zásady ochrany osobních údajů společnosti Microsoft v Centru zabezpečení](https://www.microsoft.com/trustcenter)
* [**Poradce při potížích**](tshoot-connect-pass-through-authentication.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
