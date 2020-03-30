---
title: Partnerský vztah v internetu – časté otázky
titleSuffix: Azure
description: Partnerský vztah v internetu – časté otázky
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775495"
---
# <a name="internet-peering---faqs"></a>Partnerský vztah v internetu – časté otázky

Níže si můžete prohlédnout níže uvedené informace o obecných otázkách.

**Jaký je rozdíl mezi partnerským vztahem v Internetu a službou partnerského vztahu?**

Peering Service je služba, která má v úmyslu poskytovat podnikové veřejné IP připojení společnosti Microsoft pro své podnikové zákazníky. Internet na podnikové úrovni zahrnuje připojení prostřednictvím poskytovatelů služeb Internetu, kteří mají připojení společnosti Microsoft s vysokou propustností a redundanci pro připojení HA. Kromě toho je uživatelský provoz optimalizován pro latenci k nejbližšímu microsoftedge. Služba partnerského vztahu staví na připojení partnerského vztahu s partnerským operátorem. Připojení partnerského vztahu s partnerem musí být přímý partnerský vztah na rozdíl od partnerského vztahu exchange. Přímý partnerský vztah musí mít místní a geografickou redundanci.

**Co je starší partnerský vztah?**

Připojení partnerského vztahu nastavené pomocí Azure PowerShellu se spravuje jako prostředek Azure. Připojení partnerského vztahu nastavená v minulosti jsou uložena v našem systému jako starší partnerský vztah, který můžete převést ke správě jako prostředek Azure.

**Při new-azpeeringDirectConnectionObject je volána, jaké IP adresy jsou uvedeny na Microsoft a Peer zařízení?**

Při volání rutiny New-AzPeeringDirectConnectionObject je zadána adresa /31 (a.b.c.d/31) nebo adresa /30 (a.b.c.d/30). První IP adresa (a.b.c.d+0) je dána zařízení Peer a druhá IP adresa (a.b.c.d +1) je dána zařízení Společnosti Microsoft.

**Co je MaxPrefixesInzerované IPv4 a MaxPrefixesInzerovanéIPv6 parametry v rutině New-AzPeeringDirectConnectionObject?**

MaxPrefixesInzerované IPv4 a MaxPrefixesInzerované IPv6 parametry představují maximální počet IPv4 a IPv6 předpony Peer chce Microsoft přijmout. Tyto parametry lze kdykoli změnit.