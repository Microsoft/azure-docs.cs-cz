---
title: Azure AD Connect a uživatelské ochrany osobních údajů | Dokumentace Microsoftu
description: Tento dokument popisuje, jak získat dodržování předpisů GDPR u služby Azure AD Connect.
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
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 337268db7365e417153ca3df3d545c135c5f2924
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314416"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Ochrana osobních údajů uživatelů a Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect a uživatelské ochrany osobních údajů.  Informace o službě Azure AD Connect Health a uživatele o ochraně osobních údajů najdete v článku [tady](reference-connect-health-user-privacy.md).

Vylepšení ochrany osobních údajů uživatele pro zařízení Azure AD Connect dvěma způsoby:

1.  Na vyžádání extrahovat data pro osobu a odstranění dat z této osoby ze zařízení
2.  Ujistěte se, že žádná data se uchovávají za 48 hodin.

Tým služby Azure AD Connect doporučí druhou možnost vzhledem k tomu je mnohem jednodušší implementaci a údržbu.

Server synchronizace služby Azure AD Connect obsahuje následující data o ochraně osobních údajů uživatele:
1.  Data o osoba **databáze služby Azure AD Connect**
2.  Data v **protokolu událostí Windows** soubory, které mohou obsahovat informace o osobě
3.  Data v **protokolové soubory instalace služby Azure AD Connect** o osobě, která může obsahovat

Zákazníci Azure AD Connect používejte následující pokyny při odebírání uživatelská data:
1.  Odstraňte obsah složky, která obsahuje soubory protokolu instalace služby Azure AD Connect v pravidelných intervalech – nejméně každých 48 hodin
2.  Tento produkt může také vytvořit protokoly událostí.  Další informace o protokolech protokoly událostí, najdete v tématu [dokumentaci](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Data o osobě se tato osoba data se odeberou ze zdrojového systému, odkud pochází z automaticky odebere z databáze služby Azure AD Connect. Žádná konkrétní akce před správci musí být v souladu s nařízením GDPR.  Vyžaduje však, že data služby Azure AD Connect je synchronizovaný s zdroje dat nejméně každé dva dny.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Odstraňte obsah složky souboru protokolu instalace služby Azure AD Connect
Pravidelně kontrolovat a odstraňte její obsah **c:\programdata\aadconnect** složka – s výjimkou **PersistedState.Xml** souboru. Tento soubor udržuje stav předchozí instalace aplikace Azure A připojit a používá se při upgradu instalace je provedena. Tento soubor neobsahuje žádná data o osobě a by neměl být odstraněn.

>[!IMPORTANT]
>Neodstraňujte PersistedState.xml souboru.  Tento soubor neobsahuje žádné informace o uživateli a udržuje stav předchozí instalace.

Můžete zkontrolovat a odstranit tyto soubory pomocí Průzkumníka Windows, nebo můžete použít skript následujícím postupem provádět potřebné akce:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Naplánujte tento skript ke spuštění každých 48 hodin
Použijte následující kroky plánování skript ke spuštění každých 48 hodin.

1.  Uložte skript v souboru s příponou  **&#46;PS1**, otevřete ovládací Panel a klikněte na **systémy a zabezpečení**.
    ![Systém](./media/reference-connect-user-privacy/gdpr2.png)

2.  V části nástroje pro správu, klikněte na **plánovat úkoly**.
    ![Úkol](./media/reference-connect-user-privacy/gdpr3.png)
3.  V Plánovači úloh, klikněte pravým tlačítkem na **knihovna plán úloh** a klikněte na **vytvořit základní úlohy...**
4.  Zadejte název pro novou úlohu a klikněte na tlačítko **Další**.
5.  Vyberte **denní** pro aktivační událost úlohy a klikněte na kartu **Další**.
6.  Nastavení opakování **2 dny** a klikněte na tlačítko **Další**.
7.  Vyberte **spustit program** jako akce a klikněte na kartu **Další**.
8.  Typ **PowerShell** pole u programu nebo skriptu a v poli označeném **argumenty (volitelné) přidejte**, zadejte úplnou cestu ke skriptu, který jste vytvořili dříve a pak klikněte na tlačítko **Další**.
9.  Na další obrazovce zobrazuje souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a klikněte na tlačítko **Dokončit** vytvořte úkol.



## <a name="next-steps"></a>Další postup
* [Projděte si zásady Microsoft Privacy Trust Center](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health a ochrana osobních údajů uživatele](reference-connect-health-user-privacy.md)
