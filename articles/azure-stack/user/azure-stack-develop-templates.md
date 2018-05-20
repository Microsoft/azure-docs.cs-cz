---
title: Vývoj šablon pro Azure zásobníku | Microsoft Docs
description: Další informace zásobníku Azure šablony osvědčené postupy
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 046866d9ed7ce65e3b46be1c67b4ab2058cefa4d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-resource-manager-template-considerations"></a>Aspekty šablon Azure Resource Manager

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Když budete vyvíjet aplikace, je důležité zajistit šablony přenositelnost mezi Azure a Azure zásobníku. Tento článek obsahuje důležité informace týkající se vývoje Azure Resource Manager [šablony](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), tak, aby se prototypu nasazení vaší aplikace a testování v Azure bez přístupu do prostředí Azure zásobníku.

## <a name="resource-provider-availability"></a>Dostupnost zprostředkovatele prostředků

Šablony, která se chystáte nasadit musí používat pouze služby Microsoft Azure, které jsou již k dispozici nebo ve verzi preview v zásobníku Azure.

## <a name="public-namespaces"></a>Veřejné obory názvů

Protože zásobník Azure je hostované ve vašem datovém centru, má jinou službu, pro koncový bod obory názvů než veřejného cloudu Azure. V důsledku toho pevně zakódované veřejné koncové body v šablonách Azure Resource Manager selhání při pokusu o jejich nasazení do Azure zásobníku. Můžete vytvořit dynamicky koncové body služby pomocí *odkaz* a *řetězení* funkce k načtení hodnoty od zprostředkovatele prostředků během nasazení. Například místo hardcoding *blob.core.windows.net* v šabloně, načíst [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) dynamicky nastavit *osDisk.URI* koncový bod:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Správa verzí API

Verze služby Azure se můžou lišit mezi Azure a Azure zásobníku. Každý prostředek vyžaduje **apiVersion** atributu, který definuje možnosti nabízí. Aby se zajistila Kompatibilita verze rozhraní API v Azure zásobníku, jsou platné pro každý poskytovatel prostředků následující verze rozhraní API:

| Poskytovatel prostředků | apiVersion |
| --- | --- |
| Compute |`'2015-06-15'` |
| Síť |`'2015-06-15'`, `'2015-05-01-preview'` |
| Úložiště |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Funkce šablon

Azure Resource Manager [funkce](../../azure-resource-manager/resource-group-template-functions.md) nabízí možnosti, které jsou potřebné k vytvoření dynamické šablony. Například můžete použít funkce pro úlohy, jako je:

* Zřetězení nebo ořezávání řetězce.
* Odkazování na hodnoty z jiné prostředky.
* Iterace na prostředky pro nasazení více instancí.

Tyto funkce nejsou dostupné v Azure zásobníku:

* Přeskočit
* proveďte

## <a name="resource-location"></a>Umístění prostředku

Šablony Azure Resource Manageru použijte atribut umístění umístit prostředkům během nasazení. V Azure najdete v umístění pro oblast západní USA nebo Jižní Amerika. V zásobníku Azure se liší umístění, protože zásobník Azure je ve vašem datovém centru. Aby byly šablony přenositelné mezi Azure a Azure zásobníku, by měl odkazovat umístění skupiny prostředků jako nasazení jednotlivých zdrojů. To provedete pomocí `[resourceGroup().Location]` zajistit všechny prostředky dědění umístění skupiny prostředků. Následující výpis je příklad použití této funkce při nasazení účtu úložiště:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí rozhraní příkazového řádku Azure](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
