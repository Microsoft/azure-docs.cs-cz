---
title: Azure AD Connect a ochrana osobních údajů uživatele | Microsoft Docs
description: Tento dokument popisuje, jak získat GDPR dodržování předpisů s Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997694"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Ochrana osobních údajů uživatelů a Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se věnuje Azure AD Connect a ochraně osobních údajů uživatelů.  Informace o Azure AD Connect Health a ochraně osobních údajů uživatele najdete v [článku.](reference-connect-health-user-privacy.md)

Zvyšte ochranu osobních údajů uživatelů pro instalace Azure AD Connect dvěma způsoby:

1.  Na vyžádání rozbalte data pro osobu a odeberte data z této osoby z instalací.
2.  Zajistěte, aby žádná data nezůstala déle než 48 hodin.

Azure AD Connect tým doporučuje druhou možnost, protože je mnohem jednodušší implementovat a udržovat.

Server Azure AD Connect Sync ukládá následující údaje o ochraně osobních údajů uživatele:
1.  Data o osobě v **databázi Azure AD Connect**
2.  Data v souborech **protokolu událostí systému Windows** , která mohou obsahovat informace o osobě
3.  Data v **souborech protokolu instalace Azure AD Connect** , která mohou obsahovat informace o osobě

Azure AD Connect zákazníci by při odebírání uživatelských dat měli použít následující pokyny:
1.  Odstraňte obsah složky, která obsahuje soubory protokolu instalace Azure AD Connect v pravidelných intervalech – nejméně každých 48 hodin.
2.  Tento produkt může také vytvářet protokoly událostí.  Další informace o protokolech protokolů událostí najdete v [dokumentaci zde](/windows/win32/wes/windows-event-log).

Data o osobě se automaticky odeberou z databáze Azure AD Connect, když se data této osoby odeberou ze zdrojového systému, ze kterého pochází. Žádná konkrétní akce od správců nemusí být GDPR kompatibilní.  Ale vyžaduje, aby se data Azure AD Connect synchronizovaná se zdrojem dat minimálně v každém dvou dnech.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Odstranění obsahu složky souboru protokolu instalace Azure AD Connect
Pravidelně kontrolujte a odstraňujte obsah složky **c:\programdata\aadconnect** – s výjimkou souboru **PersistedState.Xml** . Tento soubor udržuje stav předchozí instalace Azure A připojuje se a používá se při provádění instalace upgradu. Tento soubor neobsahuje žádná data o osobě a neměl by být odstraněn.

>[!IMPORTANT]
>Neodstraňujte soubor PersistedState.xml.  Tento soubor neobsahuje žádné informace o uživateli a udržuje stav předchozí instalace.

Tyto soubory můžete buď zkontrolovat a odstranit pomocí Průzkumníka Windows, nebo můžete použít skript podobný následujícímu k provedení nezbytných akcí:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Naplánovat spuštění tohoto skriptu každých 48 hodin
Pomocí následujících kroků můžete naplánovat spuštění skriptu každých 48 hodin.

1.  Uložte skript do souboru s příponou **&#46;ps1** a pak otevřete ovládací panely a klikněte na **systémy a zabezpečení**.
    ![Systém](./media/reference-connect-user-privacy/gdpr2.png)

2.  V záhlaví nástroje pro správu klikněte na **plánování úloh**.
    ![Úkol](./media/reference-connect-user-privacy/gdpr3.png)
3.  V Plánovač úloh klikněte pravým tlačítkem na **knihovnu plánování úloh** a klikněte na **vytvořit základní úlohu...**
4.  Zadejte název nové úlohy a klikněte na **Další**.
5.  U triggeru úlohy vyberte **denně** a klikněte na **Další**.
6.  Nastavte opakování na **2 dny** a klikněte na **Další**.
7.  Vyberte **Spustit program** jako akci a klikněte na tlačítko **Další**.
8.  Do pole program nebo skript zadejte **PowerShell** a do pole s popiskem **Přidat argumenty (volitelné)** zadejte úplnou cestu ke skriptu, který jste vytvořili dříve, a pak klikněte na **Další**.
9.  Na další obrazovce se zobrazí souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a kliknutím na **Dokončit** vytvořte úlohu.



## <a name="next-steps"></a>Další kroky
* [Přečtěte si téma zásady ochrany osobních údajů Microsoftu na webu Trust Center.](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health a ochrana osobních údajů uživatele](reference-connect-health-user-privacy.md)