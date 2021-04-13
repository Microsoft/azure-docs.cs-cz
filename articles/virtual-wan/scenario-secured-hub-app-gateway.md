---
title: Zabezpečení provozu mezi Application Gateway a back-endové fondy
titleSuffix: Azure Virtual WAN
description: Scénáře týkající se zabezpečení provozu na cestách prostřednictvím aplikační brány nasazené ve virtuální síti rozbočovače připojené k zabezpečené virtuální síti WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315463"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Scénář: zabezpečení provozu mezi Application Gateway a back-end fondy

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři provoz uživatele vstoupí do Azure prostřednictvím aplikační brány nasazené v síti rozbočovače, která je připojená k zabezpečenému virtuálnímu rozbočovači sítě WAN (virtuální síť WAN s Azure Firewall). Cílem je použít Azure Firewall v zabezpečeném virtuálním centru ke kontrole provozu mezi aplikační bránou a back-end fondy.

V tomto scénáři jsou dva specifické vzory návrhu, v závislosti na tom, jestli jsou Aplikační brána a back-endové fondy ve stejné virtuální síti nebo v různých virtuální sítě.

* **Scénář 1:** Aplikační brána a back-end fondy jsou ve stejné virtuální síti partnerského vztahu virtuálních sítí WAN (samostatné podsítě).
* **Scénář 2:** Aplikační brána a back-end fondy jsou v různých virtuálních sítích partnerských virtuálních sítí WAN.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Scénář 1 – stejná síť VNet

V tomto scénáři se Aplikační brána a fondy back-endu nacházejí ve stejné virtuální síti partnerského vztahu virtuálních sítí WAN (samostatné podsítě).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagram scénáře 1" lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Pracovní postup

V současné době jsou trasy, které jsou inzerovány z tabulky směrování virtuálních sítí WAN, aplikovány na celou virtuální síť, nikoli v podsítích virtuální sítě rozbočovače. V důsledku toho jsou k povolení tohoto scénáře potřeba trasy definované uživatelem. Informace o trasách definovaných uživatelem (UDR) najdete v tématu [Virtual Network vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#user-defined).


1. V Azure Firewall Manageru ve virtuální síti paprsků, která obsahuje aplikační bránu a fondy back-endu, vyberte **Povolit zabezpečený internetový provoz** a **Povolit zabezpečený privátní provoz**.
1. Nakonfigurujte trasy definované uživatelem (udr) v podsíti aplikační brány.

   * Chcete-li zajistit, aby služba Application Gateway mohla odesílat přenosy přímo do Internetu, zadejte následující UDR:

     * **Předpona adresy:** 0.0.0.0.0/0
     * **Další segment směrování:** Internet

   * Pokud chcete zajistit, aby mohla brána Application Gateway odesílat data do back-endu fondu prostřednictvím Azure Firewall ve virtuálním centru WAN, zadejte následující UDR:

      * **Předpona adresy:** Podsíť back-end fondu (10.2.0.0/24)
      * **Další segment směrování:** Azure Firewall privátní IP adresa

1. Nakonfigurujte trasu definovanou uživatelem (UDR) pro podsíť back-end fondu.

   * **Předpona adresy:** Application Gateway podsíť
   * **Další segment směrování:** Azure Firewall privátní IP adresa

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Scénář 2 – různé virtuální sítě

V tomto scénáři se Aplikační brána a fondy back-endu nacházejí v různých virtuálních sítích, které jsou partnerského vztahu k virtuálnímu rozbočovači WAN.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagram scénáře 2" lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Pracovní postup

V současné době jsou trasy, které jsou inzerovány z tabulky směrování virtuálních sítí WAN, aplikovány na celou virtuální síť, nikoli v podsítích virtuální sítě rozbočovače. V důsledku toho jsou k povolení tohoto scénáře potřeba trasy definované uživatelem. Informace o trasách definovaných uživatelem (UDR) najdete v tématu [Virtual Network vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#user-defined).

1. V **Azure firewall Manageru** vyberte **Povolit zabezpečený internetový provoz** a **Povolte zabezpečený privátní provoz** na obou virtuálních sítích paprsků.

1. Nakonfigurujte trasy definované uživatelem (udr) v podsíti aplikační brány. Chcete-li zajistit, aby služba Application Gateway mohla odesílat přenosy přímo do Internetu, zadejte následující UDR:

   * **Předpona adresy:** 0.0.0.0.0/0
   * **Další segment směrování:** Internet

## <a name="next-steps"></a>Další kroky

* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
* Další informace o trasách definovaných uživatelem najdete v tématu [Virtual Network vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#user-defined).
* Informace o virtuálních sítích zabezpečených přes virtuální síť WAN najdete v tématu [zabezpečené virtuální rozbočovače](../firewall-manager/secured-virtual-hub.md).
