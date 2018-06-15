---
title: Zobrazit topologii sledovací proces sítě Azure – rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje postup používání rozhraní příkazového řádku Azure k dotazování topologii vaší sítě.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 654c23e567d81bff1cb0c3091ba3d8f96f0a3eda
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864466"
---
# <a name="view-network-watcher-topology-with-azure-cli"></a>Zobrazení topologie sledovací proces sítě pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Topologie funkci sledovací proces sítě poskytuje vizuální reprezentace síťových prostředků v předplatném. Na portálu pro tuto vizualizaci se zobrazují automaticky. Informace pro zobrazení topologie na portálu se dají získat pomocí prostředí PowerShell.
Díky této vlastnosti je rozmanitější jako data mohou být spotřebovávána další nástroje pro sestavení se vizualizaci informací o topologii.

Tento článek používá 1.0 rozhraní příkazového řádku Azure a platformy, která je dostupná pro Windows, Mac a Linux. Sledovací proces sítě aktuálně používá pro podporu rozhraní příkazového řádku Azure CLI 1.0.

Propojení je modelován v dva vztahy.

- **Členství ve skupině** – příklad: obsahuje podsíť virtuální sítě obsahuje síťový adaptér
- **Související** – příklad: síťový adaptér je přidružený virtuální počítač

V následujícím seznamu je vlastnosti, které jsou vráceny při dotazování topologie REST API.

* **název** -název prostředku
* **ID** – identifikátor uri prostředku.
* **umístění** -umístění, kde existuje prostředek.
* **přidružení** – seznam přidružení k odkazovaného objektu.
    * **název** -název odkazovaného prostředku.
    * **resourceId** -resourceId je identifikátor uri prostředku, kterou se odkazuje v přidružení.
    * **Třída associationType** – tato hodnota se odkazuje vztah mezi podřízený objekt a nadřazený. Platné hodnoty jsou **obsahuje** nebo **přidružené**.

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři použijete `network watcher topology` rutiny k načtení informací o topologii. O tom, jak je také článek [načíst topologie sítě pomocí rozhraní REST API](network-watcher-topology-rest.md).

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku načte odpověď topologie pro danou skupinu prostředků.

## <a name="retrieve-topology"></a>Načtení topologie

`network watcher topology` Rutina načte topologie pro danou skupinu prostředků. Přidat argument "--json" zobrazíte oput ve formátu json

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Výsledky

Výsledky vrácené mít vlastnost name "zdroje", které obsahuje těla odpovědi json pro `network watcher topology` rutiny.  Odpověď obsahuje prostředky ve skupině zabezpečení sítě a jejich přidružení (který je obsahuje, přidružené).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení pravidla, která se použijí k síťovým prostředkům, navštivte stránky [přehled zobrazení skupiny zabezpečení](network-watcher-security-group-view-overview.md)
