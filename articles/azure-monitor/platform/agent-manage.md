---
title: Správa agenta Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje úlohy řízení, které se obvykle provádí během životního cyklu aplikace Microsoft Monitoring Agent (MMA) nasadit na počítače s.
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
ms.date: 03/30/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a18e45ddd2e07945a91f997b313013c6a719c869
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642876"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Správu a údržbu agenta Log Analytics pro Windows a Linux

Po počátečním nasazení nástroje Windows nebo Linux agenta pro Log Analytics budete muset změnit konfiguraci agenta nebo jej odeberte z počítače, pokud bylo dosaženo vyřazení z provozu fázi životního cyklu.  Tyto úlohy běžné údržby můžete snadno spravovat ručně nebo pomocí automatizace, což snižuje provozní chyby a výdaje.

## <a name="adding-or-removing-a-workspace"></a>Přidání nebo odebrání pracovního prostoru 

### <a name="windows-agent"></a>Agenta Windows

#### <a name="update-settings-from-control-panel"></a>Aktualizovat nastavení v Ovládacích panelech

1. Přihlaste se k počítači pomocí účtu, který má práva správce.
2. Otevřete **Ovládací panely**.
3. Vyberte **agenta Microsoft Monitoring Agent** a potom klikněte na tlačítko **Azure Log Analytics** kartu.
4. Pokud odebrání pracovního prostoru, vyberte ji a pak klikněte na **odebrat**. Tento krok opakujte pro všechny ostatní pracovní prostor má agent přestanou předávat hlášení.
5. Pokud přidáváte pracovní prostor, klikněte na tlačítko **přidat** a na **přidat pracovní prostor Log Analytics** dialogové okno, vložení ID pracovního prostoru a klíč pracovního prostoru (primární klíč). Pokud má počítač hlásit do pracovního prostoru Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu Azure Cloud Azure US Government. 
6. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="remove-a-workspace-using-powershell"></a>Odebrat pracovní prostor pomocí Powershellu 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Přidat pracovní prostor v Azure komerční pomocí Powershellu 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Přidat pracovní prostor v Azure pro státní správu USA pomocí Powershellu 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Pokud jste použili na příkazovém řádku nebo skriptu dříve pro instalaci nebo konfiguraci agenta `EnableAzureOperationalInsights` byl nahrazen `AddCloudWorkspace` a `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agenta pro Linux
Následující kroky ukazují, jak změnit konfiguraci agenta pro Linux, pokud se rozhodnete ho zaregistrovat pomocí jiného pracovního prostoru nebo chcete odebrat pracovní prostor z jeho konfigurace.  

1.  Pokud chcete ověřit, že je zaregistrovaný do pracovního prostoru, spusťte následující příkaz.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Měla by vrátit stav podobně jako v následujícím příkladu- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Je důležité, že také zobrazuje stav je agent spuštěn, jinak nebude úspěšně dokončit následující kroky a znovu nakonfigurujte agenta.  

2. Pokud je už zaregistrovaný s pracovním prostorem, odeberte registrované pracovní prostor spuštěním následujícího příkazu.  Jinak Pokud není zaregistrovaný, pokračujte k dalšímu kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Pokud chcete zaregistrovat s jiným pracovním prostorem, spusťte příkaz `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. Chcete-li ověřit změny trvalo vliv, spusťte příkaz.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Měla by vrátit stav podobně jako v následujícím příkladu- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Služba agenta není nutné restartovat, aby se změny projevily.

## <a name="update-proxy-settings"></a>Aktualizovat nastavení proxy serveru 
Konfigurace agenta pro komunikaci ve službě pomocí proxy serveru nebo [Log Analytics gateway](gateway.md) po nasazení, použijte jednu z následujících metod k dokončení této úlohy.

### <a name="windows-agent"></a>Agenta Windows

#### <a name="update-settings-using-control-panel"></a>Nastavení aktualizace pomocí ovládacích panelů

1. Přihlaste se k počítači pomocí účtu, který má práva správce.
2. Otevřete **Ovládací panely**.
3. Vyberte **agenta Microsoft Monitoring Agent** a potom klikněte na tlačítko **nastavení proxy serveru** kartu.
4. Klikněte na tlačítko **používat proxy server** a zadejte adresu URL a port číslo proxy serveru nebo brány. Pokud váš proxy server nebo brána Log Analytics vyžaduje ověření, zadejte uživatelské jméno a heslo k ověření a potom klikněte na tlačítko **OK**. 

#### <a name="update-settings-using-powershell"></a>Aktualizovat nastavení pomocí Powershellu 

Zkopírujte následující ukázkový kód Powershellu, aktualizujte ji pomocí informací specifických pro vaše prostředí a uložte s příponou názvu souboru PS1.  Spusťte skript na každém počítači, který se připojuje přímo ke službě Log Analytics.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Agenta pro Linux
Pokud vaše počítače s Linuxem potřebují komunikovat přes proxy server nebo brána Log Analytics, proveďte následující kroky.  Hodnota konfigurace proxy má následující syntaxi: `[protocol://][user:password@]proxyhost[:port]`.  Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru.

1. Spuštěním následujících příkazů upravte soubor `/etc/opt/microsoft/omsagent/proxy.conf` a změňte hodnoty na vaše konkrétní nastavení.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Restartujte agenta spuštěním následujícího příkazu: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Odinstalace agenta
Pomocí jednoho z následujících postupů odinstalujte agenta Windows nebo Linux pomocí Průvodce příkazového řádku nebo instalační program.

### <a name="windows-agent"></a>Agenta Windows

#### <a name="uninstall-from-control-panel"></a>Odinstalujte z ovládacích panelů
1. Přihlaste se k počítači pomocí účtu, který má práva správce.  
2. V **ovládací panely**, klikněte na tlačítko **programy a funkce**.
3. V **programy a funkce**, klikněte na tlačítko **agenta Microsoft Monitoring Agent**, klikněte na tlačítko **odinstalovat**a potom klikněte na tlačítko **Ano**.

>[!NOTE]
>Průvodce instalací agenta můžete spustit také dvojitým **MMASetup -<platform>.exe**, což je k dispozici ke stažení z pracovního prostoru na webu Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstalovat z příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček vytvořen s IExpress.  Instalační program pro agenta a jeho podpůrné soubory jsou obsažené v balíčku a potřeba extrahovat, aby bylo možné správně, odinstalujte ji pomocí příkazového řádku je znázorněno v následujícím příkladu. 

1. Přihlaste se k počítači pomocí účtu, který má práva správce.  
2. K extrakci instalačních souborů agenta z příkazového řádku se zvýšenými oprávněními spusťte `extract MMASetup-<platform>.exe` a zobrazí výzvu pro cestu k extrakci souborů.  Případně lze zadat cestu předáním argumentů `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Další informace o příkazového řádku swtiches IExpress podporuje, najdete v článku [přepínače příkazového řádku pro IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) a pak aktualizujte příklad tak, aby odpovídala vašim potřebám.
3. Na příkazovém řádku zadejte `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agenta pro Linux
Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz.  Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurace agenta na generování sestav pro skupinu pro správu nástroje Operations Manager

### <a name="windows-agent"></a>Agenta Windows
Proveďte následující kroky konfigurace agenta Log Analytics pro Windows oznamuje ke skupině pro správu System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

1. Přihlaste se k počítači pomocí účtu, který má práva správce.
2. Otevřete **Ovládací panely**. 
3. Klikněte na tlačítko **agenta Microsoft Monitoring Agent** a potom klikněte na tlačítko **nástroje Operations Manager** kartu.
4. Pokud vaše servery nástroje Operations Manager mají integrace se službou Active Directory, klikněte na tlačítko **automaticky aktualizovat přiřazení skupin pro správu ze služby AD DS**.
5. Klikněte na tlačítko **přidat** otevřít **přidat skupinu pro správu** dialogové okno.
6. V **název skupiny pro správu** pole, zadejte název skupiny pro správu.
7. V **primární server pro správu** pole, zadejte název počítače z primární server pro správu.
8. V **port serveru pro správu** zadejte číslo portu TCP.
9. V části **účet Agent Action Account**, zvolte účet místní systém nebo účet místní domény.
10. Klikněte na tlačítko **OK** zavřete **přidat skupinu pro správu** dialogové okno a potom klikněte na **OK** zavřete **vlastnosti služby Microsoft Monitoring Agent** Dialogové okno.

### <a name="linux-agent"></a>Agenta pro Linux
Proveďte následující kroky konfigurace agenta Log Analytics pro Linux pro hlášení skupinu pro správu System Center Operations Manager. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Upravte soubor `/etc/opt/omi/conf/omiserver.conf`
2. Ujistěte se, že řádek začínající příkazem `httpsport=` definuje port 1270. Například: `httpsport=1270`
3. Restartujte OMI server: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Další postup

Kontrola [řešení potíží s agenta pro Linux](agent-linux-troubleshoot.md) Pokud narazíte na problémy při instalaci a správě agenta.  
