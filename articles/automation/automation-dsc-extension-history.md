---
title: Práce s historií verze rozšíření pro konfiguraci požadovaného stavu Azure
description: Tento článek sdílí informace o historii verzí pro rozšíření konfigurace požadovaného stavu (DSC) v Azure.
ms.date: 02/17/2021
keywords: DSC, PowerShell, Azure, rozšíření
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651798"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Práce s historií verze rozšíření pro konfiguraci požadovaného stavu Azure

[Rozšíření](../virtual-machines/extensions/dsc-overview.md) virtuálního počítače pro konfiguraci požadovaného stavu Azure (DSC) se aktualizuje tak, aby podporovalo vylepšení a nové funkce dodávané pomocí Azure, Windows serveru a Windows Management FRAMEWORKU (WMF), která zahrnuje Windows PowerShell.

Tento článek obsahuje informace o jednotlivých verzích rozšíření virtuálních počítačů Azure DSC, která prostředí podporuje, a komentáře a poznámky k novým funkcím nebo změnám.

## <a name="latest-version"></a>Nejnovější verze

### <a name="version-283"></a>Verze 2,83

- **Datum vydání:**
  - Únor 2021
- **Podpora operačního systému:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient Windows 7/8.1/10
  - Nano Server
- **Podpora WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizace WMF 4,0
  - WMF 4.0
- **Hlediska**
  - Azure
  - Azure Čína Vianet 21
  - Azure Government
- **Poznámky:** Tato verze obsahuje opravu pro nepodepsané binární soubory s rozšířením virtuálního počítače s Windows.

### <a name="version-280"></a>Verze 2,80

- **Datum vydání:**
  - 26. září, SEP-2019 (Azure) | 6. července 2020 (Azure Čína Vianet 21) | 20. července 2020 (Azure Government)
- **Podpora operačního systému:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient Windows 7/8.1/10
  - Nano Server
- **Podpora WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizace WMF 4,0
  - WMF 4.0
- **Hlediska**
  - Azure
  - Azure Čína Vianet 21
  - Azure Government
- **Poznámky:** V této verzi nejsou zahrnuté žádné nové funkce.

### <a name="version-276"></a>Verze 2,76

- **Datum vydání:**
  - 9. května 2018 (Azure) | 21. června 2018 (Azure Čína Vianet 21, Azure Government)
- **Podpora operačního systému:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Klient Windows 7/8.1/10
  - Nano Server
- **Podpora WMF:**
  - WMF 5.1
  - WMF 5,0 RTM
  - Aktualizace WMF 4,0
  - WMF 4.0
- **Hlediska**
  - Azure
  - Azure Čína Vianet 21
  - Azure Government
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Vylepšení metadat rozšíření pro dílčí stav a další drobné opravy chyb.

## <a name="supported-versions"></a>Podporované verze

