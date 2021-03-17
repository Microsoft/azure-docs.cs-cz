---
title: Ochrana osobních údajů uživatelů a Azure Active Directory předávací ověřování | Microsoft Docs
description: Tento článek se zabývá Azure Active Directory (Azure AD) předávacím ověřováním a dodržováním předpisů GDPR.
services: active-directory
keywords: Azure AD Connect předávací ověřování, GDPR, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996572"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Ochrana osobních údajů uživatelů a předávací ověřování služby Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled

Předávací ověřování Azure AD vytvoří následující typ protokolu, který může obsahovat osobní údaje:

- Azure AD Connect soubory protokolu trasování.
- Soubory protokolu trasování ověřovacího agenta.
- Soubory protokolu událostí systému Windows.

Zvyšte ochranu osobních údajů uživatelů pro předávací ověřování dvěma způsoby:

1.  Na vyžádání rozbalte data pro osobu a odeberte data z této osoby z instalací.
2.  Zajistěte, aby žádná data nezůstala déle než 48 hodin.

Tuto druhou možnost důrazně doporučujeme, protože je snazší ji implementovat a udržovat. Pro každý typ protokolu se používají tyto pokyny:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranit Azure AD Connect soubory protokolu trasování

Ověřte obsah složky **%ProgramData%\AADConnect** a odstraňte obsah protokolu trasování (soubory **Trace- \* . log** ) této složky během 48 hodin od instalace nebo Azure AD Connect upgradu konfigurace předávacího ověřování, protože tato akce může vytvářet data, která jsou pokrytá GDPR.

>[!IMPORTANT]
>Neodstraňujte soubor **PersistedState.xml** v této složce, protože tento soubor slouží k údržbě stavu předchozí instalace Azure AD Connect a používá se v případě, že je dokončena instalace upgradu. Tento soubor nikdy nebude obsahovat žádná data o osobě a neměl by se nikdy odstranit.

Tyto soubory protokolu trasování můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete použít následující skript PowerShellu k provedení nezbytných akcí:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Uložte skript do souboru s příponou. PS1 "rozšíření. Spusťte tento skript podle potřeby.

Další informace o souvisejících požadavcích Azure AD Connect GDPR najdete v [tomto článku](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Odstranit protokoly událostí ověřovacího agenta

Tento produkt může také vytvářet **protokoly událostí systému Windows**. Pokud se chcete dozvědět víc, přečtěte si [Tento článek](/windows/win32/wes/windows-event-log).

Chcete-li zobrazit protokoly související s agentem předávacího ověřování, otevřete aplikaci **Prohlížeč událostí** na serveru a zaškrtněte v části **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Odstranit soubory protokolu trasování ověřovacího agenta

Měli byste pravidelně kontrolovat obsah **služby%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** a odstranit obsah této složky každých 48 hodin. 

>[!IMPORTANT]
>Pokud je spuštěna služba ověřovacího agenta, nebudete moci odstranit aktuální soubor protokolu ve složce. Před opakováním pokusu službu zastavte. Aby nedocházelo k chybám při přihlašování uživatelů, měli byste mít již nakonfigurovanou předávací ověřování pro [vysokou dostupnost](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Tyto soubory můžete buď zkontrolovat a odstranit pomocí Průzkumníka Windows, nebo můžete použít následující skript k provedení nezbytných akcí:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Pokud chcete naplánovat spuštění tohoto skriptu každých 48 hodin, postupujte podle těchto kroků:

1.  Uložte skript do souboru s příponou. PS1 "rozšíření.
2.  Otevřete **Ovládací panely** a klikněte na **systém a zabezpečení**.
3.  V části **Nástroje pro správu** klikněte na **plán úlohy**.
4.  V **Plánovač úloh** klikněte pravým tlačítkem na "**Knihovna plánování úkolů**" a klikněte na "**vytvořit základní úlohu...**".
5.  Zadejte název nové úlohy a klikněte na **Další**.
6.  Pro **aktivační událost úlohy** vyberte **denní** a klikněte na **Další**.
7.  Nastavte opakování na dva dny a klikněte na **Další**.
8.  Jako akci vyberte **Spustit program** a klikněte na **Další**.
9.  Do pole program nebo skript zadejte "**PowerShell**" a v poli označený jako "**Přidat argumenty (nepovinné)**" zadejte úplnou cestu ke skriptu, který jste vytvořili dříve, a pak klikněte na tlačítko **Další**.
10. Na další obrazovce se zobrazí souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a kliknutím na **Dokončit** vytvořte úlohu:
 
### <a name="note-about-domain-controller-logs"></a>Poznámka o protokolech řadičů domény

Pokud je povoleno protokolování auditu, může tento produkt generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu najdete v tomto [článku](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Další kroky
* [Přečtěte si téma zásady ochrany osobních údajů Microsoftu na webu Trust Center.](https://www.microsoft.com/trustcenter)
* [**Řešení potíží**](tshoot-connect-pass-through-authentication.md) – Naučte se řešit běžné problémy s touto funkcí.