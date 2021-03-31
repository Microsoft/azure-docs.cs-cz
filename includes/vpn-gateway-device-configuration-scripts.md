---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77111212"
---
| **Dodavatel** | **Řada zařízení** | **Verze firmwaru** |
| --- | --- | --- |
|Cisco | ISR| IOS 15,1 (Preview)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-No BGP) pro ASA pod 9,8 |
|Cisco | ASA | ASA RouteBased (IKEv2-No BGP) pro ASA 9.8 + |
|Juniper | SRX_GA | 12. x|
|Juniper | SSG_GA | ScreenOS 6.2. x|
|Juniper | JSeries_GA | JunOS 12. x|
|Juniper | SRX | JunOS 12. x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 × RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 x RouteBased BGP|

> [!NOTE]
> ( * ) Požadováno: NarrowAzureTrafficSelectors (povolit možnost UsePolicyBasedTrafficSelectors) a CustomAzurePolicies (IKE/IPsec)
>
