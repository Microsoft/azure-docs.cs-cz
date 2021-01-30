---
title: Správa agenta Azure Log Analytics
description: Tento článek popisuje různé úlohy správy, které se obvykle provádějí během životního cyklu Log Analytics agenta pro Windows nebo Linux nasazeného v počítači.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 15d01a8ac21264bf57ee7e65147e3989cd631b15
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095552"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Správa a údržba agenta Log Analytics pro Windows a Linux

Po počátečním nasazení agenta Log Analytics se systémem Windows nebo Linux v Azure Monitor může být nutné překonfigurovat agenta, provést upgrade nebo ho odebrat z počítače, pokud dosáhli fáze vyřazení v životním cyklu. Tyto rutinní úlohy údržby můžete snadno spravovat ručně nebo prostřednictvím automatizace, což snižuje provozní chybu i výdaje.

## <a name="upgrading-agent"></a>Upgrade agenta

Agenta Log Analytics pro systém Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na scénáři nasazení a prostředí, ve kterém je virtuální počítač spuštěný. K upgradu agenta lze použít následující metody.

| Prostředí | Metoda instalace | Metoda upgradu |
|--------|----------|-------------|
| Virtuální počítač Azure | Rozšíření virtuálního počítače agenta Log Analytics pro Windows/Linux | Agent je automaticky upgradován ve výchozím nastavení, pokud nenakonfigurovali šablonu Azure Resource Manager tak, aby se odhlásila nastavením vlastnosti *autoUpgradeMinorVersion* na **hodnotu false**. |
| Vlastní image virtuálních počítačů Azure | Ruční instalace agenta Log Analytics pro systém Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku se spuštěným balíčkem Instalační služby systému Windows nebo pomocí automatického extrahování a instalovatelných sad prostředků prostředí pro systém Linux.|
| Virtuální počítače mimo Azure | Ruční instalace agenta Log Analytics pro systém Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku se spuštěným balíčkem Instalační služby systému Windows nebo pomocí automatického extrahování a instalovatelných sad prostředků prostředí pro systém Linux. |

### <a name="upgrade-windows-agent"></a>Upgradovat agenta Windows 

Chcete-li aktualizovat agenta na virtuálním počítači s Windows na nejnovější verzi, která není nainstalovaná pomocí Log Analyticsho rozšíření virtuálního počítače, buď spusťte z příkazového řádku, skriptu nebo jiného řešení automatizace, nebo pomocí \<platform\> Průvodce instalací MMASetup. msi.  

Nejnovější verzi agenta pro Windows si můžete stáhnout z pracovního prostoru Log Analytics, a to provedením následujících kroků.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.

3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor.

4. V pracovním prostoru Log Analytics vyberte **Upřesnit nastavení**, pak vyberte **připojené zdroje** a nakonec **Windows servery**.

5. Na stránce **Windows servery** vyberte příslušnou verzi agenta pro **Windows** ke stažení, která se má stáhnout v závislosti na architektuře procesoru operačního systému Windows.

