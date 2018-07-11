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
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928178"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Vytvoření virtuálního počítače s Linuxem, které využívá ověřování SSH pomocí rozhraní REST API

Virtuální počítač (VM) v Azure je definovaná pomocí různých parametrů, jako je například umístění, velikost hardwaru, image operačního systému a přihlašovací údaje. V tomto článku se dozvíte, jak vytvořit virtuální počítač s Linuxem, které využívá ověřování SSH pomocí rozhraní REST API.

Chcete-li vytvořit nebo aktualizovat virtuální počítač, použijte následující *UMÍSTIT* operace:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Vytvořit žádost

Chcete-li vytvořit *UMÍSTIT* požadavek, `{subscription-id}` parametr je povinný. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Můžete definovat `{resourceGroupName}` a `{vmName}` pro vaše prostředky, spolu s `api-version` parametru. Tento článek používá `api-version=2017-12-01`.

Vyžadují se následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Typ obsahu:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Autorizace:* | Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Další informace o tom, jak vytvořit žádost, naleznete v tématu [součástí žádost/odpověď rozhraní REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

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

Úplný seznam dostupných definic v textu požadavku, najdete v části [virtuální počítače, vytvořit nebo aktualizovat definice těla požadavku](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Text požadavku na příkladu

Text požadavku následující příklad definuje image Ubuntu 18.04 LTS, který používá spravované disky úrovně Premium. Ověření veřejného klíče SSH se používá, a virtuální počítač používá existující virtuální síťová karta (NIC), které máte [vytvořili](../../virtual-network/virtual-network-network-interface.md). Zadejte veřejný klíč SSH v *osProfile.linuxConfiguration.ssh.publicKeys.keyData* pole. V případě potřeby můžete [Generování páru klíčů SSH](mac-create-ssh-keys.md).

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

Existují dva úspěšné odpovědi pro operaci vytvoření nebo aktualizaci virtuálního počítače:

| Název        | Typ                                                                              | Popis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 – vytvořeno | [Virtuální počítač](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Vytvořeno     |

Další informace o rozhraní REST API odpovědi najdete v tématu [zpracování zprávy s odpovědí](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Příklad odpovědi

Zhuštěnému *201 – vytvořeno* odpovědi z předchozího textu požadavku příklad, který vytvoří virtuální počítač zobrazuje *vmId* bylo přiřazeno a *provisioningState* je *Vytváření*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Další postup

Další informace o rozhraní REST API služby Azure nebo jiné nástroje pro správu, jako je Azure CLI 2.0 nebo Azure Powershellu naleznete v následujících tématech:

- [Rozhraní REST API poskytovatele Azure Compute](/rest/api/compute/)
- [Začínáme s Azure REST API](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Modul Azure PowerShell](/powershell/azure/overview)
