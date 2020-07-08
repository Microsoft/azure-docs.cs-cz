---
title: Připojit počítače s Windows k Azure Monitor | Microsoft Docs
description: Tento článek popisuje, jak připojit počítače s Windows hostované v jiných cloudech nebo místně, aby se Azure Monitor s agentem Log Analytics pro Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 644d1094ec57e148804941297d50398e36b1b068
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996430"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Připojení počítačů s Windows k Azure Monitor

Aby bylo možné monitorovat a spravovat virtuální počítače nebo fyzické počítače v místním datovém centru nebo jiném cloudovém prostředí pomocí Azure Monitor, je nutné nasadit agenta Log Analytics (označovaný také jako Microsoft Monitoring Agent (MMA)) a nakonfigurovat ho tak, aby se nahlásil do jednoho nebo více Log Analytics pracovních prostorů. Agent také podporuje Hybrid Runbook Worker role pro Azure Automation.  

Na monitorovaný počítač se systémem Windows je agent uveden jako služba Microsoft Monitoring Agent. Služba Microsoft Monitoring Agent shromažďuje události ze souborů protokolů a protokolu událostí systému Windows, údajů o výkonu a další telemetrie. I v případě, že Agent nemůže komunikovat s Azure Monitor IT zprávy, Agent pokračuje v běhu a zařadí shromážděná data do fronty na disk monitorovaného počítače. Po obnovení připojení služba Microsoft Monitoring Agent odesílá shromážděná data službě.

Agent může být nainstalován pomocí jedné z následujících metod. Většina instalací využívá jejich kombinaci. Díky tomu je možné nainstalovat různé sady počítačů tak, aby vyhovovaly potřebám.  Podrobnosti o použití jednotlivých metod jsou uvedené dále v článku.

* Ruční instalace. Instalační program se ručně spustí na počítači pomocí Průvodce instalací nástroje z příkazového řádku nebo nasazení pomocí existujícího nástroje pro distribuci softwaru.
* Azure Automation konfiguraci požadovaného stavu (DSC). Použití DSC v Azure Automation se skriptem pro počítače se systémem Windows, které jsou již ve vašem prostředí nasazeny.  
* PowerShellový skript.
* Šablona Správce prostředků pro virtuální počítače, na kterých běží místní Windows, v Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) závisí na Microsoft Monitoring Agent (označuje se taky jako Log Analytics Agent pro Windows) a nainstaluje a nakonfiguruje ho tak, aby se v rámci nasazení nahlásil do pracovního prostoru Log Analytics. ASC obsahuje možnost automatického zřizování, která umožňuje automatickou instalaci Log Analyticsho agenta Windows na všech virtuálních počítačích ve vašem předplatném a nakonfiguruje ho, aby se nahlásil do konkrétního pracovního prostoru. Další informace o této možnosti najdete v tématu [Povolení automatického zřizování agenta Log Analytics](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Pokud potřebujete nakonfigurovat agenta tak, aby vydával zprávy do více než jednoho pracovního prostoru, nemůžete to provést při počáteční instalaci, a to až po aktualizaci nastavení z ovládacích panelů nebo PowerShellu, jak je popsáno v tématu [Přidání nebo odebrání pracovního prostoru](agent-manage.md#adding-or-removing-a-workspace).  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](log-analytics-agent.md#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent.md#network-requirements).

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta Log Analytics pro Windows budete potřebovat ID a klíč pracovního prostoru pro pracovní prostor Log Analytics.  Tyto informace se vyžadují při instalaci z každé metody instalace, aby bylo možné správně nakonfigurovat agenta, a zajistit, aby mohl úspěšně komunikovat s Azure Monitor v cloudu pro státní správu Azure Commercial a USA. 

1. V Azure Portal vyhledejte a vyberte **Log Analytics pracovní prostory**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, do kterého chcete agenta nakonfigurovat.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.   
5. Zkopírujte a vložte do svého oblíbeného editoru, **ID pracovního prostoru** a **primární klíč**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Nakonfigurovat agenta na používání protokolu TLS 1,2
Chcete-li nakonfigurovat použití protokolu [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) pro komunikaci mezi agentem Windows a službou Log Analytics, můžete postupovat podle následujících kroků, abyste mohli povolit, aby byl agent nainstalován na virtuálním počítači nebo následně.

>[!NOTE]
>Pokud konfigurujete virtuální počítač s Windows Serverem 2008 SP2 x64 pro použití TLS 1,2, před provedením následujících kroků musíte nejdřív nainstalovat tuto [aktualizaci podpory podepisování kódu SHA-2](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) . 
>

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE \system\currentcontrolset\control\securityproviders\schannel\protocols**
2. Vytvoření podklíče v části **protokoly** pro TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Vytvořte podklíč **klienta** pod podklíčem protokolu TLS 1,2 verze, který jste vytvořili dříve. Například **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. V části **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ klient**vytvořte následující hodnoty DWORD:

    * **Povoleno** [Value = 1]
    * **DisabledByDefault** [hodnota = 0]  

Nakonfigurujte .NET Framework 4,6 nebo novější, aby podporovaly zabezpečenou kryptografii, protože ve výchozím nastavení je zakázaná. [Silná kryptografie](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) používá bezpečnější síťové protokoly jako TLS 1,2 a blokuje protokoly, které nejsou zabezpečené. 

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE \software\microsoft \\ . NETFramework\v4.0.30319**.  
2. V tomto podklíči vytvořte hodnotu DWORD **do schusestrongcrypto** s hodnotou **1**.  
3. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE \software\wow6432node\microsoft \\ . NETFramework\v4.0.30319**.  
4. V tomto podklíči vytvořte hodnotu DWORD **do schusestrongcrypto** s hodnotou **1**. 
5. Restartujte systém, aby se nastavení projevilo. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalace agenta pomocí Průvodce instalací
Následující postup nainstaluje a nakonfiguruje agenta Log Analytics v Azure a Azure Government cloudu pomocí Průvodce instalací agenta v počítači. Pokud se chcete dozvědět, jak nakonfigurovat agenta tak, aby se také nahlásil do System Center Operations Manager skupiny pro správu, přečtěte si téma [nasazení agenta Operations Manager pomocí Průvodce instalací agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. V pracovním prostoru Log Analytics klikněte na stránce **Windows servery** , na kterou jste přešli na předchozí, vyberte příslušnou verzi **agenta pro Windows** ke stažení, která se má stáhnout v závislosti na architektuře procesoru operačního systému Windows.   
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

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Pokud chcete potvrdit, že je hlášení Log Analytics, přečtěte si téma [ověření připojení agenta k Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalace agenta pomocí příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček.  Instalační program pro agenta a podpůrné soubory jsou obsaženy v balíčku a je třeba jej extrahovat, aby bylo možné správně nainstalovat pomocí příkazového řádku zobrazeného v následujících příkladech.    

>[!NOTE]
>Pokud chcete upgradovat agenta, je nutné použít rozhraní API pro Log Analytics skriptování. Další informace najdete v tématu [Správa a údržba agenta Log Analytics pro systém Windows a Linux](agent-manage.md) .

V následující tabulce jsou vysvětlené konkrétní parametry podporované instalačním programem agenta, včetně nasazení pomocí Automatizace DSC.

|Možnosti specifické pro MMA                   |Poznámky         |
|---------------------------------------|--------------|
| NOAPM=1                               | Volitelný parametr. Nainstaluje agenta bez monitorování výkonu aplikace .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurace agenta pro hlášení do pracovního prostoru                |
|OPINSIGHTS_WORKSPACE_ID                | ID pracovního prostoru (GUID) pro pracovní prostor, který se má přidat                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klíč pracovního prostoru, který se používá k prvotnímu ověření v pracovním prostoru |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Zadejte cloudové prostředí, ve kterém se nachází pracovní prostor. <br> 0 = komerční cloud Azure (výchozí) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | Identifikátor URI, který se má použít pro proxy |
|OPINSIGHTS_PROXY_USERNAME               | Uživatelské jméno pro přístup k ověřenému proxy serveru |
|OPINSIGHTS_PROXY_PASSWORD               | Heslo pro přístup k ověřenému proxy serveru |

1. K extrakci instalačních souborů agenta se spustí příkazový řádek se zvýšenými oprávněními `MMASetup-<platform>.exe /c` a zobrazí výzvu k zadání cesty k extrakci souborů.  Alternativně můžete zadat cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Pokud chcete nainstalovat agenta v tichém režimu a nakonfigurovat ho tak, aby se nahlásil k pracovnímu prostoru ve službě Azure Commercial Cloud, ze složky, do které jste extrahovali instalační soubory, zadejte: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   nebo chcete-li nakonfigurovat agenta tak, aby hlásil do cloudu Azure USA pro státní správu, zadejte: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Řetězcové hodnoty parametrů *OPINSIGHTS_WORKSPACE_ID* a *OPINSIGHTS_WORKSPACE_KEY* musí být zapouzdřeny do dvojitých uvozovek, aby bylo možné instalační služba systému Windows interprit jako platné možnosti pro balíček. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Nainstalujte agenta pomocí DSC v Azure Automation

Pomocí následujícího příkladu skriptu můžete nainstalovat agenta pomocí Azure Automation DSC.   Pokud nemáte účet Automation, přečtěte si téma Začínáme [s Azure Automation](/azure/automation/) , abyste pochopili požadavky a kroky pro vytvoření účtu Automation, který je potřeba před použitím automatizace DSC.  Pokud nejste obeznámeni s Automatizace DSC, přečtěte si téma [Začínáme s Automatizace DSC](../../automation/automation-dsc-getting-started.md).

Následující příklad nainstaluje agenta 64, který je identifikován `URI` hodnotou. Verzi 32 můžete použít i tak, že nahradíte hodnotu identifikátoru URI. Identifikátory URI pro obě verze jsou:

- Windows 64 – bit Agent-https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 – bit Agent-https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Tento postup a příklad skriptu nepodporuje upgrade agenta, který už je nasazený na počítači s Windows.

32 bitové a 64 verze balíčku agenta mají různé kódy produktů a nové verze jsou také jedinečné.  Kód produktu je identifikátor GUID, který je hlavní identifikací aplikace nebo produktu a který je reprezentován vlastností Instalační služba systému Windows **ProductCode** .  `ProductId`Hodnota ve **MMAgent.ps1m** skriptu musí odpovídat kódu produktu z balíčku 32 nebo 64 instalačního programu agenta.

Pokud chcete získat kód produktu přímo z instalačního balíčku agenta, můžete použít Orca.exe ze [součástí Windows SDK pro instalační služba systému Windows vývojářů](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , kteří jsou součástí sady Windows Software Development Kit, nebo pomocí PowerShellu, který následuje [ukázkový skript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napsaný společností MVP (Microsoft hodnotný Professional).  Pro oba tyto metody musíte nejprve extrahovat soubor **MOMagent.msi** z instalačního balíčku MMASetup.  Tento postup je uveden výše v prvním kroku v části [instalace agenta pomocí příkazového řádku](#install-the-agent-using-the-command-line).  

1. Importujte modul xPSDesiredStateConfiguration DSC z nástroje [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Azure Automation.  
2.    Vytvoří Azure Automation variabilní prostředky pro *OPSINSIGHTS_WS_ID* a *OPSINSIGHTS_WS_KEY*. Nastavte *OPSINSIGHTS_WS_ID* na ID vašeho pracovního prostoru Log Analytics a nastavte *OPSINSIGHTS_WS_KEY* na primární klíč vašeho pracovního prostoru.
3.    Zkopírujte skript a uložte ho jako MMAgent.ps1.

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

4. Aktualizujte `ProductId` hodnotu ve skriptu pomocí kódu produktu extrahovaného z nejnovější verze instalačního balíčku agenta pomocí výše doporučených metod. 
5. [Importujte konfigurační skript MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) do svého účtu Automation. 
6. Přiřaďte ke konfiguraci [počítač se systémem Windows nebo uzel](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) . Během 15 minut uzel zkontroluje svou konfiguraci a Agent se odešle do uzlu.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ověření připojení agenta k Log Analytics

Po dokončení instalace agenta ověřte, zda je úspěšné připojení a vytváření sestav lze provést dvěma způsoby.  

V **Ovládacích panelech** na počítači vyhledejte položku **Microsoft Monitoring Agent**.  Vyberte ho a na kartě **Azure Log Analytics** by měl agent zobrazit zprávu informující o tom, **že Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Operations Management Suite.**<br><br> ![Stav připojení MMA k Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

V Azure Portal můžete také provádět jednoduché dotazy protokolu.  

1. V Azure Portal vyhledejte a vyberte **monitor**.
1. V nabídce vyberte **protokoly** .
1. V podokně **protokoly** zadejte do pole dotazu:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro počítač, který označuje, že je připojený a hlásí službu.   

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Správa a údržba agenta Log Analytics pro systémy Windows a Linux](agent-manage.md) , kde se dozvíte, jak překonfigurovat, upgradovat nebo odebrat agenta z virtuálního počítače.

- Pokud narazíte na problémy při instalaci nebo správě agenta, přečtěte si téma [řešení potíží s agentem Windows](agent-windows-troubleshoot.md) .
