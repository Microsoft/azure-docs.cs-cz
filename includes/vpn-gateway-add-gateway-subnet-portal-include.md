---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 158400645423d2e9fe92768786b570e917907d98
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce vaší virtuální sítě v části **Nastavení** kliknutím na **Podsítě** rozbalte stránku Podsítě.
3. Na stránce **Podsítě** kliknutím na **+Podsíť brány** v horní části otevřete stránku **Přidat podsíť**.

  ![Přidání podsítě brány](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Přidání podsítě brány")
4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. Hodnota GatewaySubnet je potřeba k tomu, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace.

  ![Přidání podsítě brány](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Přidání podsítě brány")
5. Podsíť vytvoříte kliknutím na **OK** v dolní části stránky.