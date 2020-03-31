---
title: Přidání monitorování & diagnostiky do virtuálního počítače Azure
description: Pomocí šablony Azure Resource Manager uvytvořit nový virtuální počítač Windows s rozšířením diagnostiky Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073846"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Použití monitorování a diagnostiky se šablonami virtuálních aplikací pro Windows a Azure Resource Manager
Rozšíření Diagnostika Azure poskytuje funkce monitorování a diagnostiky na virtuálním počítači Azure založeném na Windows. Tyto funkce můžete povolit ve virtuálním počítači zahrnutím rozšíření jako součást šablony Azure Resource Manager. Další informace o zahrnutí jakéhokoli rozšíření jako součásti šablony virtuálního počítače najdete v článku [Vytváření šablon Azure Resource Manageru s rozšířeními virtuálních](../windows/template-description.md#extensions) zařízení. Tento článek popisuje, jak můžete přidat rozšíření Diagnostika Azure do šablony virtuálního počítače windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Přidání rozšíření Diagnostika Azure do definice prostředků virtuálního počítače
Chcete-li povolit rozšíření diagnostiky ve virtuálním počítači se systémem Windows, musíte rozšíření přidat jako prostředek virtuálního počítače v šabloně Správce prostředků.

Pro jednoduchý virtuální počítač založený na Nástroji pro prostředky přidejte konfiguraci rozšíření do pole *prostředků* virtuálního počítače: 

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

Další běžnou konvencí je přidání konfigurace rozšíření v uzlu kořenových prostředků šablony namísto jeho definování pod uzětem prostředků virtuálního počítače. S tímto přístupem je nutné explicitně zadat hierarchický vztah mezi rozšířením a virtuálním počítačem s hodnotami *názvu* a *typu.* Například: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozšíření je vždy přidružené k virtuálnímu počítači, můžete buď přímo definovat pod uztelem prostředků virtuálního počítače přímo nebo definovat na základní úrovni a použít hierarchickou konvenci pojmenování k jeho přidružení k virtuálnímu počítači.

Pro sady velikosti virtuálních počítačů je konfigurace rozšíření určena ve vlastnosti *extensionProfile* profilu *virtualmachineprofile*.

Vlastnost *vydavatele* s hodnotou **Microsoft.Azure.Diagnostics** a vlastnost *type* s hodnotou **IaaSDiagnostics** jednoznačně identifikují rozšíření Diagnostika Azure.

Hodnotu vlastnosti *name* lze použít k odkazování na rozšíření ve skupině prostředků. Nastavení konkrétně **microsoft.Insights.VMDiagnosticsSettings** umožňuje snadno identifikovat portál Azure zajištění, že grafy monitorování se zobrazí správně na webu Azure Portal.

*TypeHandlerVersion* určuje verzi rozšíření, které chcete použít. Nastavení *autoUpgradeMinorVersion* dílčí verze **true** zajišťuje, že získáte nejnovější dílčí verzi rozšíření, která je k dispozici. Důrazně doporučujeme vždy nastavit *autoUpgradeMinorVersion* vždy **být pravda,** takže vždy získat k použití nejnovější dostupné diagnostické rozšíření se všemi novými funkcemi a opravy chyb. 

Element *nastavení* obsahuje vlastnosti konfigurace pro rozšíření, které lze nastavit a číst zpět z rozšíření (někdy označované jako veřejná konfigurace). Vlastnost *xmlcfg* obsahuje konfiguraci založenou na xml pro protokoly diagnostiky, čítače výkonu atd., které jsou shromažďovány diagnostickým agentem. Další informace o samotném schématu XML naleznete [v tématu Diagnostics Configuration Schema.](https://msdn.microsoft.com/library/azure/dn782207.aspx) Běžnou praxí je uložit skutečnou konfiguraci XML jako proměnnou v šabloně Azure Resource Manager a potom je zřetězit a base64 zakódovat a nastavit hodnotu pro *xmlcfg*. Další informace o způsobu ukládání xml do proměnných naleznete v části [o konfiguračních proměnných diagnostiky.](#diagnostics-configuration-variables) Vlastnost *storageAccount* určuje název účtu úložiště, do kterého se přenášejí diagnostická data. 

Vlastnosti v *protectedSettings* (někdy označované jako privátní konfigurace) lze nastavit, ale nelze číst zpět po nastavení. Pouze pro zápis povahy *protectedSettings* je užitečné pro ukládání tajných kódů, jako je klíč účtu úložiště, kde jsou zapsána data diagnostiky.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Určení účtu úložiště diagnostiky jako parametrů
Rozšíření diagnostiky json fragment výše předpokládá dva parametry *existingdiagnosticsStorageAccountName* a *existingdiagnosticsStorageResourceGroup* určit účet úložiště diagnostiky, kde jsou uložena diagnostická data. Určení účtu úložiště diagnostiky jako parametr usnadňuje změnu účtu úložiště diagnostiky v různých prostředích, například můžete chtít použít jiný účet úložiště diagnostiky pro testování a jiný účet pro vaše nasazení v produkčním prostředí.  

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

Je vhodné zadat účet úložiště diagnostiky v jiné skupině prostředků než skupina prostředků pro virtuální počítač. Skupinu prostředků lze považovat za jednotku nasazení s vlastní životností, virtuální počítač lze nasadit a znovu nasadit, protože se do ní budou provádět nové aktualizace konfigurace, ale můžete pokračovat v ukládání diagnostických dat ve stejném účtu úložiště napříč těchto nasazení virtuálních strojů. S účet úložiště v jiném prostředku umožňuje účet úložiště přijímat data z různých nasazení virtuálních počítačů usnadňuje řešení problémů v různých verzích.

> [!NOTE]
> Pokud vytvoříte šablonu virtuálního počítače systému Windows z visual studia, může být výchozí účet úložiště nastaven ý na stejný účet úložiště, ve kterém se nahraje virtuální počítač. To to je zjednodušit počáteční nastavení virtuálního počítači. Znovu faktor šablony použít jiný účet úložiště, které mohou být předány jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnostické konfigurační proměnné
Předchozí rozšíření diagnostiky json fragment definuje *accountid* proměnnou pro zjednodušení získání klíče účtu úložiště pro úložiště diagnostiky:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Vlastnost *xmlcfg* pro rozšíření diagnostiky je definována pomocí více proměnných, které jsou spojeny dohromady. Hodnoty těchto proměnných jsou ve formátu XML, takže je třeba je správně uvozevat při nastavování proměnných json.

Následující příklad popisuje konfiguraci xml konfigurace diagnostiky, která shromažďuje čítače výkonu standardní úrovně systému spolu s některými protokoly událostí systému Windows a protokoly infrastruktury diagnostiky. Byla uvozena a správně formátována tak, aby bylo možné konfiguraci přímo vložit do části proměnných šablony. Viz [schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) pro více lidské čitelný příklad konfigurace xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Uzel xml definice metriky ve výše uvedené konfiguraci je důležitým konfiguračním prvkem, protože definuje, jak jsou čítače výkonu definované dříve v uzlu xml v uzlu *PerformanceCounter* agregovány a uloženy. 

> [!IMPORTANT]
> Tyto metriky řídit monitorování grafy a výstrahy na webu Azure Portal.  **Uzel Metriky** s *resourceID* a **MetricAgregation** musí být zahrnuty v konfiguraci diagnostiky pro váš virtuální počítač, pokud chcete zobrazit data monitorování virtuálních počítačů na webu Azure Portal. 
> 
> 

Následující příklad ukazuje xml pro definice metrik: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Atribut *resourceID* jednoznačně identifikuje virtuální počítač ve vašem předplatném. Ujistěte se, že používáte funkce subscription() a resourceGroup() tak, aby šablona automaticky aktualizuje tyto hodnoty na základě předplatného a skupiny prostředků, do které nasazujete.

Pokud vytváříte více virtuálních počítačů ve smyčce, budete muset naplnit hodnotu *resourceID* s copyIndex() funkce správně odlišit každý jednotlivý virtuální počítač. Hodnota *xmlCfg* může být aktualizována takto:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Hodnota MetricAgregation *PT1M* a *PT1H* znamená agregaci za minutu a agregaci za hodinu.

## <a name="wadmetrics-tables-in-storage"></a>Tabulky WADMetrics ve skladu
Výše uvedená konfigurace Metriky generuje tabulky v účtu úložiště diagnostiky s následujícími konvencemi pojmenování:

* **WADMetrics**: Standardní předpona pro všechny tabulky WADMetrics
* **PT1H** nebo **PT1M**: Znamená, že tabulka obsahuje souhrnné údaje za 1 hodinu nebo 1 minutu
* **P10D**: Označuje, že tabulka bude obsahovat data po dobu 10 dnů od zahájení sběru dat v tabulce.
* **V2S**: Řetězcová konstanta
* **yyyymmdd**: Datum, kdy tabulka začala shromažďovat data

Příklad: *WADMetricsPT1HP10DV2S20151108* obsahuje data metrik agregovaných za hodinu po dobu 10 dnů počínaje 11-Nov-2015    

Každá tabulka WADMetrics obsahuje následující sloupce:

* **PartitionKey**: Klíč oddílu je vytvořen na základě hodnoty *resourceID* k jednoznačné identifikaci prostředku virtuálního počítače. Příklad: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Sleduje `<Descending time tick>:<Performance Counter Name>`formát . Výpočet sestupných časových značek je maximální čas ových značek mínus čas začátku období agregace. Pokud například výběrové období začalo 10-Nov-2015 a 00:00Hrs UTC, výpočet by byl: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Pro počítadlo výkonu dostupných v paměti bude vypadat klíč řádku takto:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: Je název čítače výkonu. To odpovídá *protispecifikátoru* definovanému v konfiguraci xml.
* **Maximum**: Maximální hodnota čítače výkonu za období agregace.
* **Minimální**: Minimální hodnota čítače výkonu za období agregace.
* **Celkem**: Součet všech hodnot čítače výkonu vykazovaných za období agregace.
* **Počet**: Celkový počet hodnot hlášených pro čítač výkonu.
* **Průměr**: Průměrná hodnota (celkem/počet) čítače výkonu za období agregace.

## <a name="next-steps"></a>Další kroky
* Kompletní ukázkovou šablonu virtuálního počítače s Windows s rozšířením diagnostiky najdete v [tématu 201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Nasazení šablony Azure Resource Manageru pomocí [Azure PowerShellu](../windows/ps-template.md) nebo [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Další informace o [vytváření šablon Azure Resource Manageru](../../resource-group-authoring-templates.md)
