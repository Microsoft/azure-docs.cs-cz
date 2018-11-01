---
title: Vytvořit roli Avere s žádný kontroler – Avere vFXT pro Azure
description: Metodu pro vytvoření požadované role RBAC bez řadiče clusteru virtuálních počítačů
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633856"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Vytvořit přístup role Avere vFXT clusteru runtime bez kontroleru

Tento dokument ukazuje metodu pro vytvoření role clusteru uzel přístupu z příkazového řádku, před vytvořením clusteru adaptéru virtuálního počítače. 

Byl vytvořen z kontroleru clusteru, přečtěte si téma [vytvořit role clusteru uzel přístupu](avere-vfxt-deploy.md#create-the-cluster-node-access-role). Kontroler image obsahuje soubor prototypu role. Můžete aktualizovat soubor s ID vašeho předplatného a použijte jej k definování role místně na řadiči virtuálního počítače.

## <a name="create-an-azure-rbac-role"></a>Umožňuje vytvořit roli Azure RBAC

Používá systém vFXT Avere [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) k autorizaci vFXT uzly clusteru pro provedení nezbytných úloh.  

Jako součást normální vFXT operace clusteru jednotlivé vFXT uzly se muset zabývat záležitostmi, jako je čtení vlastností prostředků Azure, spravovat úložiště a řídit, ostatní uzly nastavení síťového rozhraní. 

Tato role se používá pro uzly vFXT pouze, ne pro kontroler clusteru virtuálních počítačů.  

Pokud chcete vytvořit roli před kontroleru, postupujte podle těchto kroků: 

1. Otevřete Azure Cloud Shell na webu Azure Portal nebo vyhledejte [ https://shell.azure.com ](https://shell.azure.com).

1. Přejděte ke svému předplatnému vFXT pomocí příkazového řádku Azure:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Pomocí těchto příkazů můžete stáhnout definici role z image marketplace a upravit ho. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Upravte soubor uvést ID předplatného a odstranit řádek nad ním. Uložte soubor jako ``avere-cluster.json``.

   ![Konzola znázorňující ID předplatného a "Odebrat tento řádek" vybraných pro odstranění textový editor](media/avere-vfxt-edit-role.png)

5. Vytvoření role:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Při vytváření clusteru zadejte název role (v tomto případě `avere-cluster`). Skript pro vytvoření clusteru přiřadí roli na nově vytvořený cluster uzly. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Definice role ukázka clusteru uzel modulu runtime

> [!IMPORTANT] 
> Tato ukázková definice byla získána z verze před GA produktu. Pokud verze, které získáte aktuální rozdělení produktu se liší, použijte místo toho tuto verzi.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```