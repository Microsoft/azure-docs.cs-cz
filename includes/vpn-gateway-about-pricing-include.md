---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "67666389"
---
Platíte za dvě věci: náklady na výpočetní kapacitu pro bránu virtuální sítě a výchozí přenos dat z brány virtuální sítě. Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway). Ceny pro starší verze služby Gateway najdete na [stránce s cenami ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) a posuňte se na část **Virtual Network brány** .

**Náklady na výpočetní výkon brány virtuální sítě**<br>Každá brána virtuální sítě má náklady na hodinový výpočetní výkon. Cena je založená na SKU brány, kterou určíte při vytváření brány virtuální sítě. Náklady jsou pro samotnou bránu vedle přenosu dat, který bránou prochází. Náklady na aktivní-aktivní instalaci jsou stejné jako aktivní – pasivní.

**Náklady na přenos dat**<br>Náklady na přenos dat se počítají na základě výchozího přenosu ze zdrojové brány virtuální sítě.

* Pokud odesíláte přenosy do místního zařízení VPN, budou se účtovat podle sazby za výchozí internetový přenos dat.
* Pokud přenášíte data mezi virtuálními sítěmi v různých oblastech, liší se ceny podle oblastí.
* Pokud odesíláte přenosy pouze mezi virtuálními sítěmi ve stejné oblasti, žádné náklady na data se na vás nevztahují. Přenosy dat mezi virtuálními sítěmi ve stejné oblasti je zdarma.
