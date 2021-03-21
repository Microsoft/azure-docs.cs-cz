---
title: Vytvoření virtuálního počítače se systémem Linux pomocí REST API
description: Naučte se, jak vytvořit virtuální počítač se systémem Linux v Azure, který používá Managed Disks a ověřování SSH s Azure REST API.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 519939445e67f0f993662e2faf506eb186686156
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554560"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Vytvoření virtuálního počítače se systémem Linux, který používá ověřování pomocí protokolu SSH, s REST API

Virtuální počítač se systémem Linux v Azure se skládá z různých prostředků, jako jsou například disky a síťová rozhraní, a definuje parametry, jako je umístění, velikost a bitová kopie operačního systému a nastavení ověřování.

Virtuální počítač se systémem Linux můžete vytvořit prostřednictvím Azure Portal, Azure CLI 2,0, mnoha sad Azure SDK, šablon Azure Resource Manager a mnoha nástrojů třetích stran, jako je Ansible nebo Terraformu. Všechny tyto nástroje nakonec používají REST API k vytvoření virtuálního počítače se systémem Linux.

V tomto článku se dozvíte, jak pomocí REST API vytvořit virtuální počítač Linux se systémem Ubuntu 18,04-LTS se spravovanými disky a ověřováním SSH.

## <a name="before-you-start"></a>Než začnete

Než vytvoříte a odešlete žádost, budete potřebovat:

* `{subscription-id}`Pro vaše předplatné
  * Pokud máte více předplatných, přečtěte si téma [práce s více předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli) .
* `{resourceGroupName}`Vytvořili jste předem čas
* [Virtuální síťové rozhraní](../../virtual-network/virtual-network-network-interface.md) ve stejné skupině prostředků
* Pár klíčů SSH (můžete [vygenerovat nový](mac-create-ssh-keys.md) , pokud ho ještě nemáte)

## <a name="request-basics"></a>Základy požadavků

Chcete-li vytvořit nebo aktualizovat virtuální počítač, použijte následující operaci *vložení* :

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Kromě `{subscription-id}` parametrů a musíte `{resourceGroupName}` zadat `{vmName}` ( `api-version` je volitelný, ale tento článek byl testován pomocí `api-version=2017-12-01` ).

Jsou vyžadovány následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Content-Type:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Authorization:* | Povinná hodnota. Nastavte na platný [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Obecné informace o práci s požadavky na REST API najdete v tématu [komponenty REST API žádosti a odpovědi](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvoření textu žádosti

Následující běžné definice se používají k sestavení textu žádosti:

| Name                       | Požaduje se | Typ                                                                                | Description  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | řetězec                                                                              | Umístění prostředku. |
| name                       |          | řetězec                                                                              | Název virtuálního počítače |
| Properties. položka hardwareprofile |          | [Položka hardwareprofile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Určuje nastavení hardwaru pro virtuální počítač. |
| Properties. storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Určuje nastavení úložiště pro disky virtuálních počítačů. |
| Properties. osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Určuje nastavení operačního systému pro virtuální počítač. |
| Properties. networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Určuje síťová rozhraní virtuálního počítače. |

Příklad textu žádosti je uvedený níže. Ujistěte se, že jste zadali název virtuálního počítače `{computerName}` v `{name}` parametrech a, název síťového rozhraní, které jste vytvořili v části `networkInterfaces` , své uživatelské jméno v `adminUsername` a a `path` *veřejnou* část svého souboru KeyPair SSH (například `~/.ssh/id_rsa.pub` ) v `keyData` . Další parametry, které byste mohli chtít upravit, zahrnují `location` a `vmSize` .  

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

Úplný seznam dostupných definic v těle požadavku najdete v tématu [Definice textu žádosti o vytvoření nebo aktualizaci virtuálních počítačů](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Odesílá se žádost.

K odeslání tohoto požadavku HTTP můžete použít klienta vaší předvolby. Můžete také použít [Nástroj v prohlížeči](/rest/api/compute/virtualmachines/createorupdate) kliknutím na tlačítko **vyzkoušet** .

### <a name="responses"></a>Odpovědi

Existují dvě úspěšné odpovědi, které by mohla operace vytvořit nebo aktualizovat virtuální počítač:

| Název        | Typ                                                                              | Description |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 vytvořeno | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Vytvořeno     |

Zhuštěná *201 vytvořená* odpověď z předchozího ukázkového textu žádosti, který vytvoří virtuální počítač, zobrazuje *vmId* a *provisioningState* se *vytváří*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Další informace o odpovědích REST API najdete v tématu [zpracování zprávy s odpovědí](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních REST API Azure nebo jiných nástrojích pro správu, jako je Azure CLI nebo Azure PowerShell, najdete v následujících tématech:

- [REST API zprostředkovatele Azure COMPUTE](/rest/api/compute/)
- [Začínáme s Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Modul Azure PowerShellu](/powershell/azure/)
