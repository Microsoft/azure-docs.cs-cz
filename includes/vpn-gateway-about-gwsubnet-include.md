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
ms.openlocfilehash: 0334f9fd2d749b88580ff3857d705de2ae961902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407565"
---
Brána virtuální sítě používá konkrétní podsíť nazývanou podsíť brány. Podsíť brány je součástí rozsahu IP adres virtuální sítě, který zadáváte při konfiguraci virtuální sítě. Obsahuje IP adresy, které používají prostředky a služby brány virtuální sítě. Podsíť musí mít název GatewaySubnet, aby služba Azure mohla nasadit prostředky brány. Pro nasazení prostředků brány není možné zadat jinou podsíť. Pokud nemáte podsíť GatewaySubnet, vytváření brány VPN selže.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Potřebný počet IP adres závisí na konfiguraci brány VPN, kterou chcete vytvořit. Některé konfigurace vyžadují víc IP adres než jiné. Doporučujeme vytvořit podsíť brány používající velikost /27 nebo /28.

Pokud se zobrazí chyba oznamující překrývání adresního prostoru a podsítě nebo umístění podsítě mimo adresní prostor vaší virtuální sítě, zkontroluje rozsah adres vaší virtuální sítě. V rozsahu adres, který jste pro svou virtuální síť vytvořili, možná není k dispozici dostatek IP adres. Pokud například vaše výchozí podsíť zahrnuje celý rozsah adres, k vytvoření dalších podsítí už nejsou k dispozici žádné IP adresy. Můžete upravit podsítě v rámci stávajícího adresního prostoru a tím uvolnit IP adresy nebo můžete zadat další rozsah adres a vytvořit podsíť brány v něm.