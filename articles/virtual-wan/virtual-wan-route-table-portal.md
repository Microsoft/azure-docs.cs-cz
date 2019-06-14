---
title: Vytvoření Azure virtuální sítě WAN virtuální rozbočovač směrovací tabulky – Azure portal | Dokumentace Microsoftu
description: Tabulka směrování virtuální sítě WAN virtuální rozbočovač řídit provoz na síťové virtuální zařízení na portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461650"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Vytvoření virtuální sítě WAN centra směrovací tabulky pro síťová virtuální zařízení: portál Azure

Tento článek popisuje, jak řídit provoz od rozbočovače síťové virtuální zařízení (NVA).

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Než začnete

Ověřte, že splňujete následující kritéria:

*  Máte síťové virtuální zařízení (NVA). Síťové virtuální zařízení se softwarem třetích stran podle vašeho výběru, který je obvykle zřízený z Azure Marketplace ve virtuální síti.

    * Privátní IP adresa musí přiřazené k síťovému rozhraní síťového virtuálního zařízení.

    * Síťové virtuální zařízení není nasazený ve virtuální rozbočovač. Musí být nasazeny v samostatné virtuální sítě.

    *  Síťové virtuální zařízení virtuální síť může mít jeden nebo mnoho virtuálních sítí k němu připojená. V tomto článku označujeme síťové virtuální zařízení virtuální sítě jako "nepřímé paprsku". Tyto virtuální sítě lze připojit k virtuální síti síťové virtuální zařízení s využitím partnerského vztahu.
*  Vytvořili jste 2 virtuálními sítěmi. Používají se jako virtuální sítě paprsků.

    * Pro toto cvičení se adresní prostory virtuální sítě paprsků: VNet1: 10.0.2.0/24 a VNet2: 10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md).

    * Ujistěte se, že nejsou žádné brány virtuální sítě v některém z virtuální sítě.
    * Pro tuto konfiguraci těchto virtuálních sítí, aby podsíť brány.

## <a name="signin"></a>1. Přihlášení

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

## <a name="vwan"></a>2. Vytvoření virtuální sítě WAN

Vytvořte virtuální síť WAN. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Název virtuální sítě WAN:** myVirtualWAN
* **Skupina prostředků:** testRG
* **Umístění:** Západní USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Vytvoření rozbočovače

Vytvoření rozbočovače. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Umístění:** Západní USA
* **Název:** westushub
* **Centrum privátní adresní prostor:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Vytvoření a použití směrovací tabulky centra

Aktualizujte Centrum centra směrovací tabulku. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Adresní prostory virtuální sítě nepřímé paprsku:** 10.0.2.0/24 (ze sítě VNet1 a VNet2) a 10.0.3.0/24
* **Síťové virtuální zařízení hraniční sítě rozhraní privátní IP adresa sítě:** 10.0.4.5

1. Přejděte do vaší virtuální sítě WAN.
2. Klikněte na centrum, pro kterou chcete vytvořit směrovací tabulku.
3. Klikněte na tlačítko **...** a potom klikněte na tlačítko **upravit virtuální rozbočovač**.
4. Na **upravit virtuální rozbočovač** stránce, posuňte se dolů a vyberte zaškrtávací políčko **tabulky použijte pro směrování**.
5. V **Pokud je předpona cílové** sloupce, přidejte adresní prostory. V **odesílat další segment směrování** sloupce, přidejte síťové virtuální zařízení hraniční sítě síťové rozhraní privátní IP adresu.
6. Klikněte na tlačítko **potvrdit** aktualizovat prostředek centra nastavení směrovací tabulky.

## <a name="connections"></a>5. Vytvoření připojení virtuální sítě

Vytvořte připojení k centru z každého paprsku nepřímé virtuální sítě (ze sítě VNet1 a VNet2). Pak vytvořte připojení z virtuální sítě síťové virtuální zařízení k rozbočovači.

 Pro tento krok můžete použít následující hodnoty:

| Název virtuální sítě| Název připojení|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Následující postup opakujte pro každou virtuální síť, kterou chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Klikněte na tlačítko **OK** k vytvoření připojení.

## <a name="next-steps"></a>Další postup

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).