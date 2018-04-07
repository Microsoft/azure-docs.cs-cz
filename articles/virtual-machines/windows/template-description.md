---
title: Virtuální počítače v šablonu Azure Resource Manager | Microsoft Azure
description: Další informace o tom, jak je definován prostředek virtuálního počítače v šablonu Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.openlocfilehash: 43cd6322bb03b5c781a890c3280247cbb2d118f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuální počítače v šablonu Azure Resource Manager

Tento článek popisuje aspekty šablony Azure Resource Manager, které se vztahují na virtuální počítače. Tento článek popisuje kompletní šablonu pro vytvoření virtuálního počítače; k tomu potřebujete definice prostředku pro účty úložiště, síťová rozhraní, veřejné IP adresy a virtuální sítě. Další informace o tom, jak tyto prostředky je možné definovat společně najdete v tématu [názorný Průvodce šablonou Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Existuje mnoho [šablony v galerii](https://azure.microsoft.com/documentation/templates/?term=VM) , zahrnout prostředků virtuálního počítače. Ne všechny elementy, které můžou být součástí šablony jsou popsané v tomto poli.

Tento příklad ukazuje oddíl typické prostředků šablony pro vytvoření zadaný počet virtuálních počítačů:

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
>Tento příklad spoléhá na účet úložiště, která byla dříve vytvořena. Můžete vytvořit účet úložiště po nasazení ze šablony. V příkladu se také závisí na rozhraní sítě a jeho závislé prostředky, které by definované v šabloně. Tyto prostředky se nezobrazí v příkladu.
>
>

## <a name="api-version"></a>Verze rozhraní API

Když nasadíte prostředky pomocí šablony, budete muset určit verzi rozhraní API používat. V příkladu prostředek virtuálního počítače pomocí tohoto elementu apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

Verze rozhraní API, které zadáte v šabloně ovlivňuje vlastnosti, které definujete v šabloně. Obecně byste měli vybrat nejnovější verzi rozhraní API, při vytváření šablony. Pro existující šablony můžete rozhodnout, jestli chcete pokračovat, pomocí dřívější verze rozhraní API nebo aktualizaci šablony na nejnovější verzi, abyste mohli využívat nové funkce.

Použijte tyto příležitosti pro získání nejnovější verze rozhraní API:

- Rozhraní API REST - [zobrazit seznam všech poskytovatelů prostředků](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell - [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Rozhraní příkazového řádku Azure 2.0 - [az zprostředkovatele zobrazit](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Parametry a proměnné

[Parametry](../../resource-group-authoring-templates.md) usnadní zadejte hodnoty pro šablonu, když jej spustíte. V příkladu se používá v této části Parametry:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Když nasadíte příklad šablony, zadejte hodnoty pro název a heslo účtu správce na každý virtuální počítač a počet virtuálních počítačů k vytvoření. Máte možnost zadání hodnot parametrů do samostatného souboru, který je spravován pomocí šablony, nebo zadáním hodnot po zobrazení výzvy.

[Proměnné](../../resource-group-authoring-templates.md) snadno nastavit hodnoty v šabloně jsou opakovaně použít v celé jeho nebo můžou časem změnit. V příkladu se používá v této části proměnné:

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

Při nasazování šablony Příklad hodnoty proměnné se používají pro název a identifikátor dříve vytvořený účet úložiště. Proměnné se taky používají k zadání nastavení pro rozšíření diagnostiky. Použití [osvědčené postupy pro vytváření šablon Azure Resource Manager](../../resource-manager-template-best-practices.md) vám pomohou rozhodnout, jak chcete struktury parametrů a proměnných ve vaší šabloně.

## <a name="resource-loops"></a>Smyčky prostředků

Pokud potřebujete více než jeden virtuální počítač pro vaši aplikaci, můžete použít element kopírování v šabloně. Tento volitelný element projde vytváření počet virtuálních počítačů, které jste zadali jako parametr:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Všimněte si také, v příkladu, index smyčky se používá při zadávání některé hodnoty pro prostředek. Například pokud jste zadali počet instancí tří, názvy disků operačního systému jsou myOSDisk1, myOSDisk2 a myOSDisk3:

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

Mějte na paměti, že vytváření smyčku pro jeden prostředek v šabloně, může vyžadovat použití smyčky při vytváření nebo přístup k dalším prostředkům. Například víc virtuálních počítačů nemůžou použít stejný síťového rozhraní, pokud vaše šablona projde vytváření tři virtuální počítače musí také cykly procesem vytvoření tři síťových rozhraní. Při přiřazování síťové rozhraní virtuálního počítače, index smyčky se používá k identifikaci:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Závislosti

Většina prostředky závisí na jiné prostředky fungovala správně. Virtuální počítače musí být přidružený k virtuální síti a k provádění potřebuje síťové rozhraní. [DependsOn](../../resource-group-define-dependencies.md) element se používá a ujistěte se, že síťové rozhraní je připravený k použití před vytvořením virtuálních počítačů:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Správce prostředků nasadí současně všechny prostředky, které nejsou závislé na jiný prostředek nasazuje. Buďte opatrní při nastavení závislostí, protože můžou nechtěně způsobit snížení nasazení tak, že zadáte nepotřebné závislosti. Závislosti můžete zřetězené prostřednictvím více prostředků. Síťové rozhraní, například závisí na veřejnou IP adresu a virtuální síťové prostředky.

Jak poznáte, pokud je třeba provést závislost? Podívejte se na hodnoty, které se nastavují v šabloně. Pokud element body definice prostředků virtuálního počítače na jiný prostředek, který je nasazen do stejné šablony, musíte závislost. Virtuální počítač příklad určují profil sítě:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Pokud chcete nastavit tuto vlastnost, musí existovat síťové rozhraní. Proto musíte závislost. Musíte taky nastavit závislost, když jeden prostředek (podřízená) je definována v rámci jiný prostředek (nadřazené). Například nastavení pro diagnostiku a rozšíření vlastních skriptů jsou obě definované jako podřízené prostředky virtuálního počítače. Nelze vytvořit dokud virtuální počítač existuje. Proto i prostředky jsou označené jako závislé na virtuálním počítači.

## <a name="profiles"></a>Profily

Několik elementy profil se používá při definování prostředek virtuálního počítače. Některé jsou vyžadovány a některé jsou volitelné. Například položka hardwareProfile, osProfile, storageProfile a networkProfile elementy jsou požadovány, ale diagnosticsProfile je volitelný. Tyto profily definovat nastavení, jako:
   
- [Velikost](sizes.md)
- [název](/architecture/best-practices/naming-conventions) a přihlašovací údaje
- disk a [nastavení operačního systému](cli-ps-findimage.md)
- [Síťové rozhraní](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnostika spouštění

## <a name="disks-and-images"></a>Disky a obrázků
   
V Azure, můžete soubory vhd představují [disky nebo bitové kopie](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud operační systém v souboru virtuálního pevného disku se specializuje na konkrétním virtuálním počítači, je jako disk uvedené. Pokud operační systém v souboru virtuálního pevného disku je zobecněn, který se má použít k vytvoření hodně virtuálních počítačů, je jako obrázek uvedené.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Vytvoření nové virtuální počítače a nové disky z image platformy

Když vytvoříte virtuální počítač, musíte rozhodnout, jaký operační systém používat. Element imageReference element se používá k definování operačního systému nový virtuální počítač. Příklad ukazuje definici pro operační systém Windows Server:

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

Nastavení konfigurace pro disk operačního systému jsou přiřazeny osDisk element. V příkladu definuje ukládání do mezipaměti režim nastavený na nový disk spravované **ReadWrite** a zda je disk se vytváří z [image platformy](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Vytvoření nové virtuální počítače z existujícího spravovaného disků

Pokud chcete vytvořit virtuální počítače z existujícího disků, odeberte elementu imageReference a osProfile elementy a definovat toto nastavení disku:

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

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Vytvoření nových virtuálních počítačů ze spravovaných bitové kopie

Pokud chcete vytvořit virtuální počítač z bitové kopie spravované, změňte element elementu imageReference a definovat toto nastavení disku:

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

### <a name="attach-data-disks"></a>Připojte datových disků

Volitelně můžete přidat datových disků do virtuálních počítačů. [Počet disků](sizes.md) závisí na velikosti disku operačního systému, který používáte. S velikostí virtuálních počítačů, nastavte na Standard_DS1_v2 je maximální počet datových disků, které nebylo možné přidat do je dva. V příkladu je přidáván jeden spravovaný datový disk pro každý virtuální počítač:

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

I když [rozšíření](extensions-features.md) jsou samostatné prostředků, úzce jsou svázané s virtuálními počítači. Rozšíření mohou být přidány jako podřízený prostředek virtuálního počítače nebo jako samostatnou prostředek. Příklad ukazuje [rozšíření diagnostiky](extensions-diagnostics-template.md) se přidává do virtuálních počítačů:

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

Tento prostředek rozšíření používá proměnnou storageName a diagnostiky proměnné zadat hodnoty. Pokud chcete změnit data, která se shromažďují v tomto rozšíření, můžete přidat další čítače výkonu wadperfcounters proměnné. Může se také rozhodnout put diagnostická data do jiného úložiště účtu než kde jsou uloženy disky virtuálních počítačů.

Existuje mnoho rozšíření, které můžete nainstalovat na virtuální počítač, ale je velmi užitečné pravděpodobně [rozšíření vlastních skriptů](extensions-customscript.md). V příkladu spustí skript prostředí PowerShell s názvem start.ps1 na každý virtuální počítač při prvním spuštění:

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

Skript start.ps1 lze provádět mnoho úkoly konfigurace. Například nejsou inicializovány datových disků, které jsou přidány do virtuálních počítačů v příkladu; můžete použít vlastní skript k chybě při inicializaci je. Pokud máte více spuštění úlohy uděláte, můžete použít soubor start.ps1 volat jiné skripty prostředí PowerShell v úložišti Azure. Tento příklad používá prostředí PowerShell, ale můžete použít libovolnou skriptování metodu, která je k dispozici v operačním systému, který používáte.

Stav nainstalovaného rozšíření z nastavení rozšíření na portálu můžete zobrazit:

![Načíst stav rozšíření](./media/template-description/virtual-machines-show-extensions.png)

Můžete také získat informace o rozšíření pomocí **Get-AzureRmVMExtension** příkazu Powershellu **get rozšíření virtuálního počítače** příkaz Azure CLI 2.0 nebo **získat informace o rozšíření** ROZHRANÍ REST API.

## <a name="deployments"></a>Nasazení

Při nasazení šablony Azure sleduje prostředky nasazené jako skupina a automaticky přiřadí název této skupiny nasazené. Název nasazení je stejný jako název šablony.

Pokud jste zvědaví o stavu prostředků v nasazení, můžete v okně skupiny prostředků na portálu Azure:

![Získat informace o nasazení](./media/template-description/virtual-machines-deployment-info.png)
    
Není problém používat stejné šablony vytvořit prostředky nebo aktualizovat existující prostředky. Při použití příkazů pro nasazení šablon, máte možnost k vyslovení který [režimu](../../resource-group-template-deploy.md) chcete použít. Režim může být nastaven na hodnotu **Complete** nebo **přírůstkové**. Ve výchozím nastavení se přírůstkové aktualizace. Buďte opatrní při používání **Complete** režimu vzhledem k tomu, že omylem může odstranit prostředky. Pokud nastavíte režim na **Complete**, odstraní všechny prostředky ve skupině prostředků, které nejsou v šabloně Resource Manager.

## <a name="next-steps"></a>Další kroky

- Vytvoření vlastní šablony pomocí [šablon pro tvorbu Azure Resource Manageru](../../resource-group-authoring-templates.md).
- Nasazení šablony, který jste vytvořili pomocí [vytvoření virtuálního počítače s Windows pomocí šablony Resource Manageru](ps-template.md).
- Zjistěte, jak spravovat virtuální počítače, které jste vytvořili kontrolou [vytvořit a spravovat virtuální počítače Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
