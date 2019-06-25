---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174877"
---
| **Dodavatel** | **Řada zařízení** | **Verze firmwaru** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (Preview)|
|Cisco | ASA | Azure Stream Analytics (*) RouteBased (IKEv2 bez BGP) pro Azure Stream Analytics pod 9.8 |
|Cisco | ASA | RouteBased ASA (IKEv2 – bez BGP) pro Azure Stream Analytics 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased protokolu BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| RouteBased v1.10x EdgeOS protokolu BGP|

> [!NOTE]
> ( * ) Požadováno: NarrowAzureTrafficSelectors (povolení možností UsePolicyBasedTrafficSelectors) a CustomAzurePolicies IKE a IPsec)
>
