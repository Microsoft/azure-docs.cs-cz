---
title: 'Virtuální WAN: Vytvoření tabulky směrování virtuálního rozbočovače do portálu NVA: Azure Portal'
description: Virtuální wan virtuální rozbočovací tabulka směrovat provoz do sítě virtuálního zařízení pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402950"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Vytvoření směrovací tabulky centra virtuální sítě WAN pro virtuální virtuální virtuální zařízení: portál Azure

Tento článek ukazuje, jak řídit provoz z větve (místní lokality) připojené k virtuální síti WAN hub u virtuální sítě pro virtuální síť (VNet) prostřednictvím síťového virtuálního zařízení (NVA).

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Než začnete

Ověřte, zda jste splnili následující kritéria:

*  Máte síťové virtuální zařízení (NVA). Síťové virtuální zařízení je software třetí strany podle vašeho výběru, který se obvykle zřizoval z Azure Marketplace ve virtuální síti.

    * Privátní IP adresa musí být přiřazena síťovému rozhraní síťového virtuálního zařízení.

    * NVA není nasazen ve virtuálním rozbočovači. Musí být nasazenv samostatné virtuální síti.

    *  Virtuální síť NVA může mít jednu nebo více virtuálních sítí, které jsou k němu připojeny. V tomto článku odkazujeme na virtuální síť NVA jako "nepřímá virtuální síť pro paprsky". Tyto virtuální sítě lze připojit k virtuální síti NVA pomocí partnerského vztahu virtuální sítě. Odkazy partnerského vztahu virtuální sítě jsou zobrazeny černými šipkami na výše uvedeném obrázku mezi virtuální sítí 1, virtuální sítí 2 a virtuální sítí NVA.
*  Vytvořili jste dvě virtuální sítě. Budou použity jako virtuální sítě s paprsky.

    * Adresní prostory virtuální sítě jsou: VNet1: 10.0.2.0/24 a VNet2: 10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si informace [o vytvoření virtuální sítě](../virtual-network/quick-create-portal.md).

    * Ujistěte se, že v žádné virtuální chodnících nejsou žádné brány virtuální sítě.

    * Virtuální sítě nevyžadují podsíť brány.

## <a name="1-sign-in"></a><a name="signin"></a>1. Přihlaste se

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Vytvoření virtuální sítě WAN

Vytvořte virtuální WAN. Použijte následující příkladové hodnoty:

* **Název virtuální sítě WAN:** myVirtualWAN
* **Skupina prostředků:** testRG
* **Umístění:** Západní USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Vytvoření rozbočovače

Vytvořte rozbočovač. Použijte následující příkladové hodnoty:

* **Umístění:** Západní USA
* **Jméno:** westushub
* **Privátní adresní prostor centra:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Vytvoření a použití tabulky tras rozbočovače

Aktualizujte rozbočovač pomocí směrovací tabulky rozbočovače. Použijte následující příkladové hodnoty:

* **Adresní prostory virtuální sítě pro paprsky:** (VNet1 a VNet2) 10.0.2.0/24 a 10.0.3.0/24
* **DMZ NVA síťové rozhraní privátní IP adresa:** 10.0.4.5

1. Přejděte na virtuální WAN.
2. Klikněte na rozbočovač, pro který chcete vytvořit směrovací tabulku.
3. Klikněte na **...** a potom klikněte na **Upravit virtuální rozbočovač**.
4. Na stránce **Upravit virtuální rozbočovač** posuňte dolů a zaškrtněte políčko **Použít tabulku pro směrování**.
5. Ve sloupci **Pokud je cílová předpona,** přidejte adresní prostory. Ve sloupci **Odeslat na další směrování** přidejte privátní IP adresu síťového rozhraní DMZ NVA.
6. Kliknutím na **Potvrdit** aktualizujte prostředek rozbočovače pomocí nastavení tabulky trasy.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Vytvoření připojení virtuální sítě

Vytvořte připojení virtuální sítě z každé virtuální sítě s nepřímým paprskem (VNet1 a VNet2) do rozbočovače. Tato připojení virtuální sítě jsou zobrazeny modrými šipkami na výše uvedeném obrázku. Potom vytvořte připojení virtuální sítě z virtuální sítě NVA k rozbočovači (černá šipka na obrázku).

 Pro tento krok můžete použít následující hodnoty:

| Název virtuální sítě| Název připojení|
| --- | --- |
| VNet1 | zkušební připojení1 |
| VNet2 | zkušební připojení2 |
| Síť NVAVNet | zkušební připojení3 |

Opakujte následující postup pro každou virtuální síť, kterou chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Chcete-li vytvořit připojení, klepněte na tlačítko **OK.**

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