>[!NOTE]
>Během upgradu agenta Log Analytics pro systém Windows nepodporuje konfiguraci ani změnu konfigurace pracovního prostoru pro vytvoření sestavy. Chcete-li nakonfigurovat agenta, je třeba provést jednu z podporovaných metod uvedených v části [Přidání nebo odebrání pracovního prostoru](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Upgrade pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **MMASetup. \<platform\> exe** a spusťte Průvodce instalací nástroje.

3. Na první stránce Průvodce instalací klikněte na tlačítko **Další**.

4. V dialogovém okně **instalace Microsoft Monitoring Agent** **kliknutím na Souhlasím** potvrďte souhlas s licenční smlouvou.

5. V dialogovém okně **Instalace agenta Microsoft Monitoring Agent** klikněte na tlačítko **Upgradovat**. Na stavové stránce se zobrazuje průběh upgradu.

6. Po **úspěšném dokončení konfigurace Microsoft Monitoring Agent.** zobrazí se stránka klikněte na **Dokončit**.

#### <a name="to-upgrade-from-the-command-line"></a>Upgrade z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. K extrakci instalačních souborů agenta se spustí příkazový řádek se zvýšenými oprávněními `MMASetup-<platform>.exe /c` a zobrazí výzvu k zadání cesty k extrakci souborů. Alternativně můžete zadat cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>` .

3. Spusťte níže uvedený příkaz, přičemž D:\ označuje umístění souboru protokolu upgradu.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Upgrade agenta pro Linux 

Upgrade z předchozích verzí (>1.0.0-47) je podporován. Při provádění instalace pomocí `--upgrade` příkazu se upgradují všechny součásti agenta na nejnovější verzi.

Spusťte následující příkaz pro upgrade agenta.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Přidání nebo odebrání pracovního prostoru

### <a name="windows-agent"></a>Agent Windows
Kroky v této části jsou nezbytné v případě, že nechcete překonfigurovat agenta pro systém Windows pouze na jiný pracovní prostor nebo odebrat pracovní prostor z jeho konfigurace, ale také v případě, že chcete agenta nakonfigurovat tak, aby nahlásil do více než jednoho pracovního prostoru (obvykle označovaného jako vícenásobné). Konfigurace agenta pro Windows tak, aby nahlásila do více pracovních prostorů, se dá provést jenom po počátečním nastavení agenta a pomocí metod popsaných níže.    

#### <a name="update-settings-from-control-panel"></a>Aktualizovat nastavení z ovládacích panelů

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Vyberte **Microsoft Monitoring Agent** a pak klikněte na kartu **Azure Log Analytics** .

4. Pokud jste pracovní prostor odebrali, vyberte ho a pak klikněte na **Odebrat**. Tento krok opakujte pro všechny ostatní pracovní prostory, na které chcete, aby agent zastavil generování sestav.

5. Pokud přidáváte pracovní prostor, klikněte na **Přidat** a v dialogovém okně **Přidat Log Analytics pracovní prostor** Vložte ID pracovního prostoru a klíč pracovního prostoru (primární klíč). Pokud se má počítač hlásit do pracovního prostoru služby Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu Cloud Azure možnost Azure US Government.

6. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="remove-a-workspace-using-powershell"></a>Odebrání pracovního prostoru pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Přidání pracovního prostoru do komerčního prostředí Azure pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Přidání pracovního prostoru v Azure pro státní správu USA pomocí PowerShellu

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Pokud jste dříve používali příkazový řádek nebo skript pro instalaci nebo konfiguraci agenta, `EnableAzureOperationalInsights` byl nahrazen nástrojem `AddCloudWorkspace` a `RemoveCloudWorkspace` .
>

### <a name="linux-agent"></a>Agent pro Linux
Následující kroky ukazují, jak znovu nakonfigurovat agenta pro Linux, pokud se rozhodnete ho zaregistrovat s jiným pracovním prostorem nebo odebrat pracovní prostor z jeho konfigurace.

1. Pokud chcete ověřit, jestli je zaregistrované v pracovním prostoru, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Měl by vrátit stav podobný následujícímu příkladu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Je důležité, aby se ve stavu zobrazil také agent spuštěný, jinak následující kroky pro překonfigurování agenta nebudou úspěšně dokončeny.

2. Pokud je v pracovním prostoru již zaregistrován, odeberte registrovaný pracovní prostor spuštěním následujícího příkazu. Jinak, pokud není zaregistrováno, přejděte k dalšímu kroku.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Pokud se chcete zaregistrovat v jiném pracovním prostoru, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Pokud chcete ověřit, jaké změny se projeví, spusťte následující příkaz:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Měl by vrátit stav podobný následujícímu příkladu:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Aby se změny projevily, není nutné restartovat službu agenta.

## <a name="update-proxy-settings"></a>Aktualizovat nastavení proxy serveru
Chcete-li nakonfigurovat, aby agent komunikoval se službou prostřednictvím proxy server nebo [Log Analytics brány](gateway.md) po nasazení, použijte k dokončení této úlohy jednu z následujících metod.

### <a name="windows-agent"></a>Agent Windows

#### <a name="update-settings-using-control-panel"></a>Aktualizace nastavení pomocí ovládacích panelů

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Vyberte **Microsoft Monitoring Agent** a pak klikněte na kartu **nastavení proxy serveru** .

4. Klikněte na **Použít proxy server** a zadejte adresu URL a číslo portu proxy serveru nebo brány. Pokud váš proxy server nebo brána Log Analytics vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření a pak klikněte na **OK**.

#### <a name="update-settings-using-powershell"></a>Aktualizace nastavení pomocí PowerShellu

Zkopírujte následující ukázkový kód PowerShellu, aktualizujte ho o konkrétní informace pro vaše prostředí a uložte ho s příponou názvu souboru PS1. Spusťte skript na každém počítači, který se připojuje přímo k pracovnímu prostoru Log Analytics v Azure Monitor.

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

### <a name="linux-agent"></a>Agent pro Linux
Pokud počítače se systémem Linux potřebují komunikovat prostřednictvím proxy server nebo Log Analytics bránu, proveďte následující kroky. Hodnota konfigurace proxy má následující syntaxi: `[protocol://][user:password@]proxyhost[:port]`. Vlastnost *proxyhost* přijímá plně kvalifikovaný název domény nebo IP adresu proxy serveru.

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
Pomocí jednoho z následujících postupů odinstalujte agenta systému Windows nebo Linux pomocí příkazového řádku nebo Průvodce instalací nástroje.

### <a name="windows-agent"></a>Agent Windows

#### <a name="uninstall-from-control-panel"></a>Odinstalace z ovládacích panelů
1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. V **Ovládacích panelech** klikněte na **programy a funkce**.

3. V nabídce **programy a funkce** klikněte na **Microsoft Monitoring Agent**, klikněte na **odinstalovat** a potom klikněte na **Ano**.

>[!NOTE]
>Průvodce instalací agenta lze spustit také dvojitým kliknutím na **MMASetup \<platform\> . exe**, který je k dispozici ke stažení z pracovního prostoru v Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Odinstalace z příkazového řádku
Stažený soubor pro agenta je samostatný instalační balíček vytvořený pomocí IExpress. Instalační program pro agenta a podpůrné soubory jsou obsaženy v balíčku a je třeba jej extrahovat, aby se správně odinstaloval pomocí příkazového řádku, který je znázorněn v následujícím příkladu.

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. K extrakci instalačních souborů agenta se spustí příkazový řádek se zvýšenými oprávněními `extract MMASetup-<platform>.exe` a zobrazí výzvu k zadání cesty k extrakci souborů. Alternativně můžete zadat cestu předáním argumentů `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` . Další informace o přepínačích příkazového řádku podporovaných v IExpress najdete v tématech [přepínače příkazového řádku pro IExpress](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) a pak aktualizujte příklad tak, aby odpovídal vašim potřebám.

3. Na příkazovém řádku zadejte `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` .

### <a name="linux-agent"></a>Agent pro Linux
Pokud chcete agenta odebrat, spusťte v počítači s Linuxem následující příkaz. Argument *--purge* úplně odebere agenta a jeho konfiguraci.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Nakonfigurovat agenta tak, aby se nahlásil na Operations Manager skupinu pro správu

### <a name="windows-agent"></a>Agent Windows
Provedením následujících kroků nakonfigurujte agenta Log Analytics pro systém Windows, aby vyhlásil zprávu do skupiny pro správu System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Otevřete **Ovládací panely**.

3. Klikněte na **Microsoft Monitoring Agent** a pak klikněte na kartu **Operations Manager** .

4. Pokud vaše Operations Manager servery mají integraci se službou Active Directory, klikněte na **automaticky aktualizovat přiřazení skupin pro správu z služba AD DS**.

5. Kliknutím na tlačítko **Přidat** otevřete dialogové okno **Přidat skupinu pro správu** .

6. Do pole **název skupiny pro správu** zadejte název vaší skupiny pro správu.

7. Do pole **primární Management Server** zadejte název počítače primárního Management Server.

8. Do pole **port serveru pro správu** zadejte číslo portu TCP.

9. V části **účet akce agenta** vyberte účet místní systém nebo místní doménový účet.

10. Kliknutím na tlačítko **OK** zavřete dialogové okno **Přidat skupinu pro správu** a potom kliknutím na tlačítko **OK** zavřete dialogové okno **Microsoft Monitoring Agent vlastnosti** .

### <a name="linux-agent"></a>Agent pro Linux
Provedením následujících kroků nakonfigurujte agenta Log Analytics pro systém Linux, který bude hlásit do skupiny pro správu System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Upravit soubor `/etc/opt/omi/conf/omiserver.conf`

2. Zajistěte, aby řádek začínající řetězcem `httpsport=` definoval port 1270. Například: `httpsport=1270`

3. Restartujte server OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Další kroky

- Pokud narazíte na problémy při instalaci nebo správě agenta Linux, přečtěte si téma [řešení potíží s agentem pro Linux](agent-linux-troubleshoot.md) .

- Pokud narazíte na problémy při instalaci nebo správě agenta Windows, přečtěte si téma [řešení potíží s agentem Windows](agent-windows-troubleshoot.md) .
