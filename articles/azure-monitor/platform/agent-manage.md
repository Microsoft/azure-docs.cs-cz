---
title: Správa agenta Azure Log Analytics
description: Tento článek popisuje různé úlohy správy, které budete obvykle provádět během životního cyklu agenta Služby Windows nebo Linux log Analytics nasazeného v počítači.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275097"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Správa a údržba agenta Log Analytics pro Windows a Linux

Po počátečním nasazení agenta Služby Windows nebo Linux pro analýzu protokolů ve službě Azure Monitor může být nutné agenta znovu nakonfigurovat, upgradovat nebo odebrat z počítače, pokud v jeho životním cyklu dosáhl fáze vyřazení. Tyto rutinní úlohy údržby můžete snadno spravovat ručně nebo prostřednictvím automatizace, což snižuje provozní chyby i náklady.

## <a name="upgrading-agent"></a>Inovace agenta

Agent a log Analytics pro Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na scénáři nasazení a prostředí, ve které virtuální počítač běží. Následující metody lze použít k inovaci agenta.

| Prostředí | Způsob instalace | Metoda upgradu |
|--------|----------|-------------|
| Virtuální počítač Azure | Rozšíření virtuálního počítače agenta Log Analytics pro Windows/Linux | Agent se automaticky upgraduje ve výchozím nastavení, pokud jste nakonfigurovali šablonu Azure Resource Manager uoptovat nastavením vlastnosti *autoUpgradeMinorVersion* na **false**. |
| Image vlastních virtuálních virtuálních počítačů Azure | Ruční instalace agenta Log Analytics pro Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku s instalačním balíčkem systému Windows nebo samorozbalovacím a instalovatelným balíčkem skriptů prostředí linux.|
| Virtuální počítače, které nejsou azure, | Ruční instalace agenta Log Analytics pro Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku s instalačním balíčkem systému Windows nebo samorozbalovacím a instalovatelným balíčkem skriptů prostředí linux. |

### <a name="upgrade-windows-agent"></a>Upgrade agenta systému Windows 

Chcete-li aktualizovat agenta na virtuálním počítači se systémem Windows na nejnovější verzi, která není nainstalována pomocí rozšíření virtuálního zařízení Analýzy\>protokolů, spusťte buď z příkazového řádku, skriptu nebo jiného řešení automatizace, nebo pomocí Průvodce instalací platformy MMASetup.\<  

Nejnovější verzi agenta systému Windows můžete stáhnout z pracovního prostoru Analýzy protokolů pomocí následujících kroků.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.

3. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor.

4. V pracovním prostoru Log Analytics vyberte **Upřesnit nastavení**, pak vyberte **Připojené zdroje**a nakonec **servery Windows**.

5. Na stránce **Windows Servers** vyberte příslušnou verzi aplikace Download **Windows Agent,** kterou chcete stáhnout v závislosti na architektuře procesoru operačního systému Windows.

>[!NOTE]
>Během upgradu agenta Log Analytics pro Systém Windows nepodporuje konfiguraci nebo změnu konfigurace pracovního prostoru, do které chcete podávat zprávy. Chcete-li nakonfigurovat agenta, je třeba postupovat podle jedné z podporovaných metod uvedených v části [Přidání nebo odebrání pracovního prostoru](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Inovace pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte program **MMASetup-\<platforma\>.exe** a spusťte Průvodce instalací.

3. Na první stránce Průvodce instalací klepněte na tlačítko **Další**.

4. V dialogovém okně **Nastavení agenta monitorování společnosti Microsoft** klepněte na tlačítko Souhlasím **s** přijetím licenční smlouvy.

5. V dialogovém okně **Instalace agenta Microsoft Monitoring Agent** klikněte na tlačítko **Upgradovat**. Na stavové stránce se zobrazuje průběh upgradu.

6. Po **úspěšném dokončení konfigurace agenta monitorování společnosti Microsoft.** se zobrazí, klepněte na tlačítko **Dokončit**.

#### <a name="to-upgrade-from-the-command-line"></a>Upgrade z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Chcete-li extrahovat instalační soubory agenta, z příkazového řádku se zvýšenými oprávněními se `MMASetup-<platform>.exe /c` zobrazí výzva k získání cesty k extrahování souborů. Alternativně můžete určit cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Spusťte níže uvedený příkaz, přičemž D:\ označuje umístění souboru protokolu upgradu.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Upgrade agenta Linuxu 

Upgrade z předchozích verzí (>1.0.0-47) je podporován. Provedením instalace pomocí `--upgrade` příkazu inovujete všechny součásti agenta na nejnovější verzi.

Chcete-li agenta inovovat, spusťte následující příkaz.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Přidání nebo odebrání pracovního prostoru

### <a name="windows-agent"></a>Agent Windows
Kroky v této části jsou nezbytné, pokud chcete nejen překonfigurovat agenta systému Windows tak, aby se hlásil do jiného pracovního prostoru nebo odebral pracovní prostor z jeho konfigurace, ale také pokud chcete nakonfigurovat agenta tak, aby sehlásil více než jeden pracovní prostor (obvykle označované jako víceúrovňové). Konfigurace agenta systému Windows pro podsvězování do více pracovních prostorů lze provést pouze po počátečním nastavení agenta a pomocí níže popsaných metod.    

#### <a name="update-settings-from-control-panel"></a>Aktualizace nastavení z Ovládacích panelů

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Vyberte **Microsoft Monitoring Agent** a klikněte na kartu Azure Log **Analytics.**

4. Pokud pracovní prostor odeberete, vyberte ho a klepněte na **tlačítko Odebrat**. Tento krok opakujte pro jakýkoli jiný pracovní prostor, do kterého má agent vykazování přestat.

5. Pokud přidáte pracovní prostor, klikněte na **Přidat** a v dialogovém okně **Přidat pracovní prostor Analýzy protokolů** vložte ID pracovního prostoru a klíč pracovního prostoru (primární klíč). Pokud se má počítač hlásit do pracovního prostoru služby Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu Cloud Azure možnost Azure US Government.

6. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="remove-a-workspace-using-powershell"></a>Odebrání pracovního prostoru pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Přidání pracovního prostoru v komerčním Azure pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Přidání pracovního prostoru v Azure pro vládu USA pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Pokud jste dříve k instalaci nebo konfiguraci agenta `EnableAzureOperationalInsights` použili příkazový řádek nebo skript, byl nahrazen příkazem `AddCloudWorkspace` a `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Linuxagent
Následující kroky ukazují, jak překonfigurovat agenta Linuxu, pokud se rozhodnete zaregistrovat s jiným pracovním prostorem nebo odebrat pracovní prostor z jeho konfigurace.

1. Chcete-li ověřit, zda je zaregistrován v pracovním prostoru, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Měl by vrátit stav podobný následujícímu příkladu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Je důležité, aby stav také ukazuje, že je agent spuštěn, jinak následující kroky pro změnu konfigurace agenta nebudou úspěšně dokončeny.

2. Pokud je již registrována v pracovním prostoru, odeberte registrovaný pracovní prostor spuštěním následujícího příkazu. V opačném případě, pokud není registrován, přejděte k dalšímu kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Chcete-li se zaregistrovat v jiném pracovním prostoru, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Chcete-li ověřit, zda se změny projevily, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Měl by vrátit stav podobný následujícímu příkladu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Služba agenta není nutné restartovat, aby se změny projevily.

## <a name="update-proxy-settings"></a>Aktualizovat nastavení proxy serveru
Chcete-li nakonfigurovat agenta pro komunikaci se službou prostřednictvím proxy serveru nebo [brány Log Analytics](gateway.md) po nasazení, použijte k dokončení tohoto úkolu jednu z následujících metod.

### <a name="windows-agent"></a>Agent Windows

#### <a name="update-settings-using-control-panel"></a>Aktualizace nastavení pomocí Ovládacích panelů

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Vyberte **Microsoft Monitoring Agent** a potom klikněte na kartu Nastavení proxy **serveru.**

4. Klikněte na **Použít proxy server** a zadejte adresu URL a číslo portu proxy serveru nebo brány. Pokud váš proxy server nebo brána Log Analytics vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření a pak klikněte na **OK**.

#### <a name="update-settings-using-powershell"></a>Aktualizace nastavení pomocí PowerShellu

Zkopírujte následující ukázkový kód prostředí PowerShell, aktualizujte ho informacemi specifickými pro vaše prostředí a uložte ho s příponou PS1. Spusťte skript v každém počítači, který se připojuje přímo k pracovnímu prostoru Log Analytics v Azure Monitoru.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
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

### <a name="linux-agent"></a>Linuxagent
Pokud vaše počítače s Linuxem potřebují komunikovat prostřednictvím proxy serveru nebo brány Log Analytics, proveďte následující kroky. Hodnota konfigurace proxy má následující syntaxi: `[protocol://][user:password@]proxyhost[:port]`. Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru.

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

## <a name="uninstall-agent"></a>Odinstalovat agenta
Pomocí jednoho z následujících postupů odinstalujte agenta systému Windows nebo Linux pomocí příkazového řádku nebo průvodce instalací.

### <a name="windows-agent"></a>Agent Windows

#### <a name="uninstall-from-control-panel"></a>Odinstalace z Ovládacích panelů
1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. V **Ovládacích panelech**klepněte na **položku Programy a funkce**.

3. V **části Programy a funkce**klepněte na položku Microsoft Monitoring **Agent**, klepněte na tlačítko **Odinstalovat**a potom klepněte na tlačítko **Ano**.

>[!NOTE]
>Průvodce instalací agenta lze spustit také poklepáním na **položku MMASetup- platformy\<\>.exe**, která je k dispozici ke stažení z pracovního prostoru na webu Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstalace z příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček vytvořený pomocí iExpress. Instalační program pro agenta a podpůrné soubory jsou obsaženy v balíčku a je třeba extrahovat, aby bylo možné správně odinstalovat pomocí příkazového řádku uvedeného v následujícím příkladu.

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Chcete-li extrahovat instalační soubory agenta, z příkazového řádku se zvýšenými oprávněními se `extract MMASetup-<platform>.exe` zobrazí výzva k získání cesty k extrahování souborů. Alternativně můžete určit cestu předáním argumentů `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Další informace o přepínačích příkazového řádku podporovaných službou IExpress naleznete [v tématu Přepínače příkazového řádku pro iExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) a následné aktualizaci příkladu podle vašich potřeb.

3. Na výzvu `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`zadejte .

### <a name="linux-agent"></a>Linuxagent
Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz. Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurace agenta pro hlášení skupině pro správu nástroje Operations Manager

### <a name="windows-agent"></a>Agent Windows
Pomocí následujících kroků nakonfigurujte agenta Analýzy protokolů pro systém Windows tak, aby je podřídil skupině pro správu nástroje System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Klikněte na **Microsoft Monitoring Agent** a potom klikněte na kartu Operations **Manager.**

4. Pokud jsou servery nástroje Operations Manager integrovány se službou Active Directory, klepněte na tlačítko **Automaticky aktualizovat přiřazení skupin pro správu ze služby AD DS**.

5. Kliknutím na **Přidat** otevřete dialogové okno **Přidat skupinu pro správu.**

6. Do pole **Název skupiny pro správu** zadejte název skupiny pro správu.

7. Do pole **Primární server pro správu** zadejte název počítače primárního serveru pro správu.

8. Do pole **Port serveru pro správu** zadejte číslo portu TCP.

9. V části **Účet akce agenta**zvolte účet místního systému nebo účet místní domény.

10. Klepnutím na **tlačítko OK** zavřete dialogové okno **Přidat skupinu pro správu** a klepnutím na **tlačítko OK** zavřete dialogové okno **Vlastnosti agenta sledování společnosti Microsoft.**

### <a name="linux-agent"></a>Linuxagent
Proveďte následující kroky a nakonfigurujte agenta Analýzy protokolů pro Linux tak, aby se hlásil skupině pro správu nástroje System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Úprava souboru`/etc/opt/omi/conf/omiserver.conf`

2. Ujistěte se, `httpsport=` že čára začínající definuje port 1270. Například:`httpsport=1270`

3. Restartujte server OMI:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [poradce při potížích s agentem Linuxu,](agent-linux-troubleshoot.md) pokud narazíte na problémy při instalaci nebo správě agenta Linuxu.

- Pokud při instalaci nebo správě agenta systému Windows narazíte na problémy, [zkontrolujte poradce při potížích s agentem windows.](agent-windows-troubleshoot.md)
