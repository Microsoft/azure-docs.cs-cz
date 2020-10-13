---
title: Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)
description: Přečtěte si o instalaci a konfiguraci rozšíření Windows Diagnostics. Seznamte se také s popisem způsobu, jakým jsou data uložená v a Azure Storage účtu.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e6ccba27fb599cb26da86e94d3500f4f806ecb76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328866"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)
[Rozšíření Azure Diagnostics](diagnostics-extension-overview.md) je agent v Azure monitor, který shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů Azure a dalších výpočetních prostředků. Tento článek poskytuje podrobné informace o instalaci a konfiguraci rozšíření diagnostiky systému Windows a popis způsobu, jakým jsou data uložena v a Azure Storage účtu.

Diagnostické rozšíření se implementuje jako [rozšíření virtuálního počítače](../../virtual-machines/extensions/overview.md) v Azure, takže podporuje stejné možnosti instalace, které využívají správce prostředků šablon, PowerShellu a rozhraní příkazového řádku. Podrobnosti o instalaci a údržbě rozšíření virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů pro systém Windows](../../virtual-machines/extensions/features-windows.md) .

## <a name="overview"></a>Přehled
Když konfigurujete rozšíření diagnostiky Windows Azure, musíte zadat účet úložiště, do kterého se budou posílat všechna zadaná data. Volitelně můžete přidat jednu nebo více *datových jímka* pro odeslání dat do různých umístění.

- Azure Monitor jímka – odeslání údajů o výkonu hostů do Azure Monitor metriky.
- Jímka centra událostí – odesílá data o výkonu a protokolu hostů do centra událostí Azure, aby bylo možné přesměrovat mimo Azure. Tato jímka se nedá nakonfigurovat v Azure Portal.


## <a name="install-with-azure-portal"></a>Instalace pomocí Azure Portal
Diagnostické rozšíření můžete nainstalovat a nakonfigurovat na samostatném virtuálním počítači ve Azure Portal, který poskytuje rozhraní na rozdíl od práce přímo s konfigurací. Pokud povolíte rozšíření pro diagnostiku, bude automaticky používat výchozí konfiguraci s nejběžnějšími čítači výkonu a událostmi. Tuto výchozí konfiguraci můžete upravit podle svých specifických požadavků.

> [!NOTE]
> Níže je uveden popis nejběžnějšího nastavení pro diagnostické rozšíření. Podrobnosti o všech možnostech konfigurace najdete v tématu [schéma rozšíření pro diagnostiku systému Windows](diagnostics-extension-schema-windows.md).

1. Otevřete nabídku pro virtuální počítač v Azure Portal.

2. V nabídce virtuální počítač klikněte na **nastavení diagnostiky** v části **monitorování** .