> [!WARNING]
> Verze 2,4 až 2,13 používají WMF 5,0 Public Preview, jejichž podpisové certifikáty skončily v srpnu 2016.
> Další informace o tomto problému najdete v následujícím článku na [blogu](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Verze 2,75

- **Datum vydání:** 5. března 2018
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Po vynucení TLS 1,2 nemůžete připojit virtuální počítač k Azure Automation konfiguraci stavu pomocí šablon Správce prostředků SVÉPOMOCNÁ, které jsou k dispozici v Azure Marketplace, nebo použít rozšíření DSC k načtení všech konfigurací hostovaných na GitHubu. Vrátí se chyba podobná následujícímu při nasazení rozšíření:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - V nové verzi rozšíření se teď vynutilo TLS 1,2. Pokud jste již v šabloně Správce prostředků zadali rozšíření, `AutoUpgradeMinorVersion = true` je rozšíření autoupgradem na 2,75. V případě ruční aktualizace zadejte `TypeHandlerVersion = 2.75` v šabloně správce prostředků.

### <a name="version-270---272"></a>Verze 2,70 – 2,72

- **Datum vydání:** 13. listopadu 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Opravy chyb & vylepšení, která zjednodušují použití konfigurace Azure Automation stavu na portálu a se šablonou Správce prostředků. Další informace najdete v tématu [výchozí konfigurační skript](../virtual-machines/extensions/dsc-overview.md) v dokumentaci k rozšíření DSC.

### <a name="version-226"></a>Verze 2,26

- **Datum vydání:** 9. června 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Vylepšení telemetrie.

### <a name="version-225"></a>Verze 2,25

- **Datum vydání:** 2. června 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Bylo přidáno několik oprav chyb a dalších menších vylepšení.

### <a name="version-224"></a>Verze 2,24

- **Datum vydání:** 13. dubna 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Zveřejňuje identifikátor UUID virtuálního počítače & ID agenta DSC jako metadata rozšíření. Byla přidána další menší vylepšení.

### <a name="version-223"></a>Verze 2,23

- **Datum vydání:** 15. března 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Byly přidány opravy chyb a další vylepšení.

### <a name="version-222"></a>Verze 2,22

- **Datum vydání:** 8. února 2017
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Podpora WMF:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 aktualizace, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje rozhraní [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Rozšíření DSC teď podporuje WMF 5,1.
  - Byla přidána menší další vylepšení.

### <a name="version-221"></a>Verze 2,21

- **Datum vydání:** 2. prosince 2016
- **Podpora operačního systému:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Podpora WMF:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016; pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart). Pro nano Server je na virtuálním počítači nainstalovaná role DSC.
- **Nové funkce:**
  - Rozšíření DSC je teď dostupné na nano serveru. Tato verze primárně obsahuje změny kódu pro spuštění rozšíření na nano serveru.
  - Byla přidána menší další vylepšení.

### <a name="version-220"></a>Verze 2,20

- **Datum vydání:** 2. srpna 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Podpora pro WMF 5,1 Preview. Při prvním publikování byla tato verze nepovinná a při instalaci WMF 5,1 Preview jste museli zadat Wmfversion = ' 5.1 PP ' v šablonách Správce prostředků.
    Wmfversion = ' nejnovější ' nainstaluje i [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Další informace o WMF 5,1 Preview najdete v [tomto blogu](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Byly přidány drobné další opravy a vylepšení.

### <a name="version--219"></a>Verze 2,19

- **Datum vydání:** 3. června 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Prostředí:** Azure, Azure Čína Vianet 21, Azure Government
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Rozšíření DSC je teď dostupné v Azure Čína Vianet 21. Tato verze obsahuje opravy pro spuštění rozšíření v Azure Čína Vianet 21.

### <a name="version-218"></a>Verze 2,18

- **Datum vydání:** 3. června 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Pokud dojde k chybě během stažení opravy hotfix telemetrie (známý Azure DNS problém) nebo během instalace, udělejte neblokování telemetrie.
  - Oprava pro obdobu přerušovaného problému, kdy rozšíření zastaví zpracování konfigurace po restartování. To způsobilo, že rozšíření DSC zůstane ve stavu "přechodu".
  - Byly přidány drobné další opravy a vylepšení.

### <a name="version-217"></a>Verze 2,17

- **Datum vydání:** 26. dubna 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Podpora pro WMF 4,0 Update. Další informace o aktualizaci WMF 4,0 najdete v [tomto blogu](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Použijte logiku opakování na chybách, ke kterým došlo během instalace rozšíření DSC, nebo při instalaci instalačního rozšíření pro konfiguraci DSC. V rámci této změny rozšíření zopakuje instalaci, pokud selhala předchozí instalace nebo znovu nepřijala konfiguraci DSC, která se dřív nezdařila, a to maximálně třikrát, dokud nedosáhne stavu dokončení (úspěch/chyba) nebo když přijde nová žádost. Pokud rozšíření selhalo z důvodu neplatného uživatelského nastavení/vstupu uživatele, neopakuje se. V takovém případě se rozšíření musí vyvolat znovu s novým požadavkem a správným uživatelským nastavením. 

  > [!NOTE]
   > Rozšíření DSC je závislé na agentovi virtuálního počítače Azure pro opakované pokusy. Agent virtuálního počítače Azure vyvolá rozšíření s poslední neúspěšnou žádostí, dokud nedosáhne stavu úspěch nebo chyba.

### <a name="version-216"></a>Verze 2,16

- **Datum vydání:** 21. dubna 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Zlepšení zpracování chyb a dalších drobných oprav chyb.
  - Nová vlastnost v nastavení rozšíření DSC `ForcePullAndApply` v AdvancedOptions se přidá, aby se povolilo rozšíření DSC v případě, že je režim aktualizace vyžádané obnovení (na rozdíl od výchozího režimu push). Další informace o nastavení rozšíření DSC najdete v [tomto blogu](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Verze 2,15

- **Datum vydání:** 14. března 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - V rozšíření verze 2,14 byly zahrnuty změny instalace WMF RTM. Při upgradu z verze rozšíření 2.13.2.0 na 2.14.0.0 si můžete všimnout, že některé rutiny DSC selžou nebo vaše konfigurace selže s chybou – "nebyla nalezena žádná instance s danými hodnotami vlastností". Další informace najdete v [poznámkách k verzi DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Alternativní řešení těchto problémů se přidala ve verzi 2,15. 
  - Pokud jste už nainstalovali verzi 2,14 a používáte některou z výše uvedených problémů, musíte provést tyto kroky ručně. V relaci PowerShellu se zvýšenými oprávněními spusťte následující příkazy:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Verze 2,14

- **Datum vydání:** 25. února 2016
- **Podpora operačního systému:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Podpora WMF:** WMF 5,0 RTM, WMF 4,0
- **Prostředí:** Azure
- **Poznámky:** Tato verze používá DSC jako součást Windows serveru 2016 Technical Preview. pro jiné Windows OSs nainstaluje [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalace WMF vyžaduje restart).
- **Nové funkce:**
  - Používá WMF RTM.
  - Povoluje shromažďování dat za účelem zlepšení kvality rozšíření DSC. Další informace najdete v tomto [článku na blogu](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Poskytuje aktualizovaný formát nastavení pro rozšíření v šabloně Správce prostředků. Další informace najdete v tomto [článku na blogu](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Opravy chyb a další vylepšení.

## <a name="next-steps"></a>Další kroky

- Další informace o prostředí PowerShell DSC najdete v tématu [PowerShell Documentation Center](/powershell/scripting/dsc/overview/overview).
- Projděte si [šablonu správce prostředků pro rozšíření DSC](../virtual-machines/extensions/dsc-template.md).
- Pro další funkce a prostředky, které můžete spravovat pomocí prostředí PowerShell DSC, přejděte do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Podrobnosti o předávání citlivých parametrů do konfigurací najdete v tématu [Zabezpečená Správa přihlašovacích údajů s obslužným rutinou rozšíření DSC](../virtual-machines/extensions/dsc-credentials.md).
