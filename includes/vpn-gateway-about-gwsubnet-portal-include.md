---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "71266522"
---
Brána virtuální sítě používá konkrétní podsíť nazývanou podsíť brány. Podsíť brány je součástí rozsahu IP adres virtuální sítě, který zadáváte při konfiguraci virtuální sítě. Obsahuje IP adresy, které používají prostředky a služby brány virtuální sítě. 


Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Potřebný počet IP adres závisí na konfiguraci brány VPN, kterou chcete vytvořit. Některé konfigurace vyžadují víc IP adres než jiné. Doporučujeme vytvořit podsíť brány používající velikost /27 nebo /28.


Pokud se zobrazí chyba oznamující překrývání adresního prostoru a podsítě nebo umístění podsítě mimo adresní prostor vaší virtuální sítě, zkontroluje rozsah adres vaší virtuální sítě. V rozsahu adres, který jste pro svou virtuální síť vytvořili, možná není k dispozici dostatek IP adres. Pokud například vaše výchozí podsíť zahrnuje celý rozsah adres, k vytvoření dalších podsítí už nejsou k dispozici žádné IP adresy. Můžete upravit podsítě v rámci stávajícího adresního prostoru a tím uvolnit IP adresy nebo můžete zadat další rozsah adres a vytvořit podsíť brány v něm.