---
title: Zobrazit topologii sledovací proces sítě Azure – REST API | Microsoft Docs
description: Tento článek popisuje postup použití rozhraní REST API pro dotaz topologii vaší sítě.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864179"
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Zobrazení topologie sledovací proces sítě pomocí rozhraní REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Topologie funkci sledovací proces sítě poskytuje vizuální reprezentace síťových prostředků v předplatném. Na portálu pro tuto vizualizaci se zobrazují automaticky. Informace pro zobrazení topologie na portálu se dají získat pomocí prostředí PowerShell.
Díky této vlastnosti je rozmanitější jako data mohou být spotřebovávána další nástroje pro sestavení se vizualizaci informací o topologii.

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

V tomto scénáři můžete načíst informace o topologii. ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey v [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku načte odpověď topologie pro danou skupinu prostředků.

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

Přihlaste se k armclient pomocí svých přihlašovacích údajů Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Načtení topologie

Následující příklad požádá topologii z rozhraní REST API.  V příkladu je parametry umožňující flexibilitu při vytváření příklad.  Nahraďte všechny hodnoty s \< \> které obaluje je.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

Je odpověď na následující příklad zkrácení odpovědi, která je vrácena při načtení topologie pro skupina prostředků:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak toku protokolů NSG s Power BI vizualizovat navštivte stránky [vizualizovat NSG toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

