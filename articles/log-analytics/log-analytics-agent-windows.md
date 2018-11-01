---
title: Připojení počítačů s Windows ke službě Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje postup připojení počítačů s Windows hostovaných v jiných cloudech nebo místních do Log Analytics se Microsoft Monitoring Agent (MMA).
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 2b9e7615fc0c2262c33ab5d7be39bdb99bc752bd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412954"
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Připojení počítačů s Windows ke službě Log Analytics v Azure

Abyste mohli monitorovat a spravovat virtuální počítače nebo fyzického počítače ve vašem místním datovém centru nebo jiných cloudové prostředí pomocí služby Log Analytics, budete muset nasadit Microsoft Monitoring Agent (MMA) a nakonfigurovat, aby zprávy do jednoho nebo více pracovních prostorů Log Analytics.  Agent také podporuje role Hybrid Runbook Worker pro Azure Automation.  

Na monitorovaném počítači Windows je agent uveden jako služba Microsoft Monitoring Agent. Služba Microsoft Monitoring Agent shromažďuje události ze souborů protokolu a protokolu událostí Windows, údaje o výkonu a další telemetrie. I v případě, že agent není schopen komunikovat se službou Log Analytics, který bude posílat sestavy, agent běží dál a zařadí do fronty shromážděná data na disk monitorovaného počítače. Při obnovení připojení služba Microsoft Monitoring Agent odešle shromážděná data do služby.

Může být agent nainstalovaný pomocí jedné z následujících metod. Většina zařízení pomocí kombinace těchto metod nainstalovat různé sady počítačů, podle potřeby.  Podrobnosti o použití každé metody jsou k dispozici později v tomto článku.

* Ruční instalace. Instalační program je v počítači pomocí Průvodce instalací, z příkazového řádku spustit ručně nebo nasadit pomocí stávajících nástroje pro distribuci softwaru.
* Azure Automation Desired State Configuration (DSC). Používání DSC ve službě Azure Automation pomocí skriptu pro počítače Windows už nasazená ve vašem prostředí.  
* Skript prostředí PowerShell.
* Šablona Resource Manageru pro virtuální počítače s Windows v místním ve službě Azure Stack.  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](log-analytics-concept-hybrid.md#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-concept-hybrid.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta Microsoft Monitoring Agent pro Windows potřebujete ID a klíč vašeho pracovního prostoru Log Analytics.  Tyto informace jsou nezbytné při instalaci z každé metody instalace ke správné konfiguraci agenta a zajistěte, aby že mohla úspěšně komunikovat s Log Analytics v Azure komerční a cloud pro státní správu USA.  

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který chcete v konfiguraci agenta pro hlášení.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/log-analytics-agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.   
5. Zkopírujte a vložte do oblíbeného editoru, **ID pracovního prostoru** a **primární klíč**.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Instalace agenta pomocí Průvodce instalací
Následující kroky instalace a konfigurace agenta pro Log Analytics v Azure a cloudu Azure Government s použitím Průvodce instalací pro agenta Microsoft Monitoring Agent v počítači.  

1. V pracovním prostoru Log Analytics z **servery Windows** přejde dříve, vyberte příslušné stránky **stáhnout agenta Windows** verze ke stažení v závislosti na architektuře procesoru operační systém Windows.   
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.   
6. Na stránce **Azure Log Analytics** postupujte následovně:
   1. Vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (primární klíč)**, které jste si zkopírovali dříve.  Pokud se má počítač hlásit do pracovního prostoru Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.  
7. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.<br><br> ![vložení ID pracovního prostoru a primárního klíče](media/log-analytics-agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
9. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Pokud chcete potvrdit, odesílá informace do Log Analytics, přečtěte si téma [ověřit připojení agenta k Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalace agenta pomocí příkazového řádku
Stažený soubor pro agenta je samostatná instalace balíčku.  Instalační program pro agenta a jeho podpůrné soubory jsou obsažené v balíčku a potřeba extrahovat, aby bylo možné správně nainstalovat pomocí příkazového řádku je znázorněno v následujícím příkladu.    

>[!NOTE]
>Pokud chcete provést upgrade agenta, musíte používat službu Log Analytics, rozhraní API pro skriptování. Naleznete v tématu [Správa a údržba agenta Log Analytics pro Windows a Linux](log-analytics-agent-manage.md) pro další informace.

Následující tabulka obsahuje konkrétní parametry Log Analytics podporuje instalační program pro agenta, včetně při nasazení pomocí Automation DSC.

|Možnosti specifické pro agenta MMA                   |Poznámky         |
|---------------------------------------|--------------|
| NASTAVENÍ NOAPM = 1                               | Volitelný parametr. Nainstaluje agenta bez .NET Application Performance Monitoring pro aplikace.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurace agenta na generování sestav s pracovním prostorem                |
|OPINSIGHTS_WORKSPACE_ID                | Id pracovního prostoru (guid) pro pracovní prostor pro přidání                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klíč pracovního prostoru pro počáteční ověření s pracovním prostorem |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Zadejte cloudovém prostředí, kde se nachází pracovní prostor <br> 0 = komerčním cloudu azure (výchozí) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | Identifikátor URI pro použití proxy serveru |
|OPINSIGHTS_PROXY_USERNAME               | Uživatelské jméno pro přístup k ověřený proxy server |
|OPINSIGHTS_PROXY_PASSWORD               | Heslo pro přístup k ověřený proxy server |

1. K extrakci instalačních souborů agenta z příkazového řádku se zvýšenými oprávněními spusťte `MMASetup-<platform>.exe /c` a zobrazí výzvu pro cestu k extrakci souborů.  Případně lze zadat cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Path>`.  
2. Bezobslužná instalace agenta a nakonfigurovat, aby hlásit do pracovního prostoru v komerčním cloudu Azure, ze složky extrahovány instalační soubory, zadejte: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   nebo pokud chcete konfigurovat agenta na generování sestav do cloudu Azure pro státní správu USA, zadejte: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalace agenta pomocí ve službě Azure Automation DSC

Následující ukázkový skript můžete použít k instalaci agenta pomocí Azure Automation DSC.   Pokud nemáte účet Automation, přečtěte si téma [Začínáme s Azure Automation](../automation/automation-offering-get-started.md) vám pomohou pochopit požadavky a kroky pro vytvoření účtu Automation pomocí Automation DSC je požadována.  Pokud nejste obeznámeni s Automation DSC, přečtěte si [Začínáme se službou Automation DSC](../automation/automation-dsc-getting-started.md).

Následující příklad nainstaluje agenta 64bitovým kompilátorem identifikované `URI` hodnotu. Můžete také použít 32bitovou verzi tak, že nahradíte hodnotu identifikátoru URI. Identifikátory URI pro obě verze jsou:

- Agent Windows 64-bit – https://go.microsoft.com/fwlink/?LinkId=828603
- Agent Windows 32-bit – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Příklad tohoto postupu a skripty nepodporuje upgrade agenta už nasadili do počítače Windows.

32bitové a 64bitové verze balíček agenta mají jiný produkt kódy a vydání nové verze také mít jedinečnou hodnotu.  Kód produktu je identifikátor GUID, který je základní identifikátor aplikace nebo produktu a je reprezentována Instalační služby systému Windows **ProductCode** vlastnost.  `ProductId value` v **MMAgent.ps1** skriptu musí odpovídat kód produktu z instalačního balíčku 32bitová nebo 64bitová verze agenta.

Chcete-li načíst kód přímo z instalačního balíčku agenta, můžete použít Orca.exe z [Windows vývojáři sady SDK součásti pro Windows Installer](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , který je součástí sady Windows Software Development Kit nebo pomocí Prostředí PowerShell následující [ukázkový skript](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napsané pomocí Microsoft Valuable Professional (MVP).  Kterýkoliv přístup, musíte nejprve k extrakci **MOMagent.msi** souboru z instalačního balíčku MMASetup.  To je ukázáno dříve v prvním kroku v části [instalace agenta pomocí příkazového řádku](#install-the-agent-using-the-command-line).  

1. Import DSC xPSDesiredStateConfiguration modulu z [ http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Azure Automation.  
2.  Vytvoření proměnných assetů Azure Automation pro *OPSINSIGHTS_WS_ID* a *OPSINSIGHTS_WS_KEY*. Nastavte *OPSINSIGHTS_WS_ID* ID pracovního prostoru Log Analytics a nastavte *OPSINSIGHTS_WS_KEY* na primární klíč pracovního prostoru.
3.  Zkopírujte skript a uložte ho jako MMAgent.ps1

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. [Importovat konfigurační skript MMAgent.ps1](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) do účtu Automation. 
5. [Přiřaďte počítači Windows nebo uzlu](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) ke konfiguraci. Během 15 minut uzel ověří jeho konfigurace a agent se vloží do uzlu.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ověřit připojení agenta k Log Analytics

Po dokončení instalace agenta ověřením úspěšně připojen a generování sestav můžete provést dvěma způsoby.  

Z počítače v **ovládací panely**, vyhledejte položku **agenta Microsoft Monitoring Agent**.  Vyberte ho a na **Azure Log Analytics** kartě agent by se zobrazit zpráva s oznámením: **agenta Microsoft Monitoring Agent úspěšně připojilo ke službě Microsoft Operations Management Suite.**<br><br> ![Stav připojení MMA k Log Analytics](media/log-analytics-agent-windows/log-analytics-mma-laworkspace-status.png)

Můžete také provádět jednoduché protokolu Hledat na webu Azure Portal.  

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.  
2. Na stránce pracovního prostoru Log Analytics vyberte pracovní prostor cíle a pak vyberte **prohledávání protokolů** dlaždici. 
2. V podokně prohledávání protokolu v aplikaci zadejte do pole dotazu:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Ve výsledcích hledání vrátí měli byste vidět záznamy prezenčního signálu pro počítač oznamující, že je připojený a vytváření sestav ve službě.   

## <a name="next-steps"></a>Další postup

Kontrola [Správa a údržba agenta Log Analytics pro Windows a Linux](log-analytics-agent-manage.md) Další informace o tom, jak Správa agenta během životního cyklu jeho nasazení na vašich počítačích.  
