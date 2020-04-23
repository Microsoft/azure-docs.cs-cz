---
title: Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)
description: Naučte se shromažďovat diagnostická data Azure v účtu Azure Storage, abyste je mohli zobrazit pomocí některého z několika dostupných nástrojů.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672255"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)
Rozšíření Azure Diagnostics je agent v Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů Azure a dalších výpočetních prostředků. Tento článek poskytuje podrobné informace o instalaci a konfiguraci rozšíření diagnostiky systému Windows a popis způsobu, jakým jsou data uložena v a Azure Storage účtu.

Diagnostické rozšíření se implementuje jako [rozšíření virtuálního počítače](../../virtual-machines/extensions/overview.md) v Azure, takže podporuje stejné možnosti instalace, které využívají správce prostředků šablon, PowerShellu a rozhraní příkazového řádku. Podrobnosti o instalaci a údržbě rozšíření virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů pro systém Windows](../../virtual-machines/extensions/features-windows.md) .

## <a name="install-with-azure-portal"></a>Instalace pomocí Azure Portal
Diagnostické rozšíření můžete nainstalovat a nakonfigurovat na samostatném virtuálním počítači ve Azure Portal, který poskytuje rozhraní na rozdíl od práce přímo s konfigurací. Pokud povolíte rozšíření pro diagnostiku, bude automaticky používat výchozí konfiguraci s nejběžnějšími čítači výkonu a událostmi. Tuto výchozí konfiguraci můžete upravit podle svých specifických požadavků.

> [!NOTE]
> K dispozici jsou nastavení rozšíření diagnostiky, která nejdou konfigurovat pomocí Azure Portal, včetně odesílání dat do Azure Event Hubs. Pro tato nastavení je nutné použít jednu z dalších metod konfigurace.

1. Otevřete nabídku pro virtuální počítač v Azure Portal.
2. V nabídce virtuální počítač klikněte na **nastavení diagnostiky** v části **monitorování** .
3. Klikněte na **Povolit monitorování na úrovni hosta** , pokud už rozšíření diagnostiky ještě není povolené.
4. Vytvoří se nový účet Azure Storage pro virtuální počítač s názvem, který bude založený na názvu skupiny prostředků pro virtuální počítač. Virtuální počítač můžete připojit k jinému účtu úložiště tak, že vyberete kartu **Agent** .

![Nastavení diagnostiky](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Po povolení diagnostického rozšíření můžete upravit výchozí konfiguraci. Následující tabulka popisuje možnosti, které můžete upravit na různých kartách. Některé možnosti mají **vlastní** příkaz, který vám umožní zadat podrobnější konfiguraci. Podrobnosti o různých nastaveních najdete v tématu [schéma rozšíření diagnostiky Windows](diagnostics-extension-schema-windows.md) .

| Karta | Popis |
|:---|:---|
| Přehled | Zobrazí aktuální konfiguraci s odkazy na jiné karty. |
| Čítače výkonu | Vyberte čítače výkonu, které chcete shromáždit, a vzorkovací frekvenci pro každý z nich.  |
| Protokoly | Vyberte data protokolu, která se mají shromažďovat. To zahrnuje protokoly událostí Windows, protokoly IIS, protokoly aplikací .NET a události ETW.  |
| Výpisy stavu systému | Povolte výpis stavu systému pro různé procesy. |
| Jímky | Povolit jímky dat pro posílání dat do cílových umístění kromě Azure Storage.<br>Azure Monitor – odesílá údaje o výkonu do Azure Monitor metrik.<br>Application Insights – odeslání dat do Application Insights aplikace. |
| Agent | Upravte pro agenta následující konfiguraci:<br>-Změnit účet úložiště.<br>-Zadejte maximální místní disk použitý pro agenta.<br>-Nakonfigurujte protokoly pro stav samotného agenta.|


> [!NOTE]
> I když může být konfigurace pro diagnostické rozšíření naformátovaná ve formátu JSON nebo XML, všechny konfigurace provedené v Azure Portal se vždycky uloží jako JSON. Pokud používáte XML s jinou konfigurační metodou a pak změníte konfiguraci pomocí Azure Portal, nastavení se změní na JSON.

## <a name="resource-manager-template"></a>Šablona Resource Manageru
V tématu [použití monitorování a diagnostiky pomocí virtuálního počítače s Windows a Azure Resource Manager šablon](../../virtual-machines/extensions/diagnostics-template.md) při nasazení rozšíření diagnostiky pomocí šablon Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI
Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření Azure Diagnostics do existujícího virtuálního počítače pomocí příkazů [AZ VM Extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) , jak je uvedeno v následujícím příkladu. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Chráněná nastavení jsou definována v [elementu PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) schématu konfigurace. Následuje minimální příklad souboru s chráněným nastavením, který definuje účet úložiště. Kompletní informace o privátním nastavení najdete v části [příklad konfigurace](diagnostics-extension-schema-windows.md#privateconfig-element) .

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Veřejné nastavení je definováno ve [veřejném elementu](diagnostics-extension-schema-windows.md#publicconfig-element) schématu konfigurace. Následuje minimální příklad souboru veřejného nastavení, který umožňuje shromažďování protokolů diagnostické infrastruktury, jeden čítač výkonu a jeden protokol událostí. Úplné podrobnosti o veřejném nastavení naleznete v tématu [příklad konfigurace](diagnostics-extension-schema-windows.md#publicconfig-element) .

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Nasazení prostředí PowerShell
Prostředí PowerShell lze použít k nasazení rozšíření Azure Diagnostics do existujícího virtuálního počítače pomocí rutiny [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , jak je uvedeno v následujícím příkladu. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Soukromé nastavení je definováno v [elementu PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), zatímco veřejné nastavení je definováno ve [veřejném elementu](diagnostics-extension-schema-windows.md#publicconfig-element) schématu konfigurace. Můžete také zadat podrobnosti účtu úložiště jako parametry rutiny Set-AzVMDiagnosticsExtension a nemusíte je zahrnout do privátního nastavení.

Následuje minimální příklad konfiguračního souboru, který umožňuje shromažďování protokolů diagnostické infrastruktury, jeden čítač výkonu a jeden protokol událostí. Kompletní informace o privátních a veřejných nastaveních najdete v části [příklad konfigurace](diagnostics-extension-schema-windows.md#publicconfig-element) . 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Další informace najdete [v tématu použití PowerShellu k povolení Azure Diagnostics ve virtuálním počítači s Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Úložiště dat
V následující tabulce jsou uvedeny různé typy dat shromážděných z rozšíření pro diagnostiku a zda jsou uloženy jako tabulka nebo objekt BLOB. Data uložená v tabulkách je také možné ukládat do objektů BLOB v závislosti na [Nastavení StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) ve vaší veřejné konfiguraci.


| Data | Typ úložiště | Popis |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Table | Diagnostické monitorování a změny konfigurace. |
| WADDirectoriesTable | Table | Adresáře, které monitoruje monitorování diagnostiky.  Patří sem protokoly IIS, protokoly neúspěšných požadavků služby IIS a vlastní adresáře.  Umístění souboru protokolu objektu BLOB je zadáno v poli kontejneru a název objektu BLOB je uveden v poli RelativePath.  Pole AbsolutePath označuje umístění a název souboru, který se nachází na virtuálním počítači Azure. |
| WadLogsTable | Table | Protokoly napsané v kódu pomocí naslouchacího procesu trasování. |
| WADPerformanceCountersTable | Table | Čítače výkonu. |
| WADWindowsEventLogsTable | Table | Protokoly událostí systému Windows. |
| WAD – IIS – failedreqlogfiles | Objekt blob | Obsahuje informace z protokolů neúspěšných požadavků služby IIS. |
| WAD – IIS – soubory protokolů | Objekt blob | Obsahuje informace o protokolech služby IIS. |
| Uživatelská | Objekt blob | Vlastní kontejner založený na konfiguraci adresářů monitorovaných monitorováním diagnostiky.  Název tohoto kontejneru objektů BLOB se určí v WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
K dispozici je několik nástrojů, které vám umožní zobrazit data po přenosu do úložiště. Příklad:

* Průzkumník serveru v aplikaci Visual Studio – Pokud jste nainstalovali nástroje Azure pro Microsoft Visual Studio, můžete použít uzel Azure Storage v Průzkumník serveru k zobrazení dat objektů BLOB jen pro čtení a tabulek z vašich účtů úložiště Azure. Můžete zobrazit data z místního účtu emulátoru úložiště a taky z účtů úložiště, které jste vytvořili pro Azure. Další informace najdete v tématu [procházení a Správa prostředků úložiště pomocí Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Průzkumník služby Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, OSX a Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zahrnuje správce Azure Diagnostics, který umožňuje zobrazit, stáhnout a spravovat diagnostická data shromážděná aplikacemi běžícími na Azure.

## <a name="next-steps"></a>Další kroky
- Podrobnosti o předávání dat monitorování do Azure Event Hubs najdete v tématu věnovaném [odeslání dat z rozšíření Windows Azure Diagnostics na Event Hubs](diagnostics-extension-stream-event-hubs.md) .
