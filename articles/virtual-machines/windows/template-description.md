---
title: Virtuální počítače v šabloně Azure Resource Manager | Microsoft Azure
description: Přečtěte si další informace o tom, jak je prostředek virtuálního počítače definovaný v Azure Resource Manager šabloně.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 2726ba3b337dc785de58b06fb5e8372058f71879
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202092"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuální počítače v šabloně Resource Manageru

Tento článek popisuje aspekty Azure Resource Manager šablony, které se vztahují k virtuálním počítačům. Tento článek nepopisuje úplnou šablonu pro vytvoření virtuálního počítače. potřebujete definice prostředků pro účty úložiště, síťová rozhraní, veřejné IP adresy a virtuální sítě. Další informace o tom, jak se tyto prostředky dají definovat společně, najdete v [návodu k šabloně správce prostředků](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Galerie obsahuje mnoho [šablon](https://azure.microsoft.com/documentation/templates/?term=VM) , které zahrnují prostředek virtuálního počítače. Ne všechny prvky, které lze zahrnout do šablony, jsou popsány zde.

 

Tento příklad ukazuje typický oddíl prostředků šablony pro vytvoření zadaného počtu virtuálních počítačů:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Tento příklad spoléhá na účet úložiště, který byl dříve vytvořen. Účet úložiště můžete vytvořit tak, že ho nasadíte ze šablony. Příklad také závisí na síťovém rozhraní a jeho závislých prostředcích, které by byly definovány v šabloně. Tyto prostředky nejsou uvedeny v tomto příkladu.
>
>

## <a name="api-version"></a>Verze rozhraní API

Při nasazení prostředků pomocí šablony musíte zadat verzi rozhraní API, která se má použít. V příkladu se zobrazuje prostředek virtuálního počítače pomocí tohoto elementu apiVersion:

```json
"apiVersion": "2016-04-30-preview",
```

Verze rozhraní API, kterou zadáte v šabloně, má vliv na to, které vlastnosti můžete v šabloně definovat. Obecně platí, že při vytváření šablon byste měli vybrat nejnovější verzi rozhraní API. U existujících šablon se můžete rozhodnout, jestli chcete pokračovat v používání dřívější verze rozhraní API, nebo aktualizovat šablonu pro nejnovější verzi, abyste mohli využívat nové funkce.

Tyto příležitosti použijte k získání nejnovějších verzí rozhraní API:

- REST API – [vypíše všechny poskytovatele prostředků](/rest/api/resources/providers) .
- PowerShell – [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI – [AZ Provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametry a proměnné

[Parametry](../../azure-resource-manager/templates/template-syntax.md) usnadňují zadání hodnot pro šablonu při jejím spuštění. V příkladu se používá tento oddíl parametrů:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Když nasadíte ukázkovou šablonu, zadáte hodnoty pro název a heslo účtu správce na každém virtuálním počítači a počet virtuálních počítačů, které se mají vytvořit. Máte možnost zadat hodnoty parametrů v samostatném souboru, který je spravován šablonou, nebo zadat hodnoty po zobrazení výzvy.

[Proměnné](../../azure-resource-manager/templates/template-syntax.md) usnadňují nastavení hodnot v šabloně, které se v něm používají opakovaně, nebo které se můžou v průběhu času měnit. Tento oddíl proměnných se používá v tomto příkladu:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Když nasadíte ukázkovou šablonu, použijí se pro název a identifikátor dříve vytvořeného účtu úložiště proměnné hodnoty proměnných. Proměnné také slouží k poskytnutí nastavení pro diagnostické rozšíření. Použijte [osvědčené postupy pro vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-best-practices.md) , které vám pomůžou určit, jak chcete strukturovat parametry a proměnné ve vaší šabloně.

## <a name="resource-loops"></a>Smyčky prostředků

Pokud pro svou aplikaci potřebujete více než jeden virtuální počítač, můžete použít kopírovací prvek v šabloně. Tento volitelný element Cykluje vytvořením počtu virtuálních počítačů, které jste zadali jako parametr:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Všimněte si také, že je v příkladu použit index smyčky při zadávání některých hodnot prostředku. Pokud jste například zadali počet instancí tři, názvy disků operačního systému jsou myOSDisk1, myOSDisk2 a myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>V tomto příkladu se pro virtuální počítače používají spravované disky.
>
>

Pamatujte, že vytvoření smyčky pro jeden prostředek v šabloně může vyžadovat, abyste při vytváření nebo přístupu k jiným prostředkům použili smyčku. Například více virtuálních počítačů nemůže používat stejné síťové rozhraní, takže pokud vaše šablona projde vytvořením tří virtuálních počítačů, musí se také cyklicky vytvořit tři síťová rozhraní. Při přiřazování síťového rozhraní k virtuálnímu počítači se k jeho identifikaci používá index smyčky:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Závislosti

Většina prostředků závisí na dalších prostředcích, aby fungovaly správně. Virtuální počítače musí být přidružené k virtuální síti a k tomu potřebují síťové rozhraní. Element [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) se používá k zajištění, že síťové rozhraní je připravené k použití před vytvořením virtuálních počítačů:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Správce prostředků nasadí paralelně všechny prostředky, které nejsou závislé na nasazeném jiném prostředku. Při nastavování závislostí buďte opatrní, protože je možné neúmyslně zpomalit nasazení zadáním zbytečných závislostí. Závislosti můžou zřetězit více prostředků. Například síťové rozhraní závisí na veřejné IP adrese a prostředcích virtuální sítě.

Jak zjistíte, jestli je požadovaná závislost? Podívejte se na hodnoty, které jste nastavili v šabloně. Pokud element v definici prostředků virtuálního počítače odkazuje na jiný prostředek, který je nasazený ve stejné šabloně, budete potřebovat závislost. Například ukázkový virtuální počítač definuje profil sítě:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Chcete-li nastavit tuto vlastnost, musí existovat síťové rozhraní. Proto potřebujete závislost. Je také nutné nastavit závislost, pokud je jeden prostředek (podřízený) definován v rámci jiného prostředku (nadřazený objekt). Například nastavení diagnostiky a rozšíření vlastních skriptů se definují jako podřízené prostředky virtuálního počítače. Nedají se vytvořit, dokud virtuální počítač neexistuje. Proto jsou oba prostředky označeny jako závislé na virtuálním počítači.

## <a name="profiles"></a>Profily

Při definování prostředku virtuálního počítače se používá několik prvků profilu. Některé jsou povinné a některé jsou volitelné. Například prvky položka hardwareprofile, osProfile, storageProfile a networkProfile jsou požadovány, ale diagnosticsProfile je nepovinný. Tyto profily definují nastavení, jako například:
   
- [hodnota](../sizes.md)
- [název](/azure/architecture/best-practices/resource-naming) a přihlašovací údaje
- nastavení disku a [operačního systému](cli-ps-findimage.md)
- [síťové rozhraní](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- Diagnostika spouštění

## <a name="disks-and-images"></a>Disky a image
   
V Azure můžou soubory VHD představovat [disky nebo Image](../managed-disks-overview.md). Pokud je operační systém v souboru VHD specializovaný jako na konkrétní virtuální počítač, nazývá se to disk. Pokud je operační systém v souboru VHD zobecněný, aby se mohl vytvořit mnoho virtuálních počítačů, označuje se jako obrázek.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Vytvoření nových virtuálních počítačů a nových disků z image platformy

Když vytváříte virtuální počítač, musíte se rozhodnout, jaký operační systém se má použít. Element element imagereference se používá k definování operačního systému nového virtuálního počítače. V příkladu je uvedena definice operačního systému Windows Server:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Pokud chcete vytvořit operační systém Linux, můžete použít tuto definici:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Konfigurační nastavení disku operačního systému je přiřazeno k elementu osDisk. V příkladu je definován nový spravovaný disk s režimem **ukládání do mezipaměti** , který je nastaven na nepoužívatelné, a který disk vytváří z [Image platformy](cli-ps-findimage.md):

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Vytvořit nové virtuální počítače z existujících spravovaných disků

Pokud chcete vytvořit virtuální počítače z existujících disků, odeberte element imagereference a osProfile prvky a definujte tato nastavení disku:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Vytvoření nových virtuálních počítačů ze spravované image

Pokud chcete vytvořit virtuální počítač ze spravované image, změňte element element imagereference a definujte tato nastavení disku:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Připojit datové disky

Volitelně můžete přidat datové disky k virtuálním počítačům. [Počet disků](../sizes.md) závisí na velikosti disku operačního systému, který používáte. V případě, že je velikost virtuálních počítačů nastavená na Standard_DS1_v2, maximální počet datových disků, které se dají do nich přidat, je dva. V příkladu se do každého virtuálního počítače přidávají jeden spravovaný datový disk:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Rozšíření

I když jsou [rozšíření](../extensions/features-windows.md) samostatným prostředkem, jsou úzce svázána s virtuálními počítači. Rozšíření se dají přidat jako podřízený prostředek virtuálního počítače nebo jako samostatný prostředek. V příkladu se zobrazuje [rozšíření diagnostiky](../extensions/diagnostics-template.md) , které se přidává do virtuálních počítačů:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Tento prostředek rozšíření používá proměnnou úložiště a diagnostické proměnné k poskytnutí hodnot. Pokud chcete změnit data shromážděná tímto rozšířením, můžete přidat další čítače výkonu do proměnné wadperfcounters. Můžete také zvolit, aby diagnostická data byla vložena do jiného účtu úložiště, než kde jsou uloženy disky virtuálních počítačů.

Na virtuálním počítači můžete nainstalovat spoustu rozšíření, ale nejužitečnější je pravděpodobně [rozšíření vlastních skriptů](../extensions/custom-script-windows.md). V tomto příkladu se skript prostředí PowerShell s názvem start.ps1 při prvním spuštění spouští na každém virtuálním počítači:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Skript start.ps1 může provádět mnoho úloh konfigurace. Například datové disky, které jsou přidány do virtuálních počítačů v příkladu, nejsou inicializovány. k jejich inicializaci můžete použít vlastní skript. Pokud máte více úloh po spuštění, můžete použít soubor start.ps1 pro volání dalších skriptů PowerShellu v Azure Storage. V příkladu se používá PowerShell, ale můžete použít libovolnou metodu skriptování, která je k dispozici v operačním systému, který používáte.

Stav nainstalovaných rozšíření můžete zobrazit z nastavení rozšíření na portálu:

![Získat stav rozšíření](./media/template-description/virtual-machines-show-extensions.png)

Můžete taky získat informace o rozšíření pomocí příkazu PowerShellu **Get-AzVMExtension** , **rozšíření virtuálního počítače získat** příkaz Azure CLI nebo REST API **získat informace o rozšíření** .

## <a name="deployments"></a>Nasazení

Když nasadíte šablonu, Azure sleduje prostředky, které jste nasadili jako skupinu, a automaticky přiřadí název k této nasazené skupině. Název nasazení je stejný jako název šablony.

Pokud jste zajímá o stavu prostředků v nasazení, zobrazte skupinu prostředků v Azure Portal:

![Získat informace o nasazení](./media/template-description/virtual-machines-deployment-info.png)
    
Nejedná se o problém, jak použít stejnou šablonu k vytvoření prostředků nebo k aktualizaci existujících prostředků. Když použijete příkazy k nasazení šablon, budete mít možnost vyslovit, který [režim](../../azure-resource-manager/templates/deploy-powershell.md) chcete použít. Režim lze nastavit buď jako **dokončený** , nebo **přírůstkový**. Výchozím nastavením je přírůstkové aktualizace. Při použití **kompletního** režimu buďte opatrní, protože prostředky můžete omylem odstranit. Když nastavíte režim na **dokončeno**, správce prostředků odstraní všechny prostředky ve skupině prostředků, která není v šabloně.

## <a name="next-steps"></a>Další kroky

- Vytvoření vlastní šablony pomocí [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md).
- Nasaďte šablonu, kterou jste vytvořili, pomocí [šablony Správce prostředků vytvořit virtuální počítač s Windows](ps-template.md).
- Informace o tom, jak spravovat virtuální počítače, které jste vytvořili, najdete v tématu [Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md).
- Syntaxi a vlastnosti typů prostředků v šablonách JSON naleznete v tématu [Azure Resource Manager Reference k šabloně](/azure/templates/).