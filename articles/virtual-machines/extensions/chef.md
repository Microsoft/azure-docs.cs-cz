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
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: 8977563c6b19754eda53686baf85f840a7583e77
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968362"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozšíření VM VM pro Linux a Windows

Software Chef poskytuje automatizační platformu DevOps pro Linux a Windows, která umožňuje současnou správu fyzických i virtuálních serverových konfigurací. Rozšíření VM VM je rozšíření, které umožňuje pro virtuální počítače službu pro vypínání.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače pro systém saďte je podporované ve všech [podporovaných operačních systémech pro rozšíření](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) v Azure.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření virtuálních počítačů pro virtuální počítače vyžaduje, aby byl cílový virtuální počítač připojený k Internetu, aby bylo možné načíst datovou část klienta v rámci služby Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma rozšíření virtuálního počítače pro rozhraní. Přípona vyžaduje minimálně adresu URL serveru pro název klienta, název ověřovacího klienta a ověřovací klíč pro server s názvem. Tyto hodnoty najdete v `knife.rb` souboru ve starter-kit.zip, který se stáhne při instalaci nástroje pro [automatizaci](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) nebo samostatného [serveru](https://downloads.chef.io/chef-server). Vzhledem k tomu, že ověřovací klíč by měl být považován za citlivá data, měl by být nakonfigurován v rámci elementu **protectedSettings** , což znamená, že bude pouze dešifrován v cílovém virtuálním počítači.

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

| Name | Hodnota/příklad | Typ dat
| ---- | ---- | ----
| apiVersion | `2017-12-01` | řetězec (datum) |
| vydavatel | `Chef.Bootstrap.WindowsAzure` | řetězec |
| typ | `LinuxChefClient` (Linux), `ChefClient` (Windows) | řetězec |
| typeHandlerVersion | `1210.13` | řetězec (Double) |

### <a name="settings"></a>Nastavení

| Name | Hodnota/příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ----
| nastavení/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | řetězec (URL) | Y |
| nastavení/bootstrap_options/validation_client_name | `myorg-validator` | řetězec | Y |
| nastavení/runlist | `recipe[mycookbook::default]` | řetězec | Y |

### <a name="protected-settings"></a>Chráněná nastavení

| Name | Příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | řetězec | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony lze použít k nasazení jednoho nebo více virtuálních počítačů, instalaci klienta systému saďte, připojení k serveru systému Deploy a provedení počáteční konfigurace na serveru, jak je definováno v [seznamu spuštění](https://docs.chef.io/run_lists.html) .

Ukázková šablona Správce prostředků, která obsahuje rozšíření VM VM, najdete v [galerii Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfiguraci JSON pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo umístit na kořenovou nebo nejvyšší úroveň šablony Správce prostředků JSON. Umístění konfigurace JSON má vliv na hodnotu názvu a typu prostředku. Další informace najdete v tématu [Nastavení názvu a typu pro podřízené prostředky](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření virtuálních počítačů s virtuálním počítačem do existujícího virtuálního počítače. Nahraďte **validation_key** obsahem vašeho ověřovacího klíče (Tento soubor jako `.pem` rozšíření).  Hodnoty **validation_client_name**, **chef_server_url** a **run_list** nahraďte hodnotami ze `knife.rb` souboru v úvodní sadě.

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

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Výstup spuštění rozšíření se zaznamená do následujícího souboru:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významy

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 51 | Toto rozšíření není v operačním systému virtuálního počítače podporované. | |

Další informace o řešení potíží najdete v [souboru Readme s rozšířením virtuálního počítače sady virtuálních počítačů](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> U cokoli jiného, co se přímo týká, kontaktujte [podporu](https://www.chef.io/support/)pro výrobce.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
