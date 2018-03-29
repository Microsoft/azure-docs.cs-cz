---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3d76f955f8d8d3d2b269c09387717a6571adf8c3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Brána virtuální sítě používá konkrétní podsíť nazývanou podsíť brány. Podsíť brány je součástí rozsahu IP adres virtuální sítě, který zadáváte při konfiguraci virtuální sítě. Obsahuje IP adresy, které používají prostředky a služby brány virtuální sítě. Podsíť musí mít název GatewaySubnet, aby služba Azure mohla nasadit prostředky brány. Pro nasazení prostředků brány není možné zadat jinou podsíť. Pokud nemáte podsíť GatewaySubnet, vytváření brány VPN selže.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Potřebný počet IP adres závisí na konfiguraci brány VPN, kterou chcete vytvořit. Některé konfigurace vyžadují víc IP adres než jiné. Doporučujeme vytvořit podsíť brány používající velikost /27 nebo /28.

Pokud se zobrazí chyba oznamující překrývání adresního prostoru a podsítě nebo umístění podsítě mimo adresní prostor vaší virtuální sítě, zkontroluje rozsah adres vaší virtuální sítě. V rozsahu adres, který jste pro svou virtuální síť vytvořili, možná není k dispozici dostatek IP adres. Pokud například vaše výchozí podsíť zahrnuje celý rozsah adres, k vytvoření dalších podsítí už nejsou k dispozici žádné IP adresy. Můžete upravit podsítě v rámci stávajícího adresního prostoru a tím uvolnit IP adresy nebo můžete zadat další rozsah adres a vytvořit podsíť brány v něm.