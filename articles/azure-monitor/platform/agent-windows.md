---
title: Připojení počítačů s Windows k Azure Monitoru | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak připojit počítače se systémem Windows hostované v jiných cloudech nebo místně k Azure Monitoru s agentem Log Analytics pro Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21efb16cf519d4bcad520af1c7d8818f36a77218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275032"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Připojení počítačů s Windows ke službě Azure Monitor

Chcete-li pomocí Služby Azure Monitor monitorovat a spravovat virtuální počítače nebo fyzické počítače v místním datovém centru nebo jiném cloudovém prostředí, musíte nasadit agenta Log Analytics (označované také jako Microsoft Monitoring Agent (MMA)) a nakonfigurovat ho tak, aby sestavy do jednoho nebo více pracovních prostorů Log Analytics. Agent také podporuje roli hybridního pracovníka runbooku pro Azure Automation.  

V monitorovaném počítači se systémem Windows je agent uveden jako služba Microsoft Monitoring Agent. Služba Microsoft Monitoring Agent shromažďuje události ze souborů protokolu a protokolu událostí systému Windows, dat o výkonu a další telemetrie. I v případě, že agent není schopen komunikovat s Azure Monitor, které hlásí, agent nadále spustit a fronty shromážděná data na disku monitorovaného počítače. Po obnovení připojení odešle služba Microsoft Monitoring Agent do služby shromážděná data.

Agent může být nainstalován pomocí jedné z následujících metod. Většina instalací využívá jejich kombinaci. Díky tomu je možné nainstalovat různé sady počítačů tak, aby vyhovovaly potřebám.  Podrobnosti o použití jednotlivých metod jsou uvedeny dále v článku.

* Ruční instalace. Instalační program je v počítači spuštěn ručně pomocí průvodce instalací, z příkazového řádku nebo nasazen pomocí existujícího nástroje pro distribuci softwaru.
* Azure Automation požadovanou konfiguraci stavu (DSC). Použití DSC v Azure Automation se skriptem pro počítače s Windows, které jsou už nasazené ve vašem prostředí.  
* Skript prostředí PowerShell.
* Šablona Správce prostředků pro virtuální počítače se systémem Windows v místním prostředí v Azure Stacku. 

