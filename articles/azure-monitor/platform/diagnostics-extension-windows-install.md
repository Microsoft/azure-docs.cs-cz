---
title: Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)
description: Zjistěte, jak shromažďovat diagnostická data Azure v účtu Azure Storage, abyste je mohli zobrazit pomocí jednoho z několika dostupných nástrojů.
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
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)
Rozšíření diagnostiky Azure je agent ve službě Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a úlohy virtuálních počítačů Azure a dalších výpočetních prostředků. Tento článek obsahuje podrobnosti o instalaci a konfiguraci rozšíření diagnostiky systému Windows a popis, jak jsou data uložena v účtu Azure Storage.

Rozšíření diagnostiky se implementuje jako [rozšíření virtuálního počítače](../../virtual-machines/extensions/overview.md) v Azure, takže podporuje stejné možnosti instalace pomocí šablon Správce prostředků, PowerShellu a CLI. Podrobnosti o instalaci a údržbě rozšíření virtuálních strojů najdete v tématu [Rozšíření virtuálních strojů a funkce pro Windows.](../../virtual-machines/extensions/features-windows.md)

## <a name="install-with-azure-portal"></a>Instalace s portálem Azure
Rozšíření diagnostiky můžete nainstalovat a nakonfigurovat na jednotlivém virtuálním počítači na webu Azure Portal, který vám poskytuje rozhraní, na rozdíl od přímé práce s konfigurací. Pokud povolíte rozšíření diagnostiky, bude automaticky používat výchozí konfiguraci s nejběžnějšími čítači výkonu a událostmi. Tuto výchozí konfiguraci můžete upravit podle konkrétních požadavků.

> [!NOTE]
> Existují nastavení rozšíření diagnostiky, které nelze nakonfigurovat pomocí portálu Azure, včetně odesílání dat do centra událostí Azure. Pro tato nastavení je nutné použít jednu z dalších metod konfigurace.

1. Otevřete nabídku pro virtuální počítač na webu Azure Portal.
2. Klikněte na **Nastavení diagnostiky** v části **Monitorování** v nabídce Virtuální počítač.
3. Pokud rozšíření diagnostiky ještě nebylo povoleno, klikněte **na Povolit monitorování na úrovni** hosta.
4. Nový účet Azure Storage se vytvoří pro virtuální počítač s názvem bude založen na názvu skupiny prostředků pro virtuální počítač. Virtuální ho můžete připojit k jinému účtu úložiště výběrem karty **Agent.**

![Nastavení diagnostiky](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Po povolení rozšíření diagnostiky můžete změnit výchozí konfiguraci. Následující tabulka popisuje možnosti, které můžete upravit na různých kartách. Některé možnosti mají **vlastní** příkaz, který umožňuje zadat podrobnější konfiguraci; Podrobnosti o různých nastaveních naleznete [v tématu Schéma rozšíření diagnostiky systému Windows.](diagnostics-extension-schema-windows.md)

| Karta | Popis |
|:---|:---|
| Přehled | Zobrazí aktuální konfiguraci s odkazy na ostatní karty. |
| Čítače výkonu | Vyberte čítače výkonu, které chcete shromáždit, a vzorkovací frekvenci pro každou z nich.  |
| Protokoly | Vyberte data protokolu, která chcete shromažďovat. To zahrnuje protokoly událostí systému Windows, protokoly služby IIS, protokoly aplikací .NET a události ETW.  |
| Výpisy stavu systému | Povolte výpis stavu stavu stavu pro různé procesy. |
| Propadů | Povolte jímky dat k odesílání dat do cílů kromě Azure Storage.<br>Azure Monitor – odesílá data o výkonu do metrik azure monitoru.<br>Application Insights – odesílání dat do aplikace Application Insights. |
| Agent | Upravte následující konfiguraci agenta:<br>- Změňte účet úložiště.<br>- Zadejte maximální místní disk použitý pro agenta.<br>- Konfigurace protokoly pro stav samotného agenta.|


> [!NOTE]
> Zatímco konfigurace pro rozšíření diagnostiky lze formátovat v JSON nebo XML, jakákoli konfigurace provedená na webu Azure Portal bude vždy uložena jako JSON. Pokud používáte XML s jinou metodou konfigurace a pak změnit konfiguraci s portálem Azure, nastavení se změní na JSON.

## <a name="resource-manager-template"></a>Šablona Resource Manageru
Na [jdete natémati Použití monitorování a diagnostiky se šablonami virtuálních aplikací pro Windows a Azure Resource Manageru](../../virtual-machines/extensions/diagnostics-template.md) při nasazování rozšíření diagnostiky pomocí šablon Azure Resource Manageru. 

## <a name="azure-cli-deployment"></a>Nasazení azure cli
Azure CLI slouží k nasazení rozšíření Diagnostika Azure do existujícího virtuálního počítače pomocí [sady rozšíření az vm](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) jako v následujícím příkladu. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Chráněná nastavení jsou definována v [elementu PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) schématu konfigurace. Následuje minimální příklad souboru chráněného nastavení, který definuje účet úložiště. Úplné podrobnosti o soukromých nastaveních najdete v [tématu Konfigurace příkladu.](diagnostics-extension-schema-windows.md#privateconfig-element)

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Veřejné nastavení jsou definovány v [Public prvek](diagnostics-extension-schema-windows.md#publicconfig-element) schématu konfigurace. Následuje minimální příklad souboru veřejného nastavení, který umožňuje kolekci protokolů diagnostické infrastruktury, jeden čítač výkonu a jeden protokol událostí. Úplné podrobnosti o veřejném nastavení najdete v [tématu Konfigurace příkladu.](diagnostics-extension-schema-windows.md#publicconfig-element)

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
Prostředí PowerShell lze použít k nasazení rozšíření Diagnostika Azure do existujícího virtuálního počítače pomocí [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) jako v následujícím příkladu. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Soukromé nastavení jsou definovány v [PrivateConfig element](diagnostics-extension-schema-windows.md#privateconfig-element), zatímco veřejné nastavení jsou definovány v [Public prvek](diagnostics-extension-schema-windows.md#publicconfig-element) schématu konfigurace. Můžete také zadat podrobnosti účtu úložiště jako parametry rutiny Set-AzVMDiagnosticsExtension, nikoli je zahrnout do soukromého nastavení.

Následuje minimální příklad konfiguračního souboru, který umožňuje kolekci protokolů diagnostické infrastruktury, jeden čítač výkonu a jeden protokol událostí. Úplné podrobnosti o soukromém a veřejném nastavení najdete v [tématu Konfigurace příkladu.](diagnostics-extension-schema-windows.md#publicconfig-element) 

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

Najdete v [tématu použití PowerShellu k povolení diagnostiky Azure ve virtuálním počítači se systémem Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Úložiště dat
V následující tabulce jsou uvedeny různé typy dat shromážděných z rozšíření diagnostiky a zda jsou uloženy jako tabulka nebo objekt blob. Data uložená v tabulkách mohou být také uložena v objektech BLOB v závislosti na [nastavení StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) ve veřejné konfiguraci.


| Data | Typ úložiště | Popis |
|:---|:---|:---|
| Tabulka logů WADDiagnosticInfrastructureLogsTable | Table | Diagnostické monitorování a změny konfigurace. |
| WadDirectoriesTabulka | Table | Adresáře, které monitorování diagnostiky monitoru monitoru sleduje.  To zahrnuje protokoly služby IIS, protokoly neúspěšných požadavků služby IIS a vlastní adresáře.  Umístění souboru protokolu objektu blob je určeno v poli Kontejner a název objektu blob je v poli RelativePath.  Pole AbsolutePath označuje umístění a název souboru tak, jak existoval ve virtuálním počítači Azure. |
| WadLogsTable | Table | Protokoly napsané v kódu pomocí naslouchací proces trasování. |
| Tabulka wadperformancecounterstable | Table | Čítače výkonu. |
| Tabulka logů událostí systému WADWindows | Table | Protokoly událostí systému Windows. |
| wad-iis-failedreqlogfiles wad-iis-failedreqlogfiles wad-iis-failedreqlogfiles wad- | Objekt blob | Obsahuje informace z protokolů neúspěšných požadavků služby IIS. |
| wad-iis-logfiles | Objekt blob | Obsahuje informace o protokolech iis. |
| "vlastní" | Objekt blob | Vlastní kontejner založený na konfiguraci adresářů, které jsou monitorovány diagnostickým monitorem.  Název tohoto kontejneru objektů blob bude určen v wadastable. |

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
K dispozici je několik nástrojů pro zobrazení dat po jejich přenosu do úložiště. Například:

* Průzkumník serveru v Visual Studiu – Pokud jste nainstalovali Nástroje Azure pro Microsoft Visual Studio, můžete pomocí uzlu Azure Storage v Průzkumníkovi serveru zobrazit data objektů blob a tabulky jen pro čtení z vašich účtů úložiště Azure. Můžete zobrazit data z účtu emulátoru místního úložiště a také z účtů úložiště, které jste vytvořili pro Azure. Další informace naleznete v [tématu Procházení a správa prostředků úložiště pomocí Průzkumníka serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která vám umožní snadno pracovat s daty Azure Storage na Windows, OSX a Linuxu.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) obsahuje Azure Diagnostics Manager, který vám umožní zobrazit, stáhnout a spravovat diagnostická data shromážděná aplikacemi spuštěnými v Azure.

## <a name="next-steps"></a>Další kroky
- Podrobnosti o předávání dat monitorování do Centra událostí Azure najdete v tématu [Odesílání dat z rozšíření diagnostiky Windows Azure do centra událostí.](diagnostics-extension-stream-event-hubs.md)
