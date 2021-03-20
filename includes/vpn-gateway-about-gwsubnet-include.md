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
ms.openlocfilehash: 5a734963ced9daefda2b7b6f4a52fd9ef437eddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "71269267"
---
Brána virtuální sítě používá konkrétní podsíť nazývanou podsíť brány. Podsíť brány je součástí rozsahu IP adres virtuální sítě, který zadáváte při konfiguraci virtuální sítě. Obsahuje IP adresy, které používají prostředky a služby brány virtuální sítě. Podsíť musí mít název GatewaySubnet, aby služba Azure mohla nasadit prostředky brány. Pro nasazení prostředků brány není možné zadat jinou podsíť. Pokud nemáte podsíť GatewaySubnet, vytváření brány VPN selže.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Potřebný počet IP adres závisí na konfiguraci brány VPN, kterou chcete vytvořit. Některé konfigurace vyžadují víc IP adres než jiné. Doporučujeme vytvořit podsíť brány používající velikost /27 nebo /28.

Pokud se zobrazí chyba oznamující překrývání adresního prostoru a podsítě nebo umístění podsítě mimo adresní prostor vaší virtuální sítě, zkontroluje rozsah adres vaší virtuální sítě. V rozsahu adres, který jste pro svou virtuální síť vytvořili, možná není k dispozici dostatek IP adres. Pokud například vaše výchozí podsíť zahrnuje celý rozsah adres, k vytvoření dalších podsítí už nejsou k dispozici žádné IP adresy. Můžete upravit podsítě v rámci stávajícího adresního prostoru a tím uvolnit IP adresy nebo můžete zadat další rozsah adres a vytvořit podsíť brány v něm.