---
title: Rozšíření pro Azure pro virtuální počítače Azure
description: Nasaďte do virtuálního počítače klienta systému pro nasazení pomocí rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544714"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozšíření VM VM pro Linux a Windows

Software Chef poskytuje automatizační platformu DevOps pro Linux a Windows, která umožňuje současnou správu fyzických i virtuálních serverových konfigurací. Rozšíření VM VM je rozšíření, které umožňuje pro virtuální počítače službu pro vypínání.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače pro systém saďte je podporované ve všech [podporovaných operačních systémech pro rozšíření](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) v Azure.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření virtuálních počítačů pro virtuální počítače vyžaduje, aby byl cílový virtuální počítač připojený k Internetu, aby bylo možné načíst datovou část klienta v rámci služby Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma rozšíření virtuálního počítače pro rozhraní. Přípona vyžaduje minimálně adresu URL serveru pro název klienta, název ověřovacího klienta a ověřovací klíč pro server s názvem. Tyto hodnoty najdete v souboru `knife.rb` ve formátu Starter-Kit. zip, který se stáhne při instalaci nástroje pro [automatizaci](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) nebo samostatného [serveru](https://downloads.chef.io/chef-server). Vzhledem k tomu, že ověřovací klíč by měl být považován za citlivá data, měl by být nakonfigurován v rámci elementu **protectedSettings** , což znamená, že bude pouze dešifrován v cílovém virtuálním počítači.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Hodnoty základních vlastností

| Name (Název) | Hodnota / příklad | Typ dat
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.13` | string (double) |

### <a name="settings"></a>Nastavení

| Name (Název) | Hodnota / příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | Ano |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Ano |
| settings/runlist | `recipe[mycookbook::default]` | string | Ano |

### <a name="protected-settings"></a>Chráněná nastavení

| Name (Název) | Příklad: | Typ dat | Povinné?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Ano |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony lze použít k nasazení jednoho nebo více virtuálních počítačů, instalaci klienta systému saďte, připojení k serveru systému Deploy a provedení počáteční konfigurace na serveru, jak je definováno v [seznamu spuštění](https://docs.chef.io/run_lists.html) .

Ukázková šablona Správce prostředků, která obsahuje rozšíření VM VM, najdete v [galerii Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření virtuálních počítačů s virtuálním počítačem do existujícího virtuálního počítače. Nahraďte **validation_key** obsahem ověřovacího klíče (Tento soubor je rozšířením `.pem`).  Hodnoty **validation_client_name**, **chef_server_url** a **run_list** nahraďte z `knife.rb` souboru v úvodní sadě.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich význam

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 51 | Toto rozšíření není v operačním systému virtuálního počítače podporované. | |

Další informace o řešení potíží najdete v [souboru Readme s rozšířením virtuálního počítače sady virtuálních počítačů](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> U cokoli jiného, co se přímo týká, kontaktujte [podporu](https://www.chef.io/support/)pro výrobce.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
