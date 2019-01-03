---
title: Přidat monitorování a Diagnostika na virtuálním počítači Azure | Dokumentace Microsoftu
description: Pomocí šablony Azure Resource Manageru k vytvoření nového virtuálního počítače Windows pomocí rozšíření Azure diagnostics.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85e9b49cb8be1a3f53ca0f3b4816e6165b68bde0
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993100"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Monitorováním a diagnostikou pomocí šablony virtuálního počítače Windows a Azure Resource Manageru
Rozšíření Azure Diagnostics nabízí možnosti monitorování a diagnostiku v virtuálnímu počítači s Windows v Azure. Je-li povolit tyto možnosti na virtuálním počítači, včetně přípony jako součást šablony Azure Resource Manageru. Zobrazit [vytváření šablon Azure Resource Manageru pomocí rozšíření virtuálních počítačů](../windows/template-description.md#extensions) Další informace o včetně všechna rozšíření jako součást šablony virtuálního počítače. Tento článek popisuje, jak můžete přidat rozšíření Azure Diagnostics na šablonu virtuálního počítače windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Přidání rozšíření Azure Diagnostics do definice prostředků virtuálního počítače
Pokud chcete povolit rozšíření diagnostiky na virtuální počítač Windows, budete muset přidat rozšíření jako prostředek virtuálního počítače v šabloně Resource Manageru.

Pro jednoduché Resource Manageru na základě virtuálního počítače přidejte konfigurace rozšíření *prostředky* pole pro virtuální počítač: 

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

Další běžné konvence je přidání konfigurace rozšíření v kořenovém uzlu prostředků šablony místo definování pod uzlem prostředky virtuálního počítače. S tímto přístupem musíte explicitně zadat hierarchický vztah mezi rozšíření a virtuální počítač s *název* a *typ* hodnoty. Příklad: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozšíření je vždy přidružený virtuální počítač, můžete buď přímo přímo definovat v rámci uzlu prostředku virtuálního počítače nebo jej definovat na úrovni základní a použít hierarchická zásady vytváření názvů pro jeho přiřazení k virtuálnímu počítači.

Pro Škálovací sady virtuálních počítačů je podle konfigurace rozšíření *extensionProfile* vlastnost *VirtualMachineProfile*.

*Vydavatele* vlastnost s hodnotou **Microsoft.Azure.Diagnostics** a *typ* vlastnost s hodnotou **IaaSDiagnostics**jednoznačné identifikaci rozšíření Azure Diagnostics.

Hodnota *název* vlastnosti lze odkazovat na rozšíření ve skupině prostředků. Nastavení konkrétně pro **Microsoft.Insights.VMDiagnosticsSettings** umožňuje snadno identifikovat pomocí webu Azure portal, zajištění, že monitorování grafy zobrazit si správně na webu Azure Portal.

*TypeHandlerVersion* Určuje verzi rozšíření, které chcete použít. Nastavení *autoUpgradeMinorVersion* podverze k **true** zajistí, že dostanete nejnovější dílčí verzi rozšíření, která je k dispozici. Důrazně doporučujeme vždy nastavit *autoUpgradeMinorVersion* vždy **true** tak, že vždycky získáte nejnovější dostupné diagnostické rozšíření používat nové funkce a opravy chyb. 

*Nastavení* prvek obsahuje vlastnosti konfigurace pro rozšíření, které můžete nastavit a číst z rozšíření (někdy označované jako veřejné konfigurace). *Xmlcfg* vlastnost obsahuje xml na základě konfigurace pro diagnostické protokoly, čítače výkonu atd, které se shromažďují pomocí agenta diagnostiky. Zobrazit [schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) Další informace o schématu xml, samotného. Běžnou praxí je ukládat konfiguraci skutečný xml jako proměnné v šabloně Azure Resource Manageru a pak je zřetězí a zakódovat base64, je nastavit hodnotu pro *xmlcfg*. Naleznete v části [proměnné konfigurace diagnostiky](#diagnostics-configuration-variables) bližší informace o tom, jak uložit xml do proměnné. *StorageAccount* vlastnost určuje název účtu úložiště, na které diagnostiku data se přenáší. 

Vlastnosti v *protectedSettings* (někdy označované jako privátní konfigurace). je možné nastavit, ale nelze číst zpět po nastavena. Pouze pro zápis povaze *protectedSettings* je vhodná pro ukládání tajných klíčů, jako je klíč účtu úložiště níž je zapsána diagnostická data.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Zadáte účet úložiště diagnostiky jako parametry
Diagnostické rozšíření fragmentu kódu json výše předpokládá dva parametry *existingdiagnosticsStorageAccountName* a *existingdiagnosticsStorageResourceGroup* zadat úložiště diagnostiky účet diagnostická data se mají ukládat. Zadáte účet úložiště diagnostiky jako parametr snadno změnit účet úložiště diagnostiky v různých prostředích, například můžete chtít použít jinou diagnostiky účtu úložiště pro účely testování a jiné pro vaše produkční nasazení.  

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

Je osvědčeným postupem je v jiné skupině prostředků než skupiny prostředků pro virtuální počítač zadat účet úložiště diagnostiky. Skupinu prostředků lze považovat za jednotku nasazení s svou vlastní životnost, je možné nasadit virtuální počítač a znovu nasadit jako nové aktualizace konfigurace jsou provedeny do ní, ale můžete chtít pokračovat v ukládání diagnostických dat v rámci stejného účtu úložiště napříč nasazení těchto virtuálních počítačů. Máte účet úložiště v různých prostředků umožňuje účet úložiště tak, aby přijímal data z různých nasazení virtuálních počítačů, což usnadňuje řešení problémů napříč různými verzemi.

> [!NOTE]
> Pokud vytvoříte šablonu virtuálního počítače windows ze sady Visual Studio, výchozí účet úložiště může být nastaveno na použití stejného účtu úložiště, kde je virtuální počítač virtuální pevný disk nahrát. Cílem je zjednodušit počáteční instalaci virtuálního počítače. Znovu faktor šablonu, kterou chcete použít jiný účet úložiště, které může být předáno jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Proměnné konfigurace diagnostiky
Definuje předchozím fragmentu kódu json rozšíření diagnostiky *accountid* proměnnou ke zjednodušení získání klíče účtu úložiště pro ukládání diagnostiky:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg* je definována vlastnost pro rozšíření diagnostiky pomocí více proměnných, které jsou společně zřetězených. Hodnoty těchto proměnných jsou ve formátu xml, takže musí být správně uvozené při nastavování proměnné json.

Následující příklad popisuje kód xml konfigurace diagnostiky shromažďuje čítače výkonu úrovně standard systému společně se některé protokoly událostí systému windows a protokoly infrastruktury diagnostiky. Bylo uvozen řídicími znaky a správně naformátovaná tak, aby konfigurace můžete vložit přímo do části proměnných šablony. Zobrazit [schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) víc jako lidé čitelné příklad konfigurační soubor xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Uzel xml definice metrik v konfiguraci uvedené výš je důležité konfigurační prvek, protože definují, jak se čítače výkonu definovaným dříve v xml v *PerformanceCounter* uzlu se agregují a uložené. 

> [!IMPORTANT]
> Tyto metriky jednotka sledování grafů a výstrah na webu Azure Portal.  **Metriky** uzel s *resourceID* a **MetricAggregation** musí obsahovat konfiguraci diagnostiky pro virtuální počítač, pokud chcete zobrazit data v monitorování virtuálního počítače na webu Azure portal. 
> 
> 

Následující příklad ukazuje kód xml pro definice metriky: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*ResourceID* atribut jednoznačně identifikuje virtuální počítač v rámci vašeho předplatného. Ujistěte se, že funkce subscription() a resourceGroup() tak, aby šablona automaticky aktualizuje tyto hodnoty na základě předplatné a skupinu prostředků, kterou nasazujete.

Pokud vytváříte několik virtuálních počítačů ve smyčce, budete muset naplnit *resourceID* hodnotu funkci copyIndex() správně odlišit od jednotlivých konkrétního virtuálního počítače. *XmlCfg* hodnota může být aktualizované kvůli podpoře to následujícím způsobem:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Hodnota MetricAggregation *PT1M* a *PT1H* místo agregaci po minutách a agregaci více než hodinu, v uvedeném pořadí.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabulek ve službě storage
Výše uvedené metriky konfigurace generuje tabulek ve vašem účtu úložiště diagnostiky s následující názvové konvence:

* **WADMetrics**: Standardní prefix pro všechny tabulky WADMetrics
* **PT1H** nebo **PT1M**: Označuje, že tabulka obsahuje data agregace více než 1 hodinu nebo minutu
* **P10D**: Označuje, že v tabulce bude obsahovat data po dobu 10 dnů od začátku tabulky, shromažďování dat
* **V2S**: Řetězcová konstanta
* **RRRRMMDD**: Datum, kdy spustit shromažďování dat v tabulce

Příklad: *WADMetricsPT1HP10DV2S20151108* obsahuje data metriky agregované za hodinu pro 10 dnů od 11. listopadu 2015    

Každá tabulka WADMetrics obsahuje následující sloupce:

* **PartitionKey**: Klíč oddílu je vytvořený na základě *resourceID* hodnotu k jednoznačné identifikaci prostředku virtuálního počítače. Příklad: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Následuje formát `<Descending time tick>:<Performance Counter Name>`. Sestupné značek výpočet času je značky maximální čas mínus čas začátku období agregace. Například pokud doba vzorku spustit 10. listopadu 2015 a 00:00Hrs UTC pak výpočtu by byl: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Dostupné bajty paměti čítače výkonu klíč řádku bude vypadat takto: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **Hodnota counterName**: Je název čítače výkonu. To odpovídá *counterSpecifier* definované v konfiguraci xml.
* **Maximální**: Maximální hodnota čítače výkonu za období agregace.
* **Minimální**: Minimální hodnota čítače výkonu za období agregace.
* **Celkový počet**: Součet všech hodnot čítače výkonu ohlásil období agregace.
* **Počet**: Celkový počet hodnot hlášené pro čítač výkonu.
* **Průměrná**: Průměr (celkem nebo count) hodnota čítače výkonu za období agregace.

## <a name="next-steps"></a>Další kroky
* Kompletní ukázkovou šablonu virtuálního počítače Windows s diagnostickým rozšířením, naleznete v tématu [201-vm monitorování –-rozšíření diagnostiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Nasazení pomocí šablony Azure Resource Manageru [prostředí Azure PowerShell](../windows/ps-template.md) nebo [příkazového řádku Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Další informace o [Tvorba šablon Azure Resource Manageru](../../resource-group-authoring-templates.md)
