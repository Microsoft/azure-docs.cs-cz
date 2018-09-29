---
title: Vytvoření virtuálního počítače s Linuxem pomocí rozhraní REST API služby Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem v Azure, která používá ověřování Managed Disks a SSH pomocí rozhraní Azure REST API.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 3eeaee9bc6320231f10aa85227e2f43756181806
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433476"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Vytvoření virtuálního počítače s Linuxem, které využívá ověřování SSH pomocí rozhraní REST API

Virtuální počítač s Linuxem (VM) v Azure se skládá z různých zdrojů, jako jsou disky a síťové rozhraní a definuje parametry, jako je například umístění, velikost a nastavení operačního systému image a ověřování.

Můžete vytvořit virtuální počítač s Linuxem pomocí webu Azure portal, Azure CLI 2.0, mnoho sad Azure SDK, šablon Azure Resource Manageru a celou řadu nástrojů třetích stran, jako jsou Ansible nebo Terraformu. Všechny tyto nástroje nakonec pomocí rozhraní REST API k vytvoření virtuálního počítače s Linuxem.

V tomto článku se dozvíte, jak používat rozhraní REST API k vytvoření virtuálního počítače s Linuxem, systémem Ubuntu 18.04-LTS pomocí spravovaných disků a ověřování SSH.

## <a name="before-you-start"></a>Než začnete

Před vytvořením a odešlete žádost, budete potřebovat:

* `{subscription-id}` Pro vaše předplatné
  * Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)
* A `{resourceGroupName}` jste předem vytvořili
* A [virtuální síťové rozhraní](../../virtual-network/virtual-network-network-interface.md) ve stejné skupině prostředků
* Pár klíčů SSH (můžete [vygenerovat nový token](mac-create-ssh-keys.md) pokud ho nemáte)

## <a name="request-basics"></a>Základní informace o požadavku

Chcete-li vytvořit nebo aktualizovat virtuální počítač, použijte následující *UMÍSTIT* operace:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Kromě `{subscription-id}` a `{resourceGroupName}` parametry, budete muset zadat `{vmName}` (`api-version` je volitelný, ale tento článek byl testován s `api-version=2017-12-01`)

Vyžadují se následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Typ obsahu:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Autorizace:* | Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Obecné informace o práci s požadavky rozhraní REST API najdete v tématu [součástí žádost/odpověď rozhraní REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

Následující běžné definice slouží k sestavení hlavní část žádosti:

| Název                       | Požaduje se | Typ                                                                                | Popis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | řetězec                                                                              | Umístění prostředku. |
| jméno                       |          | řetězec                                                                              | Název virtuálního počítače. |
| properties.hardwareProfile |          | [Položka HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Určuje nastavení hardwaru pro virtuální počítač. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Určuje nastavení úložiště pro disky virtuálních počítačů. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Určuje nastavení operačního systému pro virtuální počítač. |
| properties.networkProfile  |          | [Položky NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Určuje síťová rozhraní virtuálního počítače. |

Text požadavku příkladu jsou uvedeny níže. Je nutné zadat název virtuálního počítače v `{computerName}` a `{name}` parametry, název síťového rozhraní, které jste vytvořili v části `networkInterfaces`, vaše uživatelské jméno v `adminUsername` a `path`a *veřejného*část vašich klíčů SSH (například umístěný v `~/.ssh/id_rsa.pub`) v `keyData`. Zahrnout další parametry, můžete chtít upravit `location` a `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Úplný seznam dostupných definic v textu požadavku, najdete v části [virtuální počítače, vytvořit nebo aktualizovat definice těla požadavku](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Odesílání požadavku

Můžete použít klienta vaši volbu pro odeslání požadavku HTTP. Můžete také použít [nástroje v prohlížeči](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) kliknutím **vyzkoušet** tlačítko.

### <a name="responses"></a>Odezvy

Existují dva úspěšné odpovědi pro operaci vytvoření nebo aktualizaci virtuálního počítače:

| Název        | Typ                                                                              | Popis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 – vytvořeno | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Vytvořeno     |

Zhuštěnému *201 – vytvořeno* odpovědi z předchozího textu požadavku příklad, který vytvoří virtuální počítač zobrazuje *vmId* bylo přiřazeno a *provisioningState* je *Vytváření*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Další informace o rozhraní REST API odpovědi najdete v tématu [zpracování zprávy s odpovědí](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Další postup

Další informace o rozhraní REST API služby Azure nebo jiné nástroje pro správu, jako je například Azure Powershellu nebo rozhraní příkazového řádku Azure naleznete v následujících tématech:

- [Rozhraní REST API poskytovatele Azure Compute](/rest/api/compute/)
- [Začínáme s Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Modul Azure PowerShell](/powershell/azure/overview)
