---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444055"
---
1. Na [portálu](http://portal.azure.com) přejděte na virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce vaší virtuální sítě v části **Nastavení** kliknutím na **Podsítě** rozbalte stránku **Podsítě**.
3. Na stránce **Podsítě** kliknutím na **+Podsíť brány** otevřete stránku **Přidat podsíť**. 

   ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Přidání podsítě brány")
4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Nekonfigurujte směrovací tabulky nebo koncové body služby.

   ![Přidání podsítě](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Přidání podsítě")
5. Klikněte na tlačítko **OK** v dolní části stránky vytvořte podsíť.
