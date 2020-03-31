---
title: Vytvoření virtuálního počítače s Linuxem pomocí rozhraní REST API
description: Zjistěte, jak vytvořit virtuální počítač Linuxu v Azure, který používá spravované disky a ověřování SSH pomocí rozhraní Azure REST API.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970277"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Vytvoření virtuálního počítače s Linuxem, který používá ověřování SSH pomocí rozhraní REST API

Virtuální počítač (VM) linuxového počítače (VM) v Azure se skládá z různých prostředků, jako jsou disky a síťová rozhraní a definuje parametry, jako je umístění, velikost a nastavení image operačního systému a ověřování.

Virtuální počítač s Linuxem můžete vytvořit prostřednictvím portálu Azure, Azure CLI 2.0, mnoha sad Azure SDK, šablon Azure Resource Manager a mnoha nástrojů třetích stran, jako je Ansible nebo Terraform. Všechny tyto nástroje nakonec použít rozhraní REST API k vytvoření virtuálního počítače s Linuxem.

Tento článek ukazuje, jak pomocí rozhraní REST API vytvořit virtuální počítač s Linuxem se systémem Ubuntu 18.04-LTS se spravovanými disky a ověřováním SSH.

## <a name="before-you-start"></a>Než začnete

Před vytvořením a odesláním žádosti budete potřebovat:

* Pro `{subscription-id}` vaše předplatné
  * Pokud máte více předplatných, přečtěte si informace [o práci s více předplatnými.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A, `{resourceGroupName}` kterou jste vytvořili dopředu
* [Rozhraní virtuální sítě](../../virtual-network/virtual-network-network-interface.md) ve stejné skupině prostředků
* Dvojice klíčů SSH (můžete [vygenerovat nový,](mac-create-ssh-keys.md) pokud ho nemáte)

## <a name="request-basics"></a>Základy žádosti

Chcete-li vytvořit nebo aktualizovat virtuální počítač, použijte následující operaci *PUT:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Kromě parametrů `{subscription-id}` a `{resourceGroupName}` budete muset zadat `{vmName}` (`api-version` je volitelný, ale tento článek byl `api-version=2017-12-01`testován s )

Jsou vyžadovány následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Content-Type:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Authorization:* | Povinná hodnota. Nastavte na platný  [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |

Obecné informace o práci s požadavky rozhraní REST API naleznete [v tématu Součásti požadavku/odpovědi rozhraní REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvoření těla požadavku

K vytvoření těla požadavku se používají následující běžné definice:

| Name (Název)                       | Požaduje se | Typ                                                                                | Popis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | řetězec                                                                              | Umístění zdroje. |
| jméno                       |          | řetězec                                                                              | Název virtuálního počítače. |
| vlastnosti.hardwareProfil |          | [HardwareProfil](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Určuje nastavení hardwaru pro virtuální počítač. |
| properties.storageProfil  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Určuje nastavení úložiště disků virtuálních počítačů. |
| properties.osProfil       |          | [OSProfil](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Určuje nastavení operačního systému pro virtuální počítač. |
| properties.networkProfile  |          | [Profil sítě](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Určuje síťová rozhraní virtuálního počítače. |

Příklad tělo požadavku je níže. Ujistěte se, že jste `{computerName}` v `{name}` parametrech a zadali název virtuálního `networkInterfaces`počítače, název `adminUsername` síťového rozhraní, které jste vytvořili v části , uživatelské jméno v aplikaci `path`a a *veřejnou* část dvojice klíčů SSH (například umístěné `~/.ssh/id_rsa.pub`v ) v aplikaci `keyData`. Mezi další parametry, které `location` `vmSize`můžete chtít upravit, patří a .  

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

Úplný seznam dostupných definic v těle požadavku naleznete v tématu [Virtuální počítače vytvořit nebo aktualizovat definice těla požadavku](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Odeslání žádosti

Pro odeslání tohoto požadavku HTTP můžete použít klienta, který předzvěstí dáváte. [Nástroj v prohlížeči](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) můžete použít také klepnutím na tlačítko **Vyzkoušet.**

### <a name="responses"></a>Odezvy

Existují dvě úspěšné odpovědi pro operaci k vytvoření nebo aktualizaci virtuálního počítače:

| Name (Název)        | Typ                                                                              | Popis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Vytvořeno | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Vytvořeno     |

Zhuštěné *201 Vytvořeno* odpověď z předchozího příkladu tělo požadavku, který vytvoří virtuální ho virtuálního serveru ukazuje, že byl přiřazen *vmId* a *provisioningState* je *vytváření*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Další informace o odpovědích rozhraní REST API naleznete [v tématu Zpracování zprávy o odpovědi](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Další kroky

Další informace o azure rest api nebo jiných nástrojích pro správu, jako je Azure CLI nebo Azure PowerShell, najdete v následujících tématech:

- [Azure Compute provider REST API](/rest/api/compute/)
- [Začínáme s Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Modul Azure PowerShellu](/powershell/azure/overview)