>[!NOTE]
>Azure Security Center (ASC) závisí na Microsoft Monitoring Agent (označované také jako agent Log Analytics Windows) a nainstaluje a nakonfiguruje jej tak, aby sestavy do pracovního prostoru Log Analytics jako součást jeho nasazení. ASC obsahuje možnost automatického zřizování, která umožňuje automatickou instalaci agenta Systému Windows Log Analytics na všechny virtuální počítače ve vašem předplatném a nakonfiguruje ho tak, aby se hlásil do určitého pracovního prostoru. Další informace o této možnosti naleznete [v tématu Povolení automatického zřizování agenta Analýzy protokolů](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Pokud potřebujete nakonfigurovat agenta tak, aby se hlásil do více než jednoho pracovního prostoru, nelze to provést během počátečníinstalace, pouze poté aktualizací nastavení z Ovládacích panelů nebo prostředí PowerShell, jak je popsáno v [části Přidání nebo odebrání pracovního prostoru](agent-manage.md#adding-or-removing-a-workspace).  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](log-analytics-agent.md#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta Analýzy protokolů pro Systém Windows potřebujete ID pracovního prostoru a klíč pro pracovní prostor Log Analytics.  Tyto informace jsou vyžadovány během instalace z každé metody instalace správně nakonfigurovat agenta a zajistit, že může úspěšně komunikovat s Azure Monitor v Azure komerční a us government cloud. 

1. Na webu Azure Portal vyhledejte a vyberte **pracovní prostory Log Analytics**.
2. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor, který chcete nakonfigurovat agenta, který má být podaný.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.   
5. Zkopírujte a vložte do svého oblíbeného editoru, **ID pracovního prostoru** a **primárního klíče**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurace agenta pro použití protokolu TLS 1.2
Chcete-li nakonfigurovat použití protokolu [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) pro komunikaci mezi agentem systému Windows a službou Log Analytics, můžete provést následující kroky a povolit před instalací agenta na virtuální mši nebo později.

>[!NOTE]
>Pokud konfigurujete virtuální hospodařilose se systémem Windows Server 2008 SP2 x64 tak, aby používal tls 1.2, musíte nejprve nainstalovat následující [aktualizaci podpory podepisování kódu SHA-2](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) před provedením následujících kroků. 
>

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Vytvoření podklíče v části Protokoly pro **Protokoly** TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Vytvořte **podklíč klienta** pod podklíčem verze protokolu TLS 1.2, který jste vytvořili dříve. Například **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Vytvořte následující hodnoty DWORD v části **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Povoleno** [Hodnota = 1]
    * **DisabledByDefault** [Hodnota = 0]  

Nakonfigurujte rozhraní .NET Framework 4.6 nebo novější tak, aby podporovalo zabezpečenou kryptografii, protože ve výchozím nastavení je zakázána. [Silná kryptografie](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) používá bezpečnější síťové protokoly, jako je TLS 1.2, a blokuje protokoly, které nejsou zabezpečené. 

1. Vyhledejte následující podklíč registru: **\\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft . NETFramework\v4.0.30319**.  
2. Vytvořte hodnotu DWORD **SchUseStrongCrypto** pod tímto podklíčem s hodnotou **1**.  
3. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Vytvořte hodnotu DWORD **SchUseStrongCrypto** pod tímto podklíčem s hodnotou **1**. 
5. Chcete-li, aby se nastavení projevilo, restartujte systém. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalace agenta pomocí průvodce instalací
Následující kroky nainstalují a nakonfigurují agenta Log Analytics v cloudu Azure a Azure Government pomocí průvodce nastavením pro agenta ve vašem počítači. Pokud se chcete dozvědět, jak nakonfigurovat agenta tak, aby se také hlásil do skupiny správy nástroje System Center Operations Manager, [přečtěte si informace o nasazení agenta agenta pomocí Průvodce instalací agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. V pracovním prostoru Log Analytics vyberte na stránce **Windows Servers,** na kterou jste přešli dříve, příslušnou verzi **systému Stažení agenta systému Windows,** kterou chcete stáhnout v závislosti na architektuře procesoru operačního systému Windows.   
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.   
6. Na stránce **Azure Log Analytics** postupujte následovně:
   1. Vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (primární klíč)**, které jste si zkopírovali dříve.  Pokud se má počítač hlásit do pracovního prostoru služby Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.  
7. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.<br><br> ![vložení ID pracovního prostoru a primárního klíče](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
9. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Chcete-li potvrdit, že se hlásí do služby Log Analytics, přečtěte [si ověřte připojení agenta k log analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalace agenta pomocí příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček.  Instalační program pro agenta a podpůrné soubory jsou obsaženy v balíčku a je třeba extrahovat, aby bylo možné správně nainstalovat pomocí příkazového řádku uvedeného v následujících příkladech.    

>[!NOTE]
>Pokud chcete upgradovat agenta, musíte použít rozhraní API pro skriptování Analýzy protokolů. Další informace najdete v tématu [Správa a údržba agenta Log Analytics pro Windows a Linux.](agent-manage.md)

V následující tabulce jsou zvýrazněny konkrétní parametry podporované nastavením agenta, včetně nasazení pomocí automatizace DSC.

|Možnosti specifické pro MMA                   |Poznámky         |
|---------------------------------------|--------------|
| NOAPM=1                               | Volitelný parametr. Nainstaluje agenta bez monitorování výkonu aplikace .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurace agenta pro hlášení do pracovního prostoru                |
|OPINSIGHTS_WORKSPACE_ID                | ID pracovního prostoru (guid) pro pracovní prostor, který chcete přidat                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klíč pracovního prostoru použitý k prvotnímu ověření pomocí pracovního prostoru |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Určení cloudového prostředí, ve kterém se pracovní prostor nachází <br> 0 = Komerční cloud Azure (výchozí) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | Identifikátor URI pro proxy server |
|OPINSIGHTS_PROXY_USERNAME               | Uživatelské jméno pro přístup k ověřenému proxy serveru |
|OPINSIGHTS_PROXY_PASSWORD               | Heslo pro přístup k ověřenému proxy serveru |

1. Chcete-li extrahovat instalační soubory agenta, z příkazového řádku se zvýšenými oprávněními se `MMASetup-<platform>.exe /c` zobrazí výzva k získání cesty k extrahování souborů.  Alternativně můžete určit cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Chcete-li agenta bezobslužně nainstalovat a nakonfigurovat tak, aby se hlásil do pracovního prostoru v komerčním cloudu Azure, ze složky, kterou jste extrahovali instalační soubory, abyste zadali: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   nebo nakonfigurovat agenta tak, aby reportoval v cloudu Azure US Government, zadejte: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Hodnoty řetězce pro parametry *OPINSIGHTS_WORKSPACE_ID* a *OPINSIGHTS_WORKSPACE_KEY* je třeba zapouzdřit do dvojitých uvozovek, aby instalační služba systému Windows inkasovala jako platné možnosti balíčku. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalace agenta pomocí DSC v Azure Automation

Můžete použít následující příklad skriptu k instalaci agenta pomocí Azure Automation DSC.   Pokud nemáte účet automatizace, najdete [v tématu Začínáme s Azure Automation](/azure/automation/) pochopit požadavky a kroky pro vytvoření účtu automatizace požadované před použitím automatizace DSC.  Pokud nejste obeznámeni s automatizací DSC, přečtěte [si přečtěte si Začínáme s automatizací DSC](../../automation/automation-dsc-getting-started.md).

Následující příklad nainstaluje 64bitového agenta `URI` identifikovaného hodnotou. 32bitovou verzi můžete také použít nahrazením hodnoty URI. Identifikátory URI pro obě verze jsou:

- Windows 64-bit agent -https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bit agent -https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Tento postup a příklad skriptu nepodporuje inovaci agenta, který je již nasazen do počítače se systémem Windows.

32bitové a 64bitové verze balíčku agenta mají různé kódy produktů a nové vydané verze mají také jedinečnou hodnotu.  Kód produktu je identifikátor GUID, který je hlavní identifikací aplikace nebo produktu a je reprezentován vlastností Windows Installer **ProductCode.**  Hodnota `ProductId` ve skriptu **MMAgent.ps1** musí odpovídat kódu produktu z 32bitového nebo 64bitového balíčku instalátoru agenta.

Chcete-li načíst kód produktu z instalačního balíčku agenta přímo, můžete použít orca.exe z [windows sdk komponenty pro vývojáře Instalační služby systému Windows,](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) který je součástí sady Windows Software Development Kit nebo pomocí prostředí PowerShell po [příklad skriptnapsaný](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) Microsoft Valuable Professional (MVP).  Pro oba přístupy je třeba nejprve extrahovat soubor **MOMagent.msi** z instalačního balíčku MMASetup.  To je uvedeno dříve v prvním kroku v části [Instalace agenta pomocí příkazového řádku](#install-the-agent-using-the-command-line).  

1. Importujte modul DSC xPSDesiredStateConfiguration z [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) aplikace Azure Automation.  
2.  Vytvořte datové zdroje proměnných Azure Automation pro *OPSINSIGHTS_WS_ID* a *OPSINSIGHTS_WS_KEY*. Nastavte *OPSINSIGHTS_WS_ID* na ID pracovního prostoru Analýzy protokolů a nastavte *OPSINSIGHTS_WS_KEY* na primární klíč pracovního prostoru.
3.  Zkopírujte skript a uložte jej jako MMAgent.ps1.

    ```powershell
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

4. Aktualizujte `ProductId` hodnotu ve skriptu s kódem produktu extrahovaným z nejnovější verze instalačního balíčku agenta pomocí dříve doporučených metod. 
5. [Importujte konfigurační skript MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) do svého účtu Automation. 
5. [Přiřaďte ke konfiguraci počítač nebo uzel se systémem Windows.](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) Během 15 minut uzel zkontroluje jeho konfiguraci a agent je posunut do uzlu.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ověření připojení agenta k log analytics

Po dokončení instalace agenta lze ověřit jeho úspěšné připojení a vykazování dvěma způsoby.  

V **Ovládacích panelech** na počítači vyhledejte položku **Microsoft Monitoring Agent**.  Vyberte ji a na kartě **Azure Log Analytics** by měl agent zobrazit zprávu s **nápisem: Agent monitorování společnosti Microsoft se úspěšně připojil ke službě Microsoft Operations Management Suite.**<br><br> ![Stav připojení MMA k Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Můžete také provést jednoduchý dotaz protokolu na webu Azure Portal.  

1. Na webu Azure Portal vyhledejte a vyberte **Sledovat**.
1. V nabídce vyberte **Protokoly.**
1. V podokně **Protokoly** v poli dotazu:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Ve výsledcích hledání, které byly vráceny, by se měly zobrazit záznamy prezenčního signálu počítače, které označují, že je připojen a hlásí se ke službě.   

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [správu a údržbu agenta Log Analytics pro Windows a Linux,](agent-manage.md) abyste zjistili, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Pokud při instalaci nebo správě agenta narazíte na problémy, [zkontrolujte poradce při potížích s agentem](agent-windows-troubleshoot.md) systému Windows.
