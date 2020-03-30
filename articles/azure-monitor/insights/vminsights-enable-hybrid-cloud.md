---
title: Povolení Azure Monitoru pro hybridní prostředí | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro hybridní cloudové prostředí, které obsahuje jeden nebo více virtuálních počítačů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480738"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Povolení Azure Monitoru pro virtuální počítače pro hybridní prostředí

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítače nebo fyzické počítače hostované ve vašem datovém centru nebo jiném cloudovém prostředí. Na konci tohoto procesu budete úspěšně začali sledovat vaše virtuální počítače ve vašem prostředí a zjistit, zda dochází k problémům s výkonem nebo dostupností.

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ověřte, zda vaše předplatné a prostředky splňují požadavky. Zkontrolujte požadavky a metody nasazení pro [agenta Log Analytics Linux a Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor pro agenta závislostí map virtuálních počítače nepřenáší žádná data sama a nevyžaduje žádné změny brány firewall nebo porty. Mapová data se vždy přenášejí agentem Analýzy protokolů do služby Azure Monitor, a to buď přímo, nebo prostřednictvím [brány Operations Management Suite,](../../azure-monitor/platform/gateway.md) pokud zásady zabezpečení IT neumožňují počítačům v síti připojení k internetu.

Kroky k dokončení tohoto úkolu jsou shrnuty takto:

1. Nainstalujte agenta Log Analytics pro Windows nebo Linux. Před instalací agenta, zkontrolujte [protokol Analytics agent přehled](../platform/log-analytics-agent.md) článku pochopit systémové požadavky a metody nasazení.

2. Stáhněte a nainstalujte agenta závislostí na mapě Azure Monitor pro virtuální počítače pro [Windows](https://aka.ms/dependencyagentwindows) nebo [Linux](https://aka.ms/dependencyagentlinux).

3. Povolte shromažďování čítačů výkonu.

4. Nasazení Azure Monitor u virtuálních počítačů.

>[!NOTE]
>Informace popsané v tomto článku pro nasazení agenta závislostí je také použitelný pro [řešení mapy služeb](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí v systému Windows

Agenta závislostí můžete nainstalovat ručně do `InstallDependencyAgent-Windows.exe`počítačů se systémem Windows spuštěním aplikace . Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí se průvodce instalací, který můžete sledovat při interaktivní instalaci agenta.

>[!NOTE]
>K instalaci nebo odinstalaci agenta jsou vyžadována oprávnění *správce.*

V následující tabulce jsou zvýrazněny parametry, které jsou podporovány nastavením agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /s | Provádí tichou instalaci bez interakce uživatele. |

Chcete-li například spustit instalační `/?` program s parametrem, zadejte **installdependencyagent-windows.exe /?**.

Soubory agenta závislostí systému Windows jsou ve výchozím nastavení nainstalovány v *aplikaci C:\Program Files\Microsoft Dependency Agent.* Pokud se agentovi závislostí nepodaří spustit po dokončení instalace, zkontrolujte v protokolech podrobné informace o chybě. Adresář protokolu je *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí na Linuxu

Agent závislostí je nainstalován na serverech Linux u *InstallDependencyAgent-Linux64.bin*, což je shellový skript se samorozbalovacím binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidat oprávnění ke spuštění samotného souboru.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.
>

| Parametr | Popis |
|:--|:--|
| -pomoc | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| --kontrola | Zkontrolujte oprávnění a operační systém, ale neinstalujte agenta. |

Chcete-li například spustit instalační `-help` program s parametrem, zadejte **InstallDependencyAgent-Linux64.bin -help**.

Spuštěním příkazu `sh InstallDependencyAgent-Linux64.bin`nainstalujte agenta závislostí Linuxu jako root .

Pokud se agentovi závislostí nepodaří spustit, zkontrolujte v protokolech podrobné informace o chybě. U agentů Linuxu je adresář protokolu */var/opt/microsoft/dependency-agent/log*.

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Příklady instalačního skriptu

Chcete-li snadno nasadit agenta závislostí na mnoha serverech najednou, je k dispozici následující příklad skriptu ke stažení a instalaci agenta závislostí na systému Windows nebo Linux.

### <a name="powershell-script-for-windows"></a>Skript PowerShellu pro Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skript prostředí pro Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Konfigurace požadovaného stavu

Chcete-li nasadit agenta závislostí pomocí konfigurace požadovaného stavu (DSC), můžete použít modul xPSDesiredStateConfiguration s následujícím ukázkovým kódem:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Povolení čítačů výkonu

Pokud pracovní prostor Log Analytics, na který odkazuje řešení, ještě není nakonfigurován tak, aby shromažďoval čítače výkonu vyžadované řešením, musíte je povolit. Můžete tak učinit jedním ze dvou způsobů:
* Ručně, jak je popsáno ve [zdrojích dat o výkonu Windows a Linuxu v Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a spuštěním skriptu PowerShellu, který je dostupný v [Galerii Azure PowerShellu](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Nasazení Azure Monitoru pro virtuální počítače

Tato metoda zahrnuje šablonu JSON, která určuje konfiguraci pro povolení součástí řešení v pracovním prostoru Log Analytics.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Chcete-li použít příkazové příkazové příkazové příkazy Azure, musíte nejprve nainstalovat a použít příkazový příkaz cli místně. Musíte spouštět Azure CLI verze 2.0.27 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud chcete nainstalovat nebo upgradovat azure cli, najdete [v tématu instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a spuštění šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte tento soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Zachyťte hodnoty pro *název_pracovního_ ka*, *Název_prostředku_* *WorkspaceLocation* Hodnota pro *název pracovního_ kva to* je název pracovního prostoru Analýzy protokolů. Hodnota pro *WorkspaceLocation* je oblast, ve které je pracovní prostor definován.

1. Tuto šablonu můžete nasadit pomocí následujícího příkazu PowerShellu:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu a obsahuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po povolení monitorování může trvat přibližně 10 minut, než budete moci zobrazit stav a metriky pro hybridní počítač.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="vm-doesnt-appear-on-the-map"></a>Virtuální počítač se na mapě nezobrazuje

Pokud byla instalace agenta závislostí úspěšná, ale počítač na mapě nevidíte, diagnostikujte problém pomocí následujících kroků.

1. Je agent závislostí úspěšně nainstalován? Můžete ověřit kontrolou, zda je služba nainstalována a spuštěna.

    **Windows**: Vyhledejte službu s názvem "Microsoft Dependency Agent."

    **Linux**: Vyhledejte spuštěný proces "microsoft-dependency-agent."

2. Jste na [bezplatné cenové úrovni Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Plán Free umožňuje až pět jedinečných počítačů. Žádné další počítače se na mapě nezobrazí, a to ani v případě, že předchozí pětka již neodesílá data.

3. Je počítač odesílání protokolu a perf data do protokolů monitorování Azure? Proveďte pro počítač následující dotaz:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Vrátiljeden nebo více výsledků? Jsou data nedávná? Pokud ano, váš agent Log Analytics pracuje správně a komunikuje se službou. Pokud ne, zkontrolujte agenta na serveru: [Agent Log Analytics pro řešení potíží systému Windows](../platform/agent-windows-troubleshoot.md) nebo Agent Log [Analytics pro řešení potíží s Linuxem](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Počítač se zobrazí na mapě, ale nemá žádné procesy

Pokud se na mapě zobrazí server, ale nemá žádná data procesu nebo připojení, znamená to, že je agent závislostí nainstalován a spuštěn, ale ovladač jádra se nenačetl.

Zkontrolujte soubor C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) nebo /var/opt/microsoft/dependency-agent/log/service.log (Linux). Poslední řádky souboru by měly označovat, proč se jádro nenačetlo. Například jádro nemusí být v Linuxu podporováno, pokud jste aktualizovali jádro.


## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace dostupné pro analýzu pomocí Azure Monitoru pro virtuální počítače.

- Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení programu Azure Monitor for VMS .](vminsights-maps.md)

- Informace o problémových místech a celkovém využití s výkonem virtuálního počítače najdete v [tématu Zobrazení výkonu virtuálních počítačích Azure](vminsights-performance.md).