3. Klikněte na **Povolit monitorování na úrovni hosta** , pokud už rozšíření diagnostiky ještě není povolené.

   ![Povolení monitorování](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Vytvoří se nový účet Azure Storage pro virtuální počítač s názvem, který bude založený na názvu skupiny prostředků pro virtuální počítač, a vybere se výchozí sada čítačů výkonu a protokolů výkonu hosta.

   ![Nastavení diagnostiky](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Na kartě **čítače výkonu** vyberte metriky hostů, které chcete shromáždit z tohoto virtuálního počítače. Použijte **vlastní** nastavení pro pokročilejší výběr.

   ![Čítače výkonu](media/diagnostics-extension-windows-install/performance-counters.png)

6. Na kartě **protokoly** vyberte protokoly, které se mají shromažďovat z virtuálního počítače. Protokoly je možné odesílat do úložiště nebo do Center událostí, ale ne do Azure Monitor. Pomocí [agenta Log Analytics](log-analytics-agent.md) můžete shromažďovat protokoly hostů Azure monitor.

   ![Snímek obrazovky zobrazuje kartu protokoly s různými protokoly vybranými pro virtuální počítač.](media/diagnostics-extension-windows-install/logs.png)

7. Na kartě **výpisy stavu systému** určete všechny procesy, které budou shromažďovat výpisy paměti po havárii. Data budou zapsána do účtu úložiště pro nastavení diagnostiky a volitelně můžete zadat kontejner objektů BLOB.

   ![Výpisy stavu systému](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Na kartě **jímky** určete, jestli se mají posílat data do jiných umístění než do Azure Storage. Pokud vyberete **Azure monitor**, budou se odesílat údaje o výkonu hostů do Azure monitor metrik. Jímka centra událostí se nedá nakonfigurovat pomocí Azure Portal.

   ![Snímek obrazovky zobrazuje kartu jímky s povolenou možností odeslat diagnostická data do Azure Monitor.](media/diagnostics-extension-windows-install/sinks.png)
   
   Pokud jste nepovolili identitu přiřazenou systémem, která je nakonfigurovaná pro váš virtuální počítač, může se při ukládání konfigurace s Azure Monitorou jímkou zobrazit následující upozornění. Kliknutím na banner povolte identitu přiřazenou systému.
   
   ![Spravovaná entita](media/diagnostics-extension-windows-install/managed-entity.png)

9. V **agentovi**můžete změnit účet úložiště, nastavit diskovou kvótu a určit, jestli se mají shromažďovat protokoly diagnostické infrastruktury.  

   ![Snímek obrazovky s možností nastavení účtu úložiště zobrazuje kartu Agent.](media/diagnostics-extension-windows-install/agent.png)

10. Kliknutím na **Uložit** uložte konfiguraci. 

> [!NOTE]
> I když může být konfigurace pro diagnostické rozšíření naformátovaná ve formátu JSON nebo XML, všechny konfigurace provedené v Azure Portal se vždycky uloží jako JSON. Pokud používáte XML s jinou konfigurační metodou a pak změníte konfiguraci pomocí Azure Portal, nastavení se změní na JSON.

## <a name="resource-manager-template"></a>Šablona Resource Manageru
V tématu [použití monitorování a diagnostiky pomocí virtuálního počítače s Windows a Azure Resource Manager šablon](../../virtual-machines/extensions/diagnostics-template.md) při nasazení rozšíření diagnostiky pomocí šablon Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI
Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření Azure Diagnostics do existujícího virtuálního počítače pomocí příkazů [AZ VM Extension set](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) , jak je uvedeno v následujícím příkladu. 

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



## <a name="powershell-deployment"></a>Nasazení PowerShellu
Prostředí PowerShell lze použít k nasazení rozšíření Azure Diagnostics do existujícího virtuálního počítače pomocí rutiny [set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) , jak je uvedeno v následujícím příkladu. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Soukromé nastavení je definováno v [elementu PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), zatímco veřejné nastavení je definováno ve [veřejném elementu](diagnostics-extension-schema-windows.md#publicconfig-element) schématu konfigurace. Můžete se také rozhodnout, že chcete zadat podrobnosti účtu úložiště jako parametry rutiny Set-AzVMDiagnosticsExtension, a nemusíte je zahrnout do privátního nastavení.

Následuje minimální příklad konfiguračního souboru, který umožňuje shromažďování protokolů diagnostické infrastruktury, jeden čítač výkonu a jeden protokol událostí. Kompletní informace o privátních a veřejných nastaveních najdete v části [příklad konfigurace](diagnostics-extension-schema-windows.md#publicconfig-element) . 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
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


| Data | Typ úložiště | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabulka | Diagnostické monitorování a změny konfigurace. |
| WADDirectoriesTable | Tabulka | Adresáře, které monitoruje monitorování diagnostiky.  Patří sem protokoly IIS, protokoly neúspěšných požadavků služby IIS a vlastní adresáře.  Umístění souboru protokolu objektu BLOB je zadáno v poli kontejneru a název objektu BLOB je uveden v poli RelativePath.  Pole AbsolutePath označuje umístění a název souboru, který se nachází na virtuálním počítači Azure. |
| WadLogsTable | Tabulka | Protokoly napsané v kódu pomocí naslouchacího procesu trasování. |
| WADPerformanceCountersTable | Tabulka | Čítače výkonu. |
| WADWindowsEventLogsTable | Tabulka | Protokoly událostí systému Windows. |
| WAD – IIS – failedreqlogfiles | Blob | Obsahuje informace z protokolů neúspěšných požadavků služby IIS. |
| WAD – IIS – soubory protokolů | Blob | Obsahuje informace o protokolech služby IIS. |
| Uživatelská | Blob | Vlastní kontejner založený na konfiguraci adresářů monitorovaných monitorováním diagnostiky.  Název tohoto kontejneru objektů BLOB se určí v WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
K dispozici je několik nástrojů, které vám umožní zobrazit data po přenosu do úložiště. Například:

* Průzkumník serveru v aplikaci Visual Studio – Pokud jste nainstalovali nástroje Azure pro Microsoft Visual Studio, můžete použít uzel Azure Storage v Průzkumník serveru k zobrazení dat objektů BLOB jen pro čtení a tabulek z vašich účtů úložiště Azure. Můžete zobrazit data z místního účtu emulátoru úložiště a taky z účtů úložiště, které jste vytvořili pro Azure. Další informace najdete v tématu [procházení a Správa prostředků úložiště pomocí Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Průzkumník služby Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, OSX a Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zahrnuje správce Azure Diagnostics, který umožňuje zobrazit, stáhnout a spravovat diagnostická data shromážděná aplikacemi běžícími na Azure.

## <a name="next-steps"></a>Další kroky
- Podrobnosti o předávání dat monitorování do Azure Event Hubs najdete v tématu věnovaném [odeslání dat z rozšíření Windows Azure Diagnostics na Event Hubs](diagnostics-extension-stream-event-hubs.md) .
