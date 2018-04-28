---
title: Azure AD Connect a uživatele o ochraně osobních údajů | Microsoft Docs
description: Tento dokument popisuje, jak zajistit dodržování předpisů GDPR službou Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: a1fa7f58040b420bf52d89a57b1234416c2fb939
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect"></a>Ochrana osobních údajů uživatelů a Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect a uživatele o ochraně osobních údajů.  Informace o Azure AD Connect Health a uživatele o ochraně osobních údajů najdete v článku [zde](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Dodržování předpisů o ochraně osobních údajů uživatelů pro Azure AD Connect instalace lze dosáhnout dvěma způsoby:

1.  Na žádost extrahovat data pro osoby a odstranění dat z osoba ze zařízení
2.  Zkontrolujte, že žádná data se uchovávají za 48 hodin.

Tým služby Azure AD Connect doporučuje druhá možnost vzhledem k tomu, že je mnohem jednodušší na implementaci a údržbu.

Server synchronizace služby Azure AD Connect se ukládají následující data o ochraně osobních údajů uživatele:
1.  Data o osoby v **databáze Azure AD Connect**
2.  Data **protokolu událostí systému Windows** soubory, které může obsahovat informace o osoby
3.  Data **protokolové soubory instalace Azure AD Connect** , může obsahovat o osoby

Azure AD Connect zákazníci měli pomocí následujících pokynů při odebírání uživatelská data:
1.  Odstraňte obsah složky, která obsahuje soubory protokolu instalace Azure AD Connect v pravidelných intervalech – minimálně každých 48 hodin
2.  Tento produkt také vytvořit protokoly událostí.  Další informace o protokolech protokoly událostí, naleznete [dokumentaci](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data o osoby je automaticky odebrat z databáze Azure AD Connect, v případě, odeberou se tato osoba data ze zdrojového systému, které pochází z. Žádná konkrétní akce od správců musí být GDPR kompatibilní.  Ale vyžadují, aby data Azure AD Connect je synchronizovaný s datovým zdrojem každé dva dny.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Odstraňte obsah složky souboru protokolu instalace Azure AD Connect
Pravidelně kontrolovat a odstraňte obsah **c:\programdata\aadconnect** složky – s výjimkou **PersistedState.Xml** souboru. Tento soubor udržuje stav předchozí instalace Azure A připojit a používá se při upgradu instalace je provedena. Tento soubor neobsahuje žádná data o osoby a nesmí být odstraněna.

>[!IMPORTANT]
>Neodstraňujte soubor PersistedState.xml.  Tento soubor obsahuje žádné informace o uživateli a udržuje stav předchozí instalace.

Můžete zkontrolovat a odstraňte tyto soubory pomocí Průzkumníka Windows, nebo můžete použít skript takto můžete provádět potřebné akce:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Naplánovat tento skript každých 48 hodin
Pomocí následujících kroků plánování skript, který chcete spustit každých 48 hodin.

1.  Uložte skript v souboru s příponou  **&#46;PS1**, otevřete ovládací panely a klikněte na **systémy a zabezpečení**.
    ![Systém](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  V části nástroje pro správu, klikněte na **plán úlohy**.
    ![Úkol](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  V Plánovači úloh, klikněte pravým tlačítkem na **knihovna plán úloh** a klikněte na **vytvořit základní úlohy...**
4.  Zadejte název pro novou úlohu a klikněte na tlačítko **Další**.
5.  Vyberte **denní** pro aktivační události úlohy a klikněte na **Další**.
6.  Nastavit opakování na **2 dny** a klikněte na tlačítko **Další**.
7.  Vyberte **spustit program** jako akce a klikněte na **Další**.
8.  Typ **prostředí PowerShell** do pole pro Program nebo skript a do pole s názvem bez přípony **Přidat argumenty (volitelné)**, zadejte úplnou cestu ke skriptu, který jste vytvořili dříve a pak klikněte na tlačítko **Další**.
9.  Na další obrazovce zobrazuje souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a klikněte na tlačítko **Dokončit** k vytvoření úlohy.



## <a name="next-steps"></a>Další postup
* [Zkontrolujte nastavení zásad Microsoft Privacy na Centrum zabezpečení](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health a ochraně osobních údajů uživatelů](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
