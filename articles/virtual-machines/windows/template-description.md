---
title: Virtuální počítače v šabloně Azure Resource Manageru | Microsoft Azure
description: Další informace o definici prostředku virtuálního počítače v šabloně Azure Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: efd645faf3bf85f181b086fea8e55b0ab78be412
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412848"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuální počítače v šabloně Azure Resource Manageru

Tento článek popisuje aspekty šablony Azure Resource Manageru, které se vztahují k virtuálním počítačům. Tento článek nepopisuje úplnou šablonu při vytváření virtuálního počítače; k tomu potřebujete definice prostředku pro účty úložiště, síťová rozhraní, veřejné IP adresy a virtuální sítě. Další informace o tom, jak tyto prostředky je možné definovat společně, najdete v článku [názorný Průvodce šablonou Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Existuje několik instancí [šablon v galerii](https://azure.microsoft.com/documentation/templates/?term=VM) , které zahrnují prostředku virtuálního počítače. Ne všechny prvky, které mohou být součástí šablony jsou zde popsány.

Tento příklad ukazuje typické prostředků část o šablonu pro vytvoření zadaný počet virtuálních počítačů:

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
>Tento příklad využívá účet úložiště, který byl dříve vytvořen. Účet úložiště můžete vytvořit a nasadit ho z šablony. V příkladu rovněž závisí na síťové rozhraní a jeho závislé prostředky, které by v šabloně definované. Tyto prostředky se nezobrazují v příkladu.
>
>

## <a name="api-version"></a>Verze rozhraní API

Při nasazování prostředků pomocí šablony, budete muset zadat verzi rozhraní API pro použití. V příkladu prostředku virtuálního počítače pomocí elementu. Tato verze rozhraní API:

```
"apiVersion": "2016-04-30-preview",
```

Verze rozhraní API služby jste zadali v šabloně má vliv na vlastnosti, které definujete v šabloně. Obecně platí měli byste vybrat nejnovější verzi rozhraní API, při vytváření šablony. U existujících šablon můžete rozhodnout, jestli chcete dál používat starší verze rozhraní API, nebo aktualizaci šablony pro nejnovější verzi, abyste mohli využívat nové funkce.

Použijte tyto příležitosti, jak získat nejnovější verze rozhraní API:

- Rozhraní REST API – [seznamu všechny poskytovatele prostředků](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Azure CLI – [az provider show](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Parametry a proměnné

[Parametry](../../resource-group-authoring-templates.md) usnadňují zadejte hodnoty pro šablonu při každém spuštění. Tento oddíl parametrů se používá v tomto příkladu:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Když nasadíte příklad šablony, zadejte hodnoty pro jméno a heslo účtu správce na každý virtuální počítač a počet virtuálních počítačů k vytvoření. Máte možnost zadání hodnot parametrů v samostatném souboru, který je spravován pomocí šablony, nebo které uvádějí hodnoty po zobrazení výzvy.

[Proměnné](../../resource-group-authoring-templates.md) usnadňují nastavit hodnoty v šabloně se opakovaně používají v jeho nebo můžou časem změnit. Tento oddíl proměnné se používá v tomto příkladu:

```
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

Při nasazování Ukázková šablona hodnoty proměnné slouží pro název a identifikátor na dříve vytvořený účet úložiště. Proměnné se taky používají k poskytování nastavení diagnostického rozšíření. Použití [osvědčené postupy pro vytváření šablon Azure Resource Manageru](../../resource-manager-template-best-practices.md) vám pomohou rozhodnout, jak chcete struktury parametry a proměnné v šabloně.

## <a name="resource-loops"></a>Prostředek smyčky

Pokud potřebujete více než jeden virtuální počítač pro vaši aplikaci, můžete kopírovat element v šabloně. Tento volitelný prvek prochází vytváření počet virtuálních počítačů, které jste zadali jako parametr:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Všimněte si také, v příkladu, index smyčky se používá při zadání některé z hodnot pro prostředek. Pokud zadáte počet instancí tří, názvy disků operačního systému jsou například myOSDisk1 myOSDisk2 a myOSDisk3:

```
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

Mějte na paměti, který vytváří pro jeden prostředek smyčky v šabloně může vyžadovat použití smyčky při vytváření nebo přístup k dalším prostředkům. Například více virtuálních počítačů nelze použít stejném síťovém rozhraní, pokud vaše šablony prochází vytvoříte tři virtuální počítače musí také smyčka procesem vytvoření tři síťová rozhraní. Při přiřazení síťového rozhraní k virtuálnímu počítači, index smyčky se používá k identifikaci ho:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Závislosti

Většinu prostředků závisí na další zdroje informací a správně fungovat. Virtuální počítače musí být přidružená k virtuální síti a provedete, že potřebuje síťové rozhraní. [DependsOn](../../resource-group-define-dependencies.md) element slouží k Ujistěte se, že je připravená k použití, než virtuální počítače se vytvoří síťové rozhraní:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager nasadí současně všechny prostředky, které nejsou závislé na jiný prostředek nasazuje. Buďte opatrní při nastavení závislostí, protože vaše nasazení může zpomalit neúmyslně zadáním zbytečné závislosti. Závislosti můžete zřetězit prostřednictvím několika prostředků. Například síťové rozhraní závisí na veřejnou IP adresu a prostředky virtuální sítě.

Jak budete vědět, jestli je požadovaná závislost? Podívejte se na hodnoty, které jste nastavili v šabloně. Pokud prvek v bodech definice prostředku virtuálního počítače na jiný prostředek, který je nasazený do stejné šablony, musíte závislost. Například váš virtuální počítač příklad definuje profil sítě:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Pokud chcete nastavit tuto vlastnost, musí existovat síťové rozhraní. Proto musíte závislost. Musíte také nastavit závislosti, pokud jeden prostředek (podřízený) je definována v rámci jiného prostředku (nadřazené). Například nastavení diagnostiky a rozšíření vlastních skriptů jsou obě definovány jako podřízené prostředky virtuálního počítače. Nelze vytvořit dokud virtuální počítač existuje. Proto se oba prostředky se označí jako závislé na virtuálním počítači.

## <a name="profiles"></a>Profily

Několik prvků profil se používá při definování prostředku virtuálního počítače. Některé jsou vyžadovány a některé jsou volitelné. Například vyžadují se elementy – hardwareProfile, osProfile, storageProfile a položky networkProfile, ale diagnosticsProfile je volitelný. Tyto profily definují nastavení, jako například:
   
- [Velikost](sizes.md)
- [název](/azure/architecture/best-practices/naming-conventions) a přihlašovací údaje
- disk a [nastavení operačního systému](cli-ps-findimage.md)
- [Síťové rozhraní](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnostika spouštění

## <a name="disks-and-images"></a>Disky a Image
   
V Azure, může představovat soubory virtuálního pevného disku [disků nebo imagí](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Operační systém v souboru virtuálního pevného disku se specializuje na konkrétní virtuální počítač, to se označuje jako disk. Operační systém v souboru virtuálního pevného disku je zobecněný se použije k vytvoření mnoha virtuálních počítačů, ho se označuje jako obrázek.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Vytvoření nové virtuální počítače a nové disky, které z image platformy

Při vytváření virtuálního počítače, musíte rozhodnout, jaký operační systém. ImageReference element se používá k definování operačního systému z nového virtuálního počítače. Příklad ukazuje definici pro operační systém Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Pokud chcete vytvořit operačního systému Linux, můžete použít tuto definici:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Nastavení konfigurace pro disk s operačním systémem jsou přiřazeny s elementem osDisk. Příklad definuje nového spravovaného disku s ukládání do mezipaměti režim nastavený na **ReadWrite** a, který vytváří se disk z [image platformy](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Vytvoření nové virtuální počítače z existující spravované disky

Pokud chcete vytvořit virtuální počítače z existujícího disků, odeberte elementu imageReference a prvky osProfile a definovat toto nastavení disku:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Vytvoření nové virtuální počítače ze spravované image

Pokud chcete vytvořit virtuální počítač ze spravované image, změňte elementu imageReference a definovat toto nastavení disku:

```
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

Volitelně můžete přidat datové disky pro virtuální počítače. [Počet disků](sizes.md) závisí na velikosti disku s operačním systémem, který používáte. Maximální počet datových disků, které může být přidán na ně s velikostí virtuálních počítačů nastavte na Standard_DS1_v2 je dva. V tomto příkladu přidáte jednoho spravovaného datového disku pro každý virtuální počítač:

```
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

I když [rozšíření](extensions-features.md) jsou samostatný prostředek, jsou úzce vázané na virtuální počítače. Rozšíření můžete přidat jako podřízený prostředek virtuálního počítače nebo jako samostatný prostředek. Příklad ukazuje [diagnostické rozšíření](extensions-diagnostics-template.md) přidávaný do virtuálních počítačů:

```
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

Tento prostředek rozšíření používá proměnnou storageName a diagnostických proměnné k poskytnutí hodnot. Pokud chcete změnit data, která se shromažďují v tomto rozšíření, můžete přidat další čítače výkonu wadperfcounters proměnné. Můžete se také rozhodnout pro převedení diagnostická data do jiného účtu úložiště než ve kterém se ukládají disky virtuálních počítačů.

Existuje mnoho rozšíření, které můžete nainstalovat na virtuální počítač, ale je nejužitečnější pravděpodobně [rozšíření vlastních skriptů](extensions-customscript.md). V tomto příkladu spustí skript prostředí PowerShell s názvem start.ps1 na každý virtuální počítač při prvním spuštění:

```
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

Skript start.ps1 lze provádět mnoho úloh konfigurace. Například datové disky, které jsou přidány do virtuálních počítačů v příkladu nejsou inicializovány; můžete použít vlastní skript k jejich inicializaci. Pokud máte více úloh po spuštění udělat, můžete použít soubor start.ps1 volat jiné skripty prostředí PowerShell ve službě Azure storage. V příkladu Powershellu, ale můžete použít libovolný metoda, která je k dispozici v operačním systému, který používáte.

Můžete zobrazit stav nainstalovaná rozšíření z rozšíření nastavení na portálu:

![Načíst stav rozšíření](./media/template-description/virtual-machines-show-extensions.png)

Můžete také získat informace o rozšíření pomocí **Get-AzureRmVMExtension** příkazu Powershellu **get rozšíření virtuálního počítače** rozhraní příkazového řádku Azure, nebo **získat informace o rozšíření**Rozhraní REST API.

## <a name="deployments"></a>Nasazení

Při nasazení šablony Azure sleduje nasazený jako skupinu a automaticky přiřadí název této skupiny nasazené prostředky. Název nasazení je stejný jako název šablony.

Pokud vás to zajímá o stavu prostředků v nasazení, zobrazte si skupinu prostředků na webu Azure Portal:

![Získejte informace o nasazení](./media/template-description/virtual-machines-deployment-info.png)
    
To není problém používat stejnou šablonu k vytváření prostředků nebo aktualizovat stávající prostředky. Při nasazení šablon pomocí příkazů, máte možnost říct, která [režimu](../../resource-group-template-deploy.md) chcete použít. Režim může být nastaven na hodnotu **Complete** nebo **přírůstkové**. Ve výchozím nastavení je přírůstkové aktualizace. Buďte opatrní při použití **Complete** režimu vzhledem k tomu může omylem odstranit prostředky. Pokud nastavíte režim na **Complete**, odstraní všechny prostředky ve skupině prostředků, které nejsou v šabloně Resource Manageru.

## <a name="next-steps"></a>Další kroky

- Vytvořit vlastní šablonu pomocí [šablon pro vytváření Azure Resource Manageru](../../resource-group-authoring-templates.md).
- Nasadit šablonu, kterou jste vytvořili pomocí [vytvořit virtuální počítač s Windows pomocí šablony Resource Manageru](ps-template.md).
- Zjistěte, jak spravovat virtuální počítače, které jste vytvořili kontrolou [vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Syntaxi JSON a vlastnosti typů prostředků v šablonách najdete v tématu [referenčními informacemi k šablonám Azure Resource Manageru](/azure/templates/).
