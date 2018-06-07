---
title: Vytvořit virtuální počítač s Linuxem pomocí rozhraní REST API Azure | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem v Azure, která používá ověřování spravované disky a SSH pomocí rozhraní REST API Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825137"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Vytvořit virtuální počítač s Linuxem, která používá ověřování SSH pomocí rozhraní REST API

Virtuální počítač (VM) v Azure definuje různé parametry, jako je například umístění, velikost hardwaru, bitovou kopii operačního systému a přihlašovací údaje. Tento článek ukazuje, jak vytvořit virtuální počítač s Linuxem, která používá ověřování SSH pomocí rozhraní REST API.

Chcete-li vytvořit nebo aktualizovat virtuální počítač, použijte následující *PUT* operace:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Vytvořit žádost o

Chcete-li vytvořit *PUT* požadavku, `{subscription-id}` parametr je povinný. Pokud máte více předplatných, najdete v části [práce s více předplatných](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Můžete definovat `{resourceGroupName}` a `{vmName}` pro vaše prostředky, spolu s `api-version` parametr. Tento článek používá `api-version=2017-12-01`.

Vyžadují se následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Content-Type:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Autorizace:* | Povinná hodnota. Nastavte na platnou `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Další informace o tom, jak vytvořit požadavek najdete v tématu [součástí rozhraní REST API požadavků a odpovědí](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

Tyto společné definice se používají k vytvoření textu žádosti:

| Název                       | Požaduje se | Typ                                                                                | Popis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | řetězec                                                                              | Umístění prostředků. |
| jméno                       |          | řetězec                                                                              | Název virtuálního počítače. |
| properties.hardwareProfile |          | [Položka HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Určuje nastavení hardwaru pro virtuální počítač. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Určuje nastavení úložiště pro disky virtuálního počítače. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Určuje nastavení operačního systému pro virtuální počítač. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Určuje rozhraní sítě virtuálního počítače. |

Úplný seznam dostupných definice v textu požadavku, najdete v části [virtuálních počítačů umožňuje vytvořit nebo aktualizovat defintions textu požadavku](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Příklad textu žádosti

Následující příklad textu požadavku definuje bitovou Ubuntu 18.04 LTS, která používá spravované prémiové disky. Virtuální počítač používá existující virtuální síťová karta (NIC), ke které máte a slouží k ověření veřejného klíče SSH [vytvořili](../../virtual-network/virtual-network-network-interface.md). Zadejte svůj veřejný klíč SSH v *osProfile.linuxConfiguration.ssh.publicKeys.keyData* pole. V případě potřeby můžete [Generování páru klíčů SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
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
      "computerName": "myVM",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Odezvy

Existují dvě úspěšné odpovědi pro operaci Vytvořit nebo aktualizovat virtuální počítač:

| Název        | Typ                                                                              | Popis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 – vytvořeno | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Vytvořeno     |

Další informace o rozhraní REST API odpovědí najdete v tématu [zpracovat zprávu odpovědi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Příklad odpovědi

Zhuštěné *201 – vytvořeno* zobrazí odpověď z obsahu žádosti předchozí příklad, která vytvoří virtuální počítač *vmId* byl přiřazen a *provisioningState* je *Vytváření*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Další postup

Další informace o rozhraní API REST Azure nebo jiné nástroje pro správu, jako je například 2.0 rozhraní příkazového řádku Azure nebo Azure PowerShell naleznete v následujících tématech:

- [Zprostředkovatel Azure výpočetní REST API](/rest/api/compute/)
- [Začínáme s Azure REST API](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Modul Azure PowerShell](/powershell/azure/overview)
