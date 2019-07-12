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
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673290"
---
1. V [webu Azure portal](https://portal.azure.com), vyberte virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.

2. V **nastavení** část stránce vaší virtuální sítě, vyberte **podsítě** rozšířit **podsítě** stránky.

3. Na **podsítě** stránce **podsíť brány** otevřít **přidat podsíť** stránky.

   ![Přidání podsítě brány](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Přidání podsítě brány")

4. **Název** pro vaši podsíť je automaticky autofilled s hodnotou *GatewaySubnet*. Tato hodnota je vyžadována pro Azure podsíť rozpoznala jako podsíť brány. Upravit autofilled **rozsah adres** hodnoty tak, aby odpovídaly požadavkům vaší konfigurace, vyberte **OK** vytvořte podsíť.

   ![Přidání podsítě](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Přidání podsítě")
