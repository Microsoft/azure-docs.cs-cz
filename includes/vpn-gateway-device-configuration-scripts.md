---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "30326509"
---
| **Dodavatel** | **Řada zařízení** | **Verze firmwaru** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (Preview)|
|Cisco | ASA | ASA (*) RouteBased (ne IKEv2 BGP) pro ASA níže 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 – bez protokolu BGP) pro ASA 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased protokolu BGP|

> [!NOTE]
> (*) Požadováno: NarrowAzureTrafficSelectors a CustomAzurePolicies (IKE/IPsec)
>