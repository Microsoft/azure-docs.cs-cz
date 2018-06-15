---
title: Přidat monitorování & diagnostiky pro virtuální počítač Azure | Microsoft Docs
description: Vytvoření nového virtuálního počítače Windows Azure diagnostics rozšíření pomocí šablony Azure Resource Manager.
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
ms.openlocfilehash: e54060769f19546ad3ccb8c52df928eeebf03776
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917033"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Monitorování a Diagnostika pomocí šablony virtuálního počítače s Windows a Azure Resource Manager
Rozšíření diagnostiky Azure poskytuje monitorovací a diagnostické funkce v systému Windows Azure virtuálního počítače. Tyto funkce na virtuálním počítači můžete povolit v rámci šablony Azure Resource Manageru, přiložením rozšíření. V tématu [vytváření šablon Azure Resource Manager pomocí rozšíření virtuálního počítače](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) Další informace o včetně všechna rozšíření v rámci šablony virtuálního počítače. Tento článek popisuje, jak přidat rozšíření Azure Diagnostics do šablony virtuálního počítače windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Přidejte rozšíření Azure Diagnostics do definice prostředků virtuálního počítače
Pokud chcete povolit rozšíření diagnostiky na virtuální počítač Windows, musíte přidat příponu jako prostředek virtuálního počítače v šabloně Resource Manager.

Pro jednoduché Resource Manager na bázi virtuálního počítače přidat do konfigurace rozšíření *prostředky* pole pro virtuální počítač: 

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

Další běžné konvence je přidání konfigurace rozšíření na kořenový uzel prostředků šablony místo definice prostředků uzlu virtuálního počítače. S tímto přístupem, je třeba explicitně zadat hierarchický vztah mezi rozšíření a virtuální počítač s *název* a *typ* hodnoty. Příklad: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozšíření je vždy přidružený virtuální počítač, můžete buď přímo přímo definovat uzlu prostředků virtuálního počítače nebo definovat na úrovni základní a přidružit ho k virtuálnímu počítači pomocí hierarchické zásady vytváření názvů.

Konfigurace rozšíření je pro sady škálování virtuálního počítače zadaná v *extensionProfile* vlastnost *VirtualMachineProfile*.

*Vydavatele* vlastnosti s hodnotou **Microsoft.Azure.Diagnostics** a *typ* vlastnosti s hodnotou **IaaSDiagnostics** jednoznačně rozšíření Azure Diagnostics.

Hodnota *název* vlastnost lze použít k odkazování na rozšíření ve skupině prostředků. Konkrétně pro jeho nastavení **Microsoft.Insights.VMDiagnosticsSettings** umožní, aby snadno identifikovat na portálu Azure, zajistíte, že monitorování grafy zobrazují až správně na portálu Azure.

*TypeHandlerVersion* Určuje verzi rozšíření, které chcete použít. Nastavení *autoUpgradeMinorVersion* podverze k **true** zajistí, že dostanete nejnovější dílčí verzi rozšíření, která je k dispozici. Důrazně doporučujeme vždy nastavit *autoUpgradeMinorVersion* vždycky být **true** , abyste měli vždy použít nejnovější rozšíření diagnostiky k dispozici všechny nové funkce a opravy chyb. 

*Nastavení* prvek obsahuje vlastnosti konfigurace pro rozšíření, které můžete nastavit a načíst zpět z rozšíření (někdy označované jako veřejné konfigurace). *Xmlcfg* vlastnost obsahuje konfigurační soubor xml na základě protokolů diagnostiky čítače výkonu atd, které byly shromážděny sadou agenta diagnostiky. V tématu [schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) Další informace o schématu xml, sám sebe. Běžnou praxí je pro uložení konfigurační soubor xml skutečné jako proměnné v šablony Azure Resource Manageru a pak zřetězení a kódování base64 je nastavit hodnotu pro *xmlcfg*. Projděte část o [proměnné konfigurace diagnostiky](#diagnostics-configuration-variables) bližší informace o tom, jak uložit xml do proměnné. *StorageAccount* vlastnost určuje název účtu úložiště, které diagnostiku data se přenáší. 

Vlastnosti v *protectedSettings* (někdy označují jako privátní konfigurace) můžete nastavit, ale nemůže přečíst zpět po nastaveno. Jen pro zápis povaze *protectedSettings* je vhodná pro ukládání tajné klíče jako klíč účtu úložiště umístění, kam je zapisován diagnostická data.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Zadání účtu úložiště diagnostiky jako parametry
Diagnostika rozšíření json výše uvedeném fragmentu předpokládá dva parametry *existingdiagnosticsStorageAccountName* a *existingdiagnosticsStorageResourceGroup* k určení úložiště diagnostiky účet, které jsou uložená data diagnostiky. Zadání účtu úložiště diagnostiky, jako parametr lze snadno změnit účet úložiště diagnostiky různých prostředích, například můžete chtít použít jiný diagnostiky účtu úložiště pro testování a jinou pro vaše produkčním nasazení.  

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

Je vhodné v jiné skupině prostředků než skupinu prostředků pro virtuální počítač zadejte účet úložiště diagnostiky. Skupinu prostředků lze považovat za jako jednotky nasazení s vlastním životního cyklu, virtuální počítač můžete nasadit a znovu nasadit jako nové aktualizace konfigurace jsou vytvářeny k němu ale můžete chtít pokračovat v ukládání dat diagnostiky ve stejném účtu úložiště mezi tyto nasazení virtuálních počítačů. S účet úložiště v různých prostředků umožňuje účet úložiště tak, aby přijímal data z různých nasazení virtuálních počítačů, a usnadňuje tak řešení problémů mezi různými verzemi.

> [!NOTE]
> Pokud vytvoříte šablonu virtuálního počítače windows ze sady Visual Studio, může být nastaveno výchozí účet úložiště použít stejný účet úložiště, kde je načtený pevný disk virtuálního počítače. Toto je ke zjednodušení počáteční nastavení virtuálního počítače. Znovu dvoufaktorového šablonu, kterou chcete použít jiný účet úložiště, může být předán jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Proměnné konfigurace diagnostiky
Definuje diagnostiky rozšíření json výše uvedeném fragmentu *accountid* proměnná zjednodušit získávání klíč účtu úložiště pro úložiště diagnostiky:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg* vlastnost pro rozšíření diagnostiky je definovaná pomocí více proměnných, které jsou zřetězeny společně. Hodnoty těchto proměnných jsou ve formátu xml, takže budou muset být správně uvozeny zpětným lomítkem při nastavování proměnné json.

Následující část popisuje konfigurační soubor xml diagnostiky, která shromažďuje čítače úrovně výkonu standardní systému spolu se některé protokoly událostí systému windows a protokolů diagnostiky infrastruktury. Je uvozena a zda je správně naformátován tak, aby konfigurace lze vložit přímo do části proměnných šablony. Najdete v článku [schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/dn782207.aspx) více lidského čitelný příklad kódu XML konfigurace.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Uzel metriky definice xml v konfiguraci výše je důležité konfigurační element jako definuje, jak se čítače výkonu definované výše v kód xml v *PerformanceCounter* uzlu se agregují a uložené. 

> [!IMPORTANT]
> Tyto metriky jednotky monitorování grafů a výstrahy na portálu Azure.  **Metriky** uzel s *resourceID* a **MetricAggregation** musí obsahovat v konfiguraci diagnostiky pro virtuální počítač, pokud chcete zobrazit data na portálu Azure monitorování virtuálního počítače. 
> 
> 

Následuje příklad kódu XML definice metriky: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*ResourceID* atribut jednoznačně identifikuje virtuální počítač v rámci vašeho předplatného. Ujistěte se, že pomocí funkcí subscription() a resourceGroup() tak, aby šablona automaticky aktualizuje tyto hodnoty na základě předplatného a skupiny prostředků, které nasazujete.

Pokud vytváříte více virtuálních počítačů ve smyčce, máte k naplnění *resourceID* hodnoty pomocí funkce copyIndex() správně odlišit od jednotlivých jednotlivých virtuálních počítačů. *XmlCfg* hodnota může být aktualizované kvůli podpoře to následujícím způsobem:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Hodnota MetricAggregation *PT1M* a *PT1H* označily agregace za minutu a agregace přes hodinu, v uvedeném pořadí.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabulek v úložišti
Výše uvedené metriky konfigurace generuje tabulky ve vašem účtu úložiště diagnostiky s následující názvové konvence:

* **WADMetrics**: standardní předponou pro všechny tabulky WADMetrics
* **PT1H** nebo **PT1M**: označuje, zda tabulka obsahuje agregovaná data více než 1 hodina nebo 1 minuta
* **P10D**: označuje, že v tabulce budou obsahovat data pro 10 dní od při spuštění v tabulce shromažďování dat
* **V2S**: řetězcová konstanta
* **RRRRMMDD**: datum, kdy došlo v tabulce shromažďování dat

Příklad: *WADMetricsPT1HP10DV2S20151108* budou obsahovat data metriky agregovat přes hodinu 10 dní od 11. listopadu 2015    

Každá tabulka WADMetrics bude obsahovat následující sloupce:

* **PartitionKey**: klíč oddílu je sestavený na základě *resourceID* hodnotu k jednoznačné identifikaci prostředků virtuálního počítače. Příklad: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: strukturu `<Descending time tick>:<Performance Counter Name>`. Sestupné značek výpočet doby je maximální doba rysky minus čas začátku období agregace. Pokud například na 10. listopadu 2015 spustit ukázkový období a 00:00Hrs UTC pak výpočet by: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Pro paměť bajty k dispozici čítače výkonu klíč řádku bude vypadat jako: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **Název_čítače**: název čítače výkonu. To odpovídá *counterSpecifier* definované v xml konfigurace.
* **Maximální**: maximální hodnota čítače výkonu období agregace.
* **Minimální**: minimální hodnota čítače výkonu období agregace.
* **Celkový počet**: součet všech hodnot čítače výkonu hlášené období agregace.
* **Počet**: Celkový počet hodnot hlášené pro čítač výkonu.
* **Průměrná**: průměr (celkem nebo počet) hodnota čítače výkonu období agregace.

## <a name="next-steps"></a>Další kroky
* Pro dokončení vzorové šablony z virtuálního počítače s Windows pomocí rozšíření diagnostiky, najdete v části [201-vm monitorování--rozšíření diagnostiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Nasazení pomocí šablony Azure Resource Manager [prostředí Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [příkazového řádku Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Další informace o [vytváření šablon Azure Resource Manager](../../resource-group-authoring-templates.md)
