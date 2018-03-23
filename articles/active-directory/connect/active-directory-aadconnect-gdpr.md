---
title: Azure AD Connect a nařízení ochrany dat obecné | Microsoft Docs
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
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>GDPR dodržování předpisů a Azure AD Connect 

V květnu 2018, ochrany osobních údajů Evropského zákona, [obecné Data Protection nařízení (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), je kvůli vstoupily v platnost. GDPR ukládá nové pravidel na společnosti, organizace státní správy, bez zisku a organizace nabídka zboží a služeb na osoby ve Evropské unie (EU), nebo že shromažďovat a analyzovat data svázané s obyvatele Evropské unie. GDPR platí bez ohledu na to, kde se nacházíte. 

Produkty a služby Microsoftu jsou dnes, které vám pomůžou GDPR požadavkům. Další informace o zásadách Microsoft Privacy na [Centrum zabezpečení](https://www.microsoft.com/trustcenter)

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect a GDPR dodržování předpisů.  Informace o Azure AD Connect Health a GDPR dodržování předpisů najdete v článku [zde](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Data obecné ochrany nařízení dodržování předpisů pro instalace Azure AD Connect jsou dostupné dvěma způsoby:

1.  Na žádost extrahovat data pro osoby a odstranění dat z osoba ze zařízení
2.  Zkontrolujte, že žádná data se uchovávají za 48 hodin.

Tým služby Azure AD Connect doporučuje druhá možnost vzhledem k tomu, že je mnohem jednodušší na implementaci a údržbu.

Server synchronizace služby Azure AD Connect se ukládají následující data, která jsou v oboru pro GDPR dodržování předpisů:
1.  Data o osoby v **databáze Azure AD Connect**
2.  Data **protokolu událostí systému Windows** soubory, které může obsahovat informace o osoby
3.  Data **protokolové soubory instalace Azure AD Connect** , může obsahovat o osoby

GDPR kompatibilní, Azure AD Connect Zákazníci by měl používat následující pokyny:
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
- [Integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health a GDPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
