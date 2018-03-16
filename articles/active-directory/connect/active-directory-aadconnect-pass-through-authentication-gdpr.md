---
title: "Azure AD Connect – předávací ověřování – dodržování předpisů GDPR | Microsoft Docs"
description: "Tento článek se zabývá předávací ověřování Azure Active Directory (Azure AD) a GDPR dodržování předpisů."
services: active-directory
keywords: "Azure AD Connect předávací ověřování, GDPR, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 21874c961163e3efba45c2ee8557c03135987f95
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-gdpr-compliance"></a>Azure předávací ověřování služby Active Directory: Dodržování předpisů GDPR

## <a name="overview"></a>Přehled

V květnu 2018, ochrany osobních údajů Evropského zákona, [obecné Data Protection nařízení (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), je kvůli vstoupily v platnost. GDPR ukládá nové pravidel na společnosti, organizace státní správy, bez zisku a organizace nabídka zboží a služeb na osoby ve Evropské unie (EU), nebo že shromažďovat a analyzovat data svázané s obyvatele Evropské unie. GDPR platí bez ohledu na to, kde se nacházíte. 

Produkty a služby Microsoftu jsou dnes, které vám pomůžou GDPR požadavkům. Další informace o zásadách Microsoft Privacy na [Centrum zabezpečení](https://www.microsoft.com/trustcenter).

Azure AD předávací ověřování vytvoří následující typy protokolů, které může obsahovat EUII:

- Soubory protokolu trasování Azure AD Connect.
- Soubory protokolu trasování ověření agenta.
- Soubory protokolu událostí systému Windows.

GDPR dodržování předpisů pro předávací ověřování lze dosáhnout dvěma způsoby:

1.  Na žádost extrahovat data pro osoby a odstranění dat z osoba ze zařízení.
2.  Zkontrolujte, že žádná data se uchovávají za 48 hodin.

Důrazně doporučujeme druhou možnost, jako je jednodušší implementaci a údržbu. Toto jsou pokyny pro každý typ protokolu:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstraňte soubory protokolů trasování Azure AD Connect

Zkontrolujte obsah **%ProgramData%\AADConnect** složku a odstraňte trasování protokolu obsah (**trasování -\*.log** soubory) této složky do 48 hodin po instalaci nebo upgradu Azure AD Connect nebo úprava konfigurace předávací ověřování, jako tato akce vytvoří předmětem GDPR data.

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

### <a name="delete-authentication-agent-event-logs"></a>Odstranění protokolů událostí ověření agenta

Tento produkt také vytvořit **protokoly událostí systému Windows**. Chcete-li získat další informace, přečtěte si [v tomto článku](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Chcete-li zobrazit protokoly související s předávací ověřování agenta, otevřete **Prohlížeč událostí** aplikace na serveru a v části **aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Odstraňte soubory protokolu trasování Agent ověřování

Pravidelně byste měli zkontrolovat obsah **%ProgramData%\Microsoft\Azure AD připojit Agent\Trace ověřování\**  a odstranit obsah této složky každých 48 hodin. 

>[!IMPORTANT]
>Pokud je spuštěna Služba agenta ověřování není budete moct odstranit aktuální soubor protokolu ve složce. Zastavte službu než to zkusíte znovu. Aby se zabránilo neúspěšných přihlášení uživatele, měli jste již nakonfigurovali předávací ověřování pro [vysokou dostupnost](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

Můžete zkontrolovat a odstraňte tyto soubory pomocí Průzkumníka Windows, nebo můžete použít následující skript provádět potřebné akce:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Při plánování tohoto skriptu ke spuštění každých 48 hodin, postupujte takto:

1.  Uložte skript v souboru se ". PS1 "rozšíření.
2.  Otevřete **ovládací panely** a klikněte na **systém a zabezpečení**.
3.  V části **nástroje pro správu** záhlaví, klikněte na "**plán úlohy**".
4.  V **Plánovač úloh**, klikněte pravým tlačítkem na "**knihovna plán úloh**"a klikněte na"**vytvořit základní úlohy...** ".
5.  Zadejte název pro novou úlohu a klikněte na tlačítko **Další**.
6.  Vyberte "**denní**" pro **aktivační události úlohy** a klikněte na tlačítko **Další**.
7.  Nastavení opakování do dvou dnů a klikněte na tlačítko **Další**.
8.  Vyberte "**spustit program**" akce a klikněte na **Další**.
9.  Typ "**prostředí PowerShell**"v poli pro Program nebo skript a do pole s popiskem"**Přidat argumenty (volitelné)**", zadejte úplnou cestu ke skriptu, který jste vytvořili dříve a pak klikněte na tlačítko **Další**.
10. Na další obrazovce zobrazuje souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a klikněte na tlačítko **Dokončit** k vytvoření úlohy:
 
### <a name="note-about-domain-controller-logs"></a>Všimněte si o protokoly řadiče domény

Pokud je povoleno protokolování auditu, může tento produkt generovat protokolů zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu, přečtěte si to [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další kroky
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
