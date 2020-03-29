---
title: Rozšíření chef pro virtuální počítače Azure
description: Nasazení klienta chef do virtuálního počítače pomocí rozšíření virtuálního počítače chef.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76544714"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozšíření virtuálního počítače Chef pro Linux a Windows

Software Chef poskytuje automatizační platformu DevOps pro Linux a Windows, která umožňuje současnou správu fyzických i virtuálních serverových konfigurací. Rozšíření virtuálního počítače Chef je rozšíření, které umožňuje chef na virtuálních počítačích.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače Chef je podporované ve všech [rozšíření podporovaných operačních systémů](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) v Azure.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření virtuálního počítače Chef vyžaduje, aby cílový virtuální počítač je připojen k internetu za účelem načtení chef klienta datové části ze sítě pro doručování obsahu (CDN).  

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření virtuálního počítači chef. Rozšíření vyžaduje minimálně adresu URL serveru chef, název ověřovacího klienta a ověřovací klíč pro server chef; Tyto hodnoty lze nalézt `knife.rb` v souboru v starter-kit.zip, který je stažen při instalaci [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) nebo samostatný [Chef Server](https://downloads.chef.io/chef-server). Vzhledem k tomu, že ověřovací klíč by měl být považován za citlivá data, měl by být nakonfigurován pod elementem **protectedSettings,** což znamená, že bude dešifrovován pouze v cílovém virtuálním počítači.

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

### <a name="core-property-values"></a>Základní hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat
| ---- | ---- | ----
| apiVersion | `2017-12-01` | řetězec (datum) |
| vydavatel | `Chef.Bootstrap.WindowsAzure` | řetězec |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | řetězec |
| typeHandlerVersion | `1210.13` | řetězec (dvojitý) |

### <a name="settings"></a>Nastavení

| Name (Název) | Hodnota / Příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ----
| nastavení/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | řetězec (url) | Ano |
| nastavení/bootstrap_options/validation_client_name | `myorg-validator` | řetězec | Ano |
| nastavení/runlist | `recipe[mycookbook::default]` | řetězec | Ano |

### <a name="protected-settings"></a>Chráněná nastavení

| Name (Název) | Příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | řetězec | Ano |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Šablony lze použít k nasazení jednoho nebo více virtuálních počítačů, instalaci klienta Chef, připojení k chef serveru a provedení počáteční konfigurace na serveru, jak je definováno [v seznamu Run-list](https://docs.chef.io/run_lists.html)

Ukázka šablony Správce prostředků, která obsahuje rozšíření virtuálního počítače chef najdete v [galerii rychlého startu Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfigurace JSON pro rozšíření virtuálního počítače může být vnořena uvnitř prostředku virtuálního počítače nebo umístěna na kořenové nebo nejvyšší úrovni šablony JSON správce prostředků. Umístění konfigurace JSON ovlivňuje hodnotu názvu a typu prostředku. Další informace naleznete v tématu [Nastavení názvu a typu podřízených prostředků](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Azure CLI slouží k nasazení rozšíření virtuálního počítače chef na existující virtuální počítač. Nahraďte **validation_key** obsahem ověřovacího klíče (tento soubor jako příponu). `.pem`  Nahraďte **validation_client_name**, **chef_server_url** a `knife.rb` **run_list** těmito hodnotami ze souboru ve startovací soupravě.

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

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Výstup spuštění rozšíření je zaznamenán do následujícího souboru:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Chybové kódy a jejich význam

| Kód chyby | Význam | Možná akce |
| :---: | --- | --- |
| 51 | Toto rozšíření není podporováno v operačním systému virtuálního počítači. | |

Další informace o řešení potíží naleznete v [readme rozšíření virtuálního viceprezidenta chef](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Chcete-li cokoli jiného, co přímo souvisí s chef, obraťte se na [podporu šéfkuchaře](https://www.chef.io/support/).

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
