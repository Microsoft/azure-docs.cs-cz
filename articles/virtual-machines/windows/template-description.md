---
title: Virtuální počítače v šabloně Azure Resource Manager | Microsoft Azure
description: Další informace o tom, jak je prostředek virtuálního počítače definován v šabloně Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: c9bf1cf0564655c932e066e5b74225382375e9c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235420"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuální počítače v šabloně Azure Resource Manager

Tento článek popisuje aspekty šablony Azure Resource Manager, které se vztahují na virtuální počítače. Tento článek nepopisuje úplnou šablonu pro vytvoření virtuálního počítače; pro které potřebujete definice prostředků pro účty úložiště, síťová rozhraní, veřejné IP adresy a virtuální sítě. Další informace o tom, jak lze tyto prostředky společně definovat, naleznete v [návodu k šabloně Správce prostředků](../../azure-resource-manager/resource-manager-template-walkthrough.md).

V galerii je mnoho [šablon,](https://azure.microsoft.com/documentation/templates/?term=VM) které obsahují prostředek virtuálního počítače. Ne všechny prvky, které mohou být zahrnuty do šablony jsou popsány zde.

 

Tento příklad ukazuje typickou část prostředků šablony pro vytvoření zadaného počtu virtuálních počítače:

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
>Tento příklad závisí na účtu úložiště, který byl dříve vytvořen. Účet úložiště můžete vytvořit nasazením ze šablony. Příklad také závisí na síťovérozhraní a jeho závislé prostředky, které by byly definovány v šabloně. Tyto prostředky nejsou uvedeny v příkladu.
>
>

## <a name="api-version"></a>Verze rozhraní API

Při nasazování prostředků pomocí šablony, budete muset zadat verzi rozhraní API použít. Příklad ukazuje prostředek virtuálního počítače pomocí tohoto elementu apiVersion:

```json
"apiVersion": "2016-04-30-preview",
```

Verze rozhraní API, kterou zadáte v šabloně, ovlivní vlastnosti, které můžete v šabloně definovat. Obecně byste měli při vytváření šablon vybrat nejnovější verzi rozhraní API. U existujících šablon se můžete rozhodnout, zda chcete pokračovat v používání starší verze rozhraní API, nebo aktualizovat šablonu pro nejnovější verzi, abyste mohli využívat nové funkce.

Využijte tyto příležitosti k získání nejnovějších verzí rozhraní API:

- ROZHRANÍ REST API – [seznam všech poskytovatelů prostředků](https://docs.microsoft.com/rest/api/resources/providers)
- Prostředí PowerShell – [get-azresourceprovider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI – [show zprostředkovatele az](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametry a proměnné

[Parametry](../../resource-group-authoring-templates.md) usnadňují zadání hodnot pro šablonu při jeho spuštění. Tato část parametrů se používá v příkladu:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Při nasazení ukázkové šablony zadáte hodnoty pro název a heslo účtu správce na každém virtuálním počítači a počet virtuálních počítačů, které chcete vytvořit. Máte možnost zadat hodnoty parametrů v samostatném souboru, který je spravován pomocí šablony, nebo zadat hodnoty po zobrazení výzvy.

[Proměnné](../../resource-group-authoring-templates.md) usnadňují nastavení hodnot v šabloně, které se v ní opakovaně používají nebo které se mohou v průběhu času měnit. Tato část proměnných se používá v příkladu:

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

Při nasazení ukázkové šablony se hodnoty proměnných používají pro název a identifikátor dříve vytvořeného účtu úložiště. Proměnné se také používají k poskytnutí nastavení pro rozšíření diagnostiky. Pomocí [osvědčených postupů pro vytváření šablon Azure Resource Manager](../../resource-manager-template-best-practices.md) uděláte s vámi při rozhodování, jak chcete strukturovat parametry a proměnné v šabloně.

## <a name="resource-loops"></a>Smyčky zdrojů

Když potřebujete více než jeden virtuální počítač pro vaši aplikaci, můžete použít prvek kopírování v šabloně. Tento volitelný prvek smyčky prostřednictvím vytvoření počtu virtuálních stránek, které jste zadali jako parametr:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Všimněte si také v příkladu, že index smyčky se používá při zadávání některé hodnoty pro prostředek. Pokud jste například zadali počet instancí tři, názvy disků operačního systému jsou myOSDisk1, myOSDisk2 a myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Tento příklad používá spravované disky pro virtuální počítače.
>
>

Mějte na paměti, že vytvoření smyčky pro jeden prostředek v šabloně může vyžadovat použití smyčky při vytváření nebo přístupu k jiným prostředkům. Například více virtuálních počítačů nelze použít stejné síťové rozhraní, takže pokud vaše šablona smyčky prostřednictvím vytvoření tří virtuálních počítačů musí také smyčku prostřednictvím vytváření tří síťových rozhraní. Při přiřazování síťového rozhraní k virtuálnímu virtuálnímu soudu se k jeho identifikaci používá index smyčky:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Závislosti

Většina prostředků závisí na jiných prostředcích, aby fungovaly správně. Virtuální počítače musí být přidruženy k virtuální síti a k tomu, že potřebuje síťové rozhraní. Element [dependsOn](../../resource-group-define-dependencies.md) se používá k ujistěte se, že síťové rozhraní je připraven k použití před vytvořením virtuálních počítačů:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Správce prostředků nasazuje paralelně všechny prostředky, které nejsou závislé na jiném prostředku, který se nasazuje. Při nastavování závislostí buďte opatrní, protože můžete nechtěně zpomalit nasazení zadáním zbytečných závislostí. Závislosti lze zřetězit prostřednictvím více prostředků. Síťové rozhraní například závisí na veřejné IP adrese a prostředcích virtuální sítě.

Jak poznáte, zda je vyžadována závislost? Podívejte se na hodnoty, které jste nastavili v šabloně. Pokud prvek v definici prostředku virtuálního počítače odkazuje na jiný prostředek, který je nasazený ve stejné šabloně, potřebujete závislost. Například váš příklad virtuálního počítače definuje profil sítě:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Chcete-li nastavit tuto vlastnost, musí existovat síťové rozhraní. Proto potřebujete závislost. Je také nutné nastavit závislost, pokud je jeden prostředek (podřízený) definován v rámci jiného prostředku (nadřazeného). Například nastavení diagnostiky a rozšíření vlastních skriptů jsou definovány jako podřízené prostředky virtuálního počítače. Nelze je vytvořit, dokud virtuální počítač neexistuje. Proto jsou oba prostředky označeny jako závislé na virtuálním počítači.

## <a name="profiles"></a>Profily

Několik prvků profilu se používá při definování prostředku virtuálního počítače. Některé jsou povinné a některé jsou volitelné. Například hardwareProfile, osProfile, storageProfile a networkProfile prvky jsou povinné, ale diagnosticsProfile je volitelné. Tyto profily definují nastavení, například:
   
- [Velikost](sizes.md)
- [jméno](/azure/architecture/best-practices/resource-naming) a pověření
- nastavení disku a [operačního systému](cli-ps-findimage.md)
- [síťové rozhraní](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnostika spouštění

## <a name="disks-and-images"></a>Disky a obrázky
   
V Azure mohou soubory vhd představovat [disky nebo bitové kopie](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Když se operační systém v souboru vhd specializuje na konkrétní virtuální počítače, označuje se jako disk. Když je operační systém v souboru vhd zobecněn tak, aby se použil k vytvoření mnoha virtuálních<a), označuje se jako bitová kopie.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Vytváření nových virtuálních počítačů a nových disků z bitové kopie platformy

Při vytváření virtuálního virtuálního masivu se musíte rozhodnout, jaký operační systém použít. Element imageReference se používá k definování operačního systému nového virtuálního soudu. Příklad ukazuje definici operačního systému Windows Server:

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

Nastavení konfigurace disku operačního systému je přiřazeno k prvku osDisk. Příklad definuje nový spravovaný disk s režimem ukládání do mezipaměti nastaveným na **ReadWrite** a tím, že je vytvářen z [bitové kopie platformy](cli-ps-findimage.md):

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Vytvoření nových virtuálních počítačů ze stávajících spravovaných disků

Pokud chcete vytvořit virtuální počítače z existujících disků, odeberte elementy imageReference a osProfile a definujte tato nastavení disku:

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

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Vytvoření nových virtuálních počítačů ze spravované bitové kopie

Pokud chcete vytvořit virtuální počítač ze spravované bitové kopie, změňte element imageReference a definujte tato nastavení disku:

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

### <a name="attach-data-disks"></a>Připojení datových disků

Volitelně můžete přidat datové disky do virtuálních počítačů. [Počet disků](sizes.md) závisí na velikosti disku operačního systému, který používáte. S velikostí virtuálních počítačů nastavena na Standard_DS1_v2, maximální počet datových disků, které by mohly být přidány do nich je dva. V příkladu se do každého virtuálního počítače přidává jeden spravovaný datový disk:

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

I když [rozšíření](extensions-features.md) jsou samostatný prostředek, jsou úzce souvisí s virtuálními ms. Rozšíření lze přidat jako podřízený prostředek virtuálního soudu nebo jako samostatný prostředek. Příklad ukazuje [rozšíření diagnostiky,](extensions-diagnostics-template.md) které se přidává do virtuálních discích:

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

Tento prostředek rozšíření používá proměnnou storageName a diagnostické proměnné k poskytnutí hodnot. Pokud chcete změnit data shromážděná tímto rozšířením, můžete přidat další čítače výkonu do proměnné wadperfcounters. Můžete také zvolit, zda chcete diagnostická data umístit do jiného účtu úložiště, než kde jsou uloženy disky virtuálního počítače.

Existuje mnoho rozšíření, které můžete nainstalovat na virtuální počítač, ale nejužitečnější je pravděpodobně [rozšíření vlastní skript](extensions-customscript.md). V tomto příkladu se skript prostředí PowerShell s názvem start.ps1 spustí na každém virtuálním počítači při prvním spuštění:

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

Skript start.ps1 může provádět mnoho konfiguračních úloh. Například datové disky, které jsou přidány do virtuálních počítačů v příkladu nejsou inicializovány; k inicializaci můžete použít vlastní skript. Pokud máte k práci víc úloh při spuštění, můžete pomocí souboru start.ps1 volat jiné skripty PowerShellu v úložišti Azure. Příklad používá prostředí PowerShell, ale můžete použít libovolnou metodu skriptování, která je k dispozici v operačním systému, který používáte.

Stav nainstalovaných rozšíření najdete v nastavení rozšíření na portálu:

![Získat stav rozšíření](./media/template-description/virtual-machines-show-extensions.png)

Informace o rozšíření můžete také získat pomocí příkazu **Get-AzVMExtension** PowerShell, **příkazu získat rozhraní příkazu** Azure CLI v rozšíření nebo **získat informace o rozšíření** REST API.

## <a name="deployments"></a>Nasazení

Když nasadíte šablonu, Azure sleduje prostředky, které jste nasadili jako skupinu a automaticky přiřadí název této nasazené skupině. Název nasazení je stejný jako název šablony.

Pokud vás zajímá stav prostředků v nasazení, podívejte se na skupinu prostředků na webu Azure Portal:

![Získání informací o nasazení](./media/template-description/virtual-machines-deployment-info.png)
    
Není problém použít stejnou šablonu k vytvoření prostředků nebo k aktualizaci existujících prostředků. Při použití příkazů k nasazení šablon máte možnost říci, který [režim](../../resource-group-template-deploy.md) chcete použít. Režim lze nastavit na **úplné** nebo **přírůstkové**. Ve výchozím nastavení je provést přírůstkové aktualizace. Při použití režimu **Dokončení** buďte opatrní, protože můžete omylem odstranit prostředky. Pokud nastavíte režim na **Dokončení**, Správce prostředků odstraní všechny prostředky ve skupině prostředků, které nejsou v šabloně.

## <a name="next-steps"></a>Další kroky

- Vytvořte si vlastní šablonu pomocí [vytváření šablon Azure Resource Manager .](../../resource-group-authoring-templates.md)
- Nasazení šablony, kterou jste vytvořili [pomocí vytvoření virtuálního počítače s Windows pomocí šablony Správce prostředků](ps-template.md).
- Zjistěte, jak spravovat virtuální počítače, které jste vytvořili, najdete v recenzi [na vytvoření a správu virtuálních počítačích s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Syntaxe JSON a vlastnosti typů prostředků v šablonách najdete v [tématu odkaz na šablonu Správce prostředků Azure](/azure/templates/).
