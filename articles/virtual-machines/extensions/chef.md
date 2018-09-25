---
title: Chef rozšíření pro virtuální počítače Azure | Dokumentace Microsoftu
description: Nasazení Chef klienta do virtuálního počítače pomocí Chefu rozšíření virtuálního počítače.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: cf151b4b72158253288a69911b55a2354947f9f4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47057889"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef rozšíření virtuálního počítače pro systémy Linux a Windows

Software Chef poskytuje automatizační platformu DevOps pro Linux a Windows, která umožňuje současnou správu fyzických i virtuálních serverových konfigurací. Rozšíření virtuálního počítače Chef je rozšíření, která umožňuje Chefu na virtuálních počítačích.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření virtuálního počítače Chef se podporuje na všech [rozšíření nepodporuje OS](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) v Azure.

### <a name="internet-connectivity"></a>Připojení k internetu

Chef rozšíření virtuálního počítače vyžaduje, aby cílový virtuální počítač je připojený k Internetu získat datovou část Chef klienta v síti pro doručování obsahu (CDN).  

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schématu pro rozšíření virtuálního počítače Chef. Rozšíření vyžaduje minimálně adresu URL serveru Chef, název ověření klienta a ověřovací klíč pro Chef serveru. Tyto hodnoty můžete najít v `knife.rb` souboru v kit.zip starter, který se stáhne při instalaci [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) nebo samostatný [Chef serveru](https://downloads.chef.io/chef-server). Protože ověřovací klíč by měl být považován za citlivá data, by měla být nakonfigurovaná v části **protectedSettings** elementu, což znamená, že ho bude dešifrovat jenom na cílovém virtuálním počítači.

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
    "typeHandlerVersion": "1210.12",
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

### <a name="core-property-values"></a>Hodnoty vlastností Core

| Název | Hodnota / příklad | Typ dat
| ---- | ---- | ---- | ----
| apiVersion | `2017-12-01` | řetězec (date) |
| vydavatele | `Chef.Bootstrap.WindowsAzure` | řetězec |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | řetězec |
| typeHandlerVersion | `1210.12` | řetězec (double) |

### <a name="settings"></a>Nastavení

| Název | Hodnota / příklad | Typ dat | Povinné?
| ---- | ---- | ---- | ----
| nastavení/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | řetězec (url) | Ano |
| nastavení/bootstrap_options/validation_client_name | `myorg-validator` | řetězec | Ano |
| nastavení nebo poradci | `recipe[mycookbook::default]` | řetězec | Ano |

### <a name="protected-settings"></a>Chráněné nastavení

| Název | Příklad: | Typ dat | Povinné?
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

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony lze nasadit jeden nebo více virtuálních počítačů, nainstalovat klienta Chef, připojit k serveru Chef a provést počáteční konfiguraci serveru podle definice [seznam spuštění](https://docs.chef.io/run_lists.html)

Ukázka šablony Resource Manageru, která zahrnuje Chef rozšíření virtuálního počítače můžete najít na [Galerie Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON konfigurace pro rozšíření virtuálního počítače můžete vnořit do prostředku virtuálního počítače nebo objektu umístěn na kořenový server WSUS nebo nejvyšší úrovni šablony JSON Resource Manageru. Umístění konfigurace JSON má vliv na hodnotu názvu prostředku a typů. Další informace najdete v tématu [nastavte název a typ pro podřízené prostředky](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Azure CLI slouží k nasazení Chef rozšíření virtuálního počítače do existujícího virtuálního počítače. Nahradit **validation_key** s obsahem ověřovací klíč (Tento soubor jako `.pem` rozšíření).  Nahraďte **validation_client_name**, **chef_server_url** a **run_list** s těmito hodnotami z `knife.rb` souboru v Starter Kit.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
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
| 51 | Toto rozšíření není podporován v operačním systému Virtuálního počítače | |

Další informace o odstraňování potíží najdete v [readme rozšíření virtuálního počítače Chef](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Další postup

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
