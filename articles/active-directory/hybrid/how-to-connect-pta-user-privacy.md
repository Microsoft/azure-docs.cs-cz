---
title: Předávací ověřování ochrana osobních údajů uživatelů a Azure Active Directory | Dokumentace Microsoftu
description: Tento článek se zabývá předávací ověřování služby Azure Active Directory (Azure AD) a nařízení GDPR dodržování předpisů.
services: active-directory
keywords: Azure AD Connect předávací ověřování, nařízení GDPR, povinné součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: ef86c9ee00fdd4673774640d33eec93e2ead876d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193089"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Předávací ověřování ochrana osobních údajů uživatelů a Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Přehled

Azure AD předávací ověřování vytvoří následující typ protokolu, který může obsahovat osobní údaje:

- Soubory protokolu trasování Azure AD Connect.
- Soubory protokolu trasování ověřování agenta.
- Soubory protokolu událostí Windows.

Vylepšení ochrany osobních údajů uživatele pro předávací ověřování dvěma způsoby:

1.  Na vyžádání extrahovat data pro osobu a odstranění dat z této osoby ze zařízení.
2.  Ujistěte se, že žádná data se uchovávají za 48 hodin.

Důrazně doporučujeme druhou možnost, jak usnadňuje implementaci a údržbu. Toto jsou pokyny pro jednotlivé typy protokolů:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Odstranit soubory protokolu trasování služby Azure AD Connect

Zkontrolujte obsah **%ProgramData%\AADConnect** složku a odstraňte trasování protokolu obsah (**trasování -\*.log** soubory) této složky do 48 hodin od instalace nebo upgrade služby Azure AD Connect nebo změna konfigurace předávacího ověřování, jako tuto akci mohou vytvořit data vztahuje nařízení GDPR.

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

### <a name="delete-authentication-agent-event-logs"></a>Odstranit protokoly událostí ověřování agenta

Tento produkt může také vytvořit **protokoly událostí Windows**. Další informace, přečtěte si prosím [v tomto článku](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Chcete-li zobrazit protokoly vztahujících se k agentovi předávací ověřování, otevřete **Prohlížeč událostí** aplikace na serveru a v části **aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Odstraňte soubory protokolů trasování ověřování agenta

Měli byste pravidelně zkontrolovat obsah <strong>%ProgramData%\Microsoft\Azure AD Connect Agent\Trace ověřování\</ strong > a odstraňte její obsah této složky každých 48 hodin. 

>[!IMPORTANT]
>Pokud je spuštěna Služba agenta ověřování, není možné odstranit aktuální soubor protokolu ve složce. Zastavte službu než to zkusíte znovu. Aby se zabránilo neúspěšných přihlášení uživatele, měli byste mít už nakonfigurovaný předávací ověřování pro [vysoké dostupnosti](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Můžete zkontrolovat a odstranit tyto soubory pomocí Průzkumníka Windows, nebo můžete použít následující skript provádět potřebné akce:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Naplánování spuštění tohoto skriptu za 48 hodin, postupujte takto:

1.  Uložte skript v souboru se ". PS1 "rozšíření.
2.  Otevřít **ovládací panely** a klikněte na **systém a zabezpečení**.
3.  V části **nástroje pro správu** záhlaví, klikněte na "**plánovat úkoly**".
4.  V **Plánovač úloh**, klikněte pravým tlačítkem na "**knihovna plán úloh**"a klikněte na"**vytvořit základní úlohy...** ".
5.  Zadejte název pro novou úlohu a klikněte na tlačítko **Další**.
6.  Vyberte "**denní**" pro **aktivační událost úlohy** a klikněte na tlačítko **Další**.
7.  Nastavení opakování na dvou dní a klikněte na tlačítko **Další**.
8.  Vyberte "**spustit program**" akce a klikněte na **Další**.
9.  Typ "**PowerShell**"v poli pro Program či skript a do pole s popiskem"**argumenty (volitelné) přidejte**", zadejte úplnou cestu ke skriptu, který jste vytvořili dříve a pak klikněte na tlačítko **Další**.
10. Na další obrazovce zobrazuje souhrn úlohy, kterou se chystáte vytvořit. Ověřte hodnoty a klikněte na tlačítko **Dokončit** vytvoření úlohy:
 
### <a name="note-about-domain-controller-logs"></a>Mějte na paměti o protokolech řadiče domény

Pokud je povoleno protokolování auditu, tento produkt může generovat protokoly zabezpečení pro řadiče domény. Další informace o konfiguraci zásad auditu, najdete v tomto [článku](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Další postup
* [Projděte si zásady Microsoft Privacy Trust Center](https://www.microsoft.com/trustcenter)
- [**Řešení potíží s** ](tshoot-connect-pass-through-authentication.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
