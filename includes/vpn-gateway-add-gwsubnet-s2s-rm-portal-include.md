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
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce vaší virtuální sítě v části **Nastavení** kliknutím na **Podsítě** rozbalte stránku Podsítě.
3. Na stránce **Podsítě** kliknutím na **+Podsíť brány** v horní části otevřete stránku **Přidat podsíť**.

  ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Přidání podsítě brány")
4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. Hodnota GatewaySubnet je potřeba k tomu, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace.

  ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Přidání podsítě brány")
5. Podsíť vytvoříte kliknutím na **OK** v dolní části stránky.