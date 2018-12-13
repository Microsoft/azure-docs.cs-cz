---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ec30840e550b87f83de67d52348b9723400e0f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111925"
---
1. V [webu Azure portal](http://portal.azure.com), vyberte virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.

2. V **nastavení** část stránce vaší virtuální sítě, vyberte **podsítě** rozšířit **podsítě** stránky.

3. Na **podsítě** stránce **podsíť brány** otevřít **přidat podsíť** stránky.

  ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Přidání podsítě brány")

4. **Název** pro vaši podsíť je automaticky autofilled s hodnotou *GatewaySubnet*. Tato hodnota je vyžadována pro Azure podsíť rozpoznala jako podsíť brány. Upravit autofilled **rozsah adres** hodnoty tak, aby odpovídaly požadavkům vaší konfigurace, vyberte **OK** vytvořte podsíť.

  ![Přidání podsítě](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Přidání podsítě")