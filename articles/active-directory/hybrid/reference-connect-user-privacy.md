---
title: Azure AD Connect a ochrana osobních údajů uživatelů | Dokumenty společnosti Microsoft
description: Tento dokument popisuje, jak získat kompatibilitu GDPR s Azure AD Connect.
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
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455758"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Ochrana osobních údajů uživatelů a Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect a soukromí uživatelů.  Informace o azure ad připojení stavu a soukromí uživatelů naleznete v článku [zde](reference-connect-health-user-privacy.md).

Zlepšení ochrany osobních údajů uživatelů pro instalace Azure AD Connect dvěma způsoby:

1.  Na požádání extrahujte údaje o osobě a odstraňte z ní údaje ze zařízení
2.  Ujistěte se, že žádná data nejsou uchovávána po 48 hodinách.

Tým Azure AD Connect doporučuje druhou možnost, protože je mnohem jednodušší implementovat a udržovat.

Synchronizační server Azure AD Connect ukládá následující data o ochraně osobních údajů uživatelů:
1.  Data o osobě v **databázi Azure AD Connect**
2.  Data v souborech **protokolu událostí systému Windows,** která mohou obsahovat informace o osobě
3.  Data v **souborech protokolu instalace Azure AD Connect,** které mohou obsahovat informace o osobě

Zákazníci Azure AD Connect by měli při odebírání uživatelských dat používat následující pokyny:
1.  Pravidelně odstraňujte obsah složky, která obsahuje soubory protokolu instalace služby Azure AD Connect – alespoň každých 48 hodin
2.  Tento produkt může také vytvořit protokoly událostí.  Další informace o protokolech protokolů protokolů událostí naleznete v [dokumentaci zde](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data o osobě se automaticky odeberou z databáze Azure AD Connect, když se data této osoby odeberou ze zdrojového systému, odkud pochází. Žádná konkrétní akce ze strany správců nemusí být v souladu s GDPR.  Však vyžaduje, aby azure ad připojení data se synchronizuje se zdrojem dat alespoň každé dva dny.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Odstranění obsahu složky souboru protokolu instalace služby Azure AD Connect
Pravidelně kontrolujte a odstraňujte obsah složky **c:\programdata\aadconnect** – s výjimkou souboru **PersistedState.Xml.** Tento soubor udržuje stav předchozí instalace Azure A Connect a používá se při provádění instalace upgradu. Tento soubor neobsahuje žádná data o osobě a neměl by být odstraněn.

>[!IMPORTANT]
>Neodstraňujte soubor PersistedState.xml.  Tento soubor neobsahuje žádné informace o uživateli a udržuje stav předchozí instalace.

Tyto soubory můžete zkontrolovat a odstranit pomocí Průzkumníka Windows nebo můžete k provedení nezbytných akcí použít skript, jako je následující:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Naplánujte spuštění tohoto skriptu každých 48 hodin
Pomocí následujících kroků naplánujte spuštění skriptu každých 48 hodin.

1.  Uložte skript do souboru s příponou **&#46;PS1**, otevřete Ovládací panely a klikněte na **systém a zabezpečení**.
    ![Systém](./media/reference-connect-user-privacy/gdpr2.png)

2.  V záhlaví Nástroje pro správu klikněte na **položku Naplánovat úkoly**.
    ![Úkol](./media/reference-connect-user-privacy/gdpr3.png)
3.  V Plánovači úloh klikněte pravým tlačítkem myši na **knihovnu plánu úloh** a klikněte na **Vytvořit základní úkol...**
4.  Zadejte název nového úkolu a klepněte na tlačítko **Další**.
5.  Vyberte **denní** pro aktivační událost úkolu a klepněte na **tlačítko Další**.
6.  Nastavte opakování na **2 dny** a klepněte na tlačítko **Další**.
7.  Vyberte **Spustit program** jako akci a klepněte na **tlačítko Další**.
8.  Do pole Programu/skriptu zadejte **PowerShell** a do pole s popiskem **Přidat argumenty (volitelné)** zadejte úplnou cestu ke skriptu, který jste vytvořili dříve, a klepněte na tlačítko **Další**.
9.  Na další obrazovce se zobrazí souhrn úkolu, který se chystáte vytvořit. Ověřte hodnoty a klepnutím na **tlačítko Dokončit** vytvořte úkol.



## <a name="next-steps"></a>Další kroky
* [Projděte si zásady ochrany osobních údajů společnosti Microsoft v Centru zabezpečení](https://www.microsoft.com/trustcenter)
* [Azure AD Connect stav a ochrana osobních údajů uživatelů](reference-connect-health-user-privacy.md)
