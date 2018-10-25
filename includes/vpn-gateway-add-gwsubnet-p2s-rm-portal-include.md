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
ms.openlocfilehash: bc1e2803a420b95e1abec0886733c5e42a8cfdb4
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026598"
---
1. Na [portálu](http://portal.azure.com) přejděte na virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce vaší virtuální sítě v části **Nastavení** kliknutím na **Podsítě** rozbalte stránku **Podsítě**.
3. Na stránce **Podsítě** kliknutím na **+Podsíť brány** otevřete stránku **Přidat podsíť**. 

  ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Přidání podsítě brány")
4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Nekonfigurujte směrovací tabulky nebo koncové body služby.

  ![Přidání podsítě](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Přidání podsítě")
5.  Klikněte na tlačítko **OK** v dolní části stránky vytvořte podsíť.