---
title: Internet peering – Nejčastější dotazy
titleSuffix: Azure
description: Internet peering – Nejčastější dotazy
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775495"
---
# <a name="internet-peering---faqs"></a>Internet peering – Nejčastější dotazy

Pro obecné otázky si můžete přečíst následující informace.

**Jaký je rozdíl mezi internetovým partnerským vztahem a službou partnerského vztahu?**

Peering Service je služba, která v úmyslu poskytovat Microsoftu pro své podnikové zákazníky připojení veřejné IP adresy podnikové úrovně. Podniková síť Internet zahrnuje připojení prostřednictvím poskytovatelů internetových služeb, které mají vysokou propustnost připojení Microsoftu a redundanci pro konektivitu HA. Pro latenci na nejbližší Microsoft Edge se navíc optimalizuje provoz uživatele. Služba partnerského vztahu je založena na připojení partnerského vztahu s partnerským operátorem. Připojení partnerského vztahu k partnerovi musí být přímé partnerské vztahy na rozdíl od Exchange peering. Přímý partnerský vztah musí mít místní a geografickou redundanci.

**Co je starší verze partnerského vztahu?**

Připojení partnerského vztahu nastavené pomocí Azure PowerShell se spravuje jako prostředek Azure. Připojení partnerského vztahu nastavená v minulosti se ukládají v našem systému jako starší verze partnerského vztahu, kterou můžete zvolit pro správu jako prostředek Azure.

**Když se zavolá New-AzPeeringDirectConnectionObject, jaké IP adresy se mají přidávat do zařízení Microsoftu a peer?**

Při volání rutiny New-AzPeeringDirectConnectionObject se zadá adresa/31 (a. b. c. d/31) nebo adresa/30 (a. b. c. d/30). První IP adresa (a. b. c. d. d + 0) se předávají zařízení společnosti Microsoft pro zařízení druhé IP adresy (a. b. c. d + 1).

**Jaké jsou parametry MaxPrefixesAdvertisedIPv4 a MaxPrefixesAdvertisedIPv6 v rutině New-AzPeeringDirectConnectionObject?**

Parametry MaxPrefixesAdvertisedIPv4 a MaxPrefixesAdvertisedIPv6 reprezentují maximální počet předpon adres IPv4 a IPv6, které chce společnost Microsoft přijmout. Tyto parametry lze kdykoli změnit.