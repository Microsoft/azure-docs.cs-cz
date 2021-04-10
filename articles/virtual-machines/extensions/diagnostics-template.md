---
title: Přidání diagnostiky & monitorování do virtuálního počítače Azure
description: K vytvoření nového virtuálního počítače s Windows pomocí rozšíření Azure Diagnostics použijte šablonu Azure Resource Manager.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 05/31/2017
ms.openlocfilehash: 6d365c7e927c11f52b97fbb0cc01a7aa37ad5afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560051"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Použití monitorování a diagnostiky pomocí virtuálních počítačů s Windows a Azure Resource Manager šablon
Rozšíření Azure Diagnostics poskytuje funkce pro monitorování a diagnostiku na virtuálním počítači Azure se systémem Windows. Tyto možnosti můžete na virtuálním počítači povolit zahrnutím rozšíření jako části šablony Azure Resource Manager. Další informace o začlenění všech rozšíření v rámci šablony virtuálního počítače najdete v tématu [vytváření Azure Resource Manager šablon s rozšířeními virtuálních počítačů](../windows/template-description.md#extensions) . Tento článek popisuje, jak můžete přidat rozšíření Azure Diagnostics do šablony virtuálního počítače s Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Přidání rozšíření Azure Diagnostics do definice prostředku virtuálního počítače
Pokud chcete povolit diagnostické rozšíření na virtuálním počítači s Windows, musíte v šabloně Správce prostředků přidat rozšíření jako prostředek virtuálního počítače.

U jednoduchého virtuálního počítače založeného na Správce prostředků přidejte konfiguraci rozšíření do pole *prostředků* pro virtuální počítač: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Další běžnou konvencí je přidání konfigurace rozšíření v uzlu kořenových prostředků šablony, a nikoli jejím definováním v uzlu prostředky virtuálního počítače. S tímto přístupem musíte explicitně zadat hierarchický vztah mezi příponou a virtuálním počítačem s hodnotami *název* a *typ* . Například: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Přípona je vždy přidružena k virtuálnímu počítači, můžete ji buď přímo definovat přímo v uzlu prostředku virtuálního počítače, nebo ji definovat na základní úrovni a pomocí hierarchické konvence vytváření názvů ji přidružit k virtuálnímu počítači.

Pro Virtual Machine Scale Sets konfigurace rozšíření je určena ve vlastnosti *ExtensionProfile* *VirtualMachineProfile*.

Vlastnost *vydavatele* s hodnotou **Microsoft. Azure. Diagnostics** a vlastností *typu* s hodnotou **IaaSDiagnostics** jednoznačně identifikují rozšíření Azure Diagnostics.

Hodnota vlastnosti *Name* se dá použít k odkazování na rozšíření ve skupině prostředků. Nastavení konkrétně na **Microsoft. Insights. VMDiagnosticsSettings** umožňuje snadnou identifikaci pomocí Azure Portal zajistit, aby se grafy monitorování správně zobrazovaly v Azure Portal.

*TypeHandlerVersion* určuje verzi rozšíření, která se má použít. Nastavení podverze *autoUpgradeMinorVersion* na **hodnotu true** zajistí, že získáte nejnovější podverzi rozšíření, která je k dispozici. Důrazně doporučujeme, abyste vždycky nastavili *autoUpgradeMinorVersion* vždy na **true** , abyste vždy používali nejnovější dostupné diagnostické rozšíření se všemi novými funkcemi a opravami chyb. 

Element *Settings* obsahuje vlastnosti konfigurace pro rozšíření, které lze nastavit a načíst zpět z rozšíření (někdy označovaného jako veřejná konfigurace). Vlastnost *xmlcfg* obsahuje konfiguraci založenou na XML pro diagnostické protokoly, čítače výkonu atd. shromažďované agentem diagnostiky. Další informace o samotném schématu XML najdete v tématu věnovaném [schématu konfigurace diagnostiky](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) . Běžnou praxí je uložit skutečnou konfiguraci XML jako proměnnou v šabloně Azure Resource Manager a potom zřetězit a kódování Base64, aby se nastavila hodnota pro *xmlcfg*. Další informace o tom, jak soubor XML ukládat do proměnných, najdete v části o [proměnných konfigurace diagnostiky](#diagnostics-configuration-variables) . Vlastnost *storageAccount* Určuje název účtu úložiště, do kterého se budou přenášet diagnostická data. 

Vlastnosti v *protectedSettings* (někdy označované jako soukromá konfigurace) lze nastavit, ale po nastavení nelze číst znovu. Povaha jen pro zápis *protectedSettings* je užitečná pro ukládání tajných kódů, jako je klíč účtu úložiště, do kterého se zapisují diagnostická data.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Zadání účtu úložiště diagnostiky jako parametrů
Fragment kódu JSON pro diagnostické rozšíření předpokládá dva parametry *existingdiagnosticsStorageAccountName* a *existingdiagnosticsStorageResourceGroup* k určení účtu úložiště diagnostiky, kde jsou uložená diagnostická data. Zadání účtu úložiště pro diagnostiku jako parametru usnadňuje změnu účtu úložiště diagnostiky v různých prostředích, například můžete chtít použít jiný účet úložiště diagnostiky pro testování a jiný pro produkční nasazení.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Osvědčeným postupem je zadat účet úložiště diagnostiky v jiné skupině prostředků, než je skupina prostředků pro virtuální počítač. Skupina prostředků se dá považovat za jednotku nasazení s vlastní životností. virtuální počítač se dá nasadit a znovu nasadit, protože se k němu aktualizují nové konfigurace, ale možná budete chtít pokračovat v ukládání diagnostických dat do stejného účtu úložiště v těchto nasazeních virtuálních počítačů. Když účet úložiště v jiném prostředku povolíte, může účet úložiště přijímat data z různých nasazení virtuálních počítačů, což usnadňuje řešení problémů v různých verzích.

> [!NOTE]
> Pokud vytvoříte šablonu virtuálního počítače s Windows ze sady Visual Studio, může se výchozí účet úložiště nastavit tak, aby používal stejný účet úložiště, ve kterém se nahrál virtuální pevný disk virtuálního počítače. Tím se zjednoduší počáteční nastavení virtuálního počítače. Přefaktorujte šablonu, aby používala jiný účet úložiště, který se dá předat jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Proměnné konfigurace diagnostiky
Předchozí fragment kódu JSON pro diagnostiku definuje proměnnou *accountid* , aby se zjednodušilo získání klíče účtu úložiště pro úložiště diagnostiky:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Vlastnost *xmlcfg* pro rozšíření diagnostiky je definována pomocí více proměnných, které jsou zřetězeny dohromady. Hodnoty těchto proměnných jsou v XML, takže musí být při nastavení proměnných JSON správně uvozeny řídicími znaky.

Následující příklad popisuje konfigurační XML diagnostiky, který shromažďuje standardní čítače výkonu na úrovni systému spolu s některými protokoly událostí systému Windows a protokoly infrastruktury diagnostiky. Byl označený a správně naformátovaný, aby bylo možné konfiguraci přímo vložit do oddílu Variables vaší šablony. V tématu [schéma konfigurace diagnostiky](../../azure-monitor/agents/diagnostics-extension-schema-windows.md) si můžete přečíst příklad kódu XML pro snadnější čtení.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Uzel XML definice metrik v rámci výše uvedené konfigurace je důležitý prvek konfigurace, který definuje, jak jsou agregované a uložené čítače výkonu definované dříve v souboru XML v uzlu *PerformanceCounter* . 

> [!IMPORTANT]
> Tyto metriky řídí grafy monitorování a výstrahy v Azure Portal.  Uzel **metriky** s *ID ResourceID* a **MetricAggregation** musí být zahrnut do konfigurace diagnostiky pro váš virtuální počítač, pokud chcete zobrazit data monitorování virtuálních počítačů v Azure Portal. 
> 
> 

Následující příklad ukazuje XML pro definice metrik: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Atribut *ResourceID* jednoznačně identifikuje virtuální počítač v rámci vašeho předplatného. Ujistěte se, že používáte funkce Subscription () a Resource (), takže šablona automaticky aktualizuje tyto hodnoty na základě předplatného a skupiny prostředků, do které nasazujete.

Pokud vytváříte více Virtual Machines ve smyčce, je nutné naplnit hodnotu *ResourceID* pomocí funkce copyIndex (), aby se správně lišil každý JEDNOTLIVÝ virtuální počítač. Hodnotu *xmlCfg* můžete aktualizovat tak, aby podporovala následující:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Hodnota MetricAggregation *PT1M* a *PT1H* značí agregaci za minutu a agregaci za hodinu, v uvedeném pořadí.

## <a name="wadmetrics-tables-in-storage"></a>Tabulky WADMetrics v úložišti
Výše uvedená konfigurace metrik generuje tabulky v účtu úložiště diagnostiky s následujícími konvencemi pro pojmenování:

* **WADMetrics**: standardní předpona pro všechny tabulky WADMetrics
* **PT1H** nebo **PT1M**: značí, že tabulka obsahuje agregovaná data za 1 hodinu nebo 1 minutu.
* **P10D**: značí, že tabulka bude obsahovat data po dobu 10 dní od okamžiku, kdy tabulka začala shromažďovat data.
* **V2S**: String – konstanta
* **RRRRMMDD**: datum, kdy tabulka začala shromažďovat data

Příklad: *WADMetricsPT1HP10DV2S20151108* obsahuje data metrik agregovaná za hodinu po dobu 10 dní od 11. listopadu 2015.    

Každá tabulka WADMetrics obsahuje následující sloupce:

* **PartitionKey**: klíč oddílu je vytvořen na základě hodnoty *ResourceID* k jedinečné identifikaci prostředku virtuálního počítače. Příklad: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: následuje formát `<Descending time tick>:<Performance Counter Name>` . Výpočet vzestupného časového intervalu je maximální časový interval v čase začátku agregačního období. Pokud například začíná ukázková Perioda 10. listopadu-2015 a 00:00Hrs UTC, pak výpočet by byl: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . V čítači výkonu dostupné bajty paměti bude klíč řádku vypadat takto: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: je název čítače výkonu. To odpovídá *counterSpecifier* definovanému v konfiguraci XML.
* **Maximum**: maximální hodnota čítače výkonu v rámci agregačního období.
* **Minimum**: minimální hodnota čítače výkonu v rámci agregačního období.
* **Total**: součet všech hodnot čítače výkonu hlášených v rámci agregačního období.
* **Count**: celkový počet hodnot hlášených pro čítač výkonu.
* **Average**: Průměrná hodnota (celková hodnota/počet) čítače výkonu v rámci agregovaného období.

## <a name="next-steps"></a>Další kroky
* Úplnou ukázkovou šablonu virtuálního počítače s Windows s diagnostickým rozšířením najdete v tématu [201-VM-monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension) .   
* Nasazení šablony Azure Resource Manager pomocí [Azure PowerShell](../windows/ps-template.md) nebo [příkazového řádku Azure](../linux/create-ssh-secured-vm-from-template.md)
* Další informace o [vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md)