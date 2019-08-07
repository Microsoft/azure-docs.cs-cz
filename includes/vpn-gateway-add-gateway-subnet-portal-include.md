---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780177"
---
Podsíť brány obsahuje rezervované IP adresy, které používají služby brány virtuální sítě. Vytvořte podsíť brány.

1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce virtuální síť klikněte na **podsítě** a rozbalte stránku **VNet1-subnets** .
3. Kliknutím na **+ podsíť brány** v horní části otevřete stránku **Přidat podsíť** .

   ![Přidání podsítě brány](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Přidání podsítě brány")
4. **Název** vaší podsítě se automaticky vyplní požadovanou hodnotou ' GatewaySubnet '. Upravte automaticky vyplněný **Rozsah adres (blok CIDR)** tak, aby odpovídal následující hodnotě:

   **Rozsah adres (blok CIDR)** : 10.1.255.0/27

   ![Přidání podsítě brány](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Přidání podsítě brány")
5. Zbývající nastavení nechte nastavené na výchozí **hodnotu žádné** nebo **0**. Pak kliknutím na **OK** vytvořte podsíť brány.