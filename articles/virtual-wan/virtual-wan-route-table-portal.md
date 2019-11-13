---
title: 'Virtuální síť WAN: vytvoření tabulky směrování virtuálního rozbočovače do síťové virtuální zařízení: Azure Portal'
description: Tabulka směrování virtuálních rozbočovačů sítě WAN pro řízení provozu do síťového virtuálního zařízení pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014987"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Vytvoření směrovací tabulky pro virtuální síť WAN pro síťová virtuální zařízení: Azure Portal

V tomto článku se dozvíte, jak řídit provoz z rozbočovače na síťové virtuální zařízení (síťové virtuální zařízení).

![Diagram služby Virtual WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Než začnete

Ověřte, že splňujete následující kritéria:

*  Máte síťové virtuální zařízení (síťové virtuální zařízení). Síťové virtuální zařízení je software od jiného výrobce, který je obvykle zřízený z Azure Marketplace ve virtuální síti.

    * Privátní IP adresa musí být přiřazena k síťové virtuální zařízení síťovému rozhraní.

    * SÍŤOVÉ virtuální zařízení není nasazený ve virtuálním centru. Musí být nasazené v samostatné virtuální síti.

    *  Virtuální síť síťové virtuální zařízení může mít připojenou jednu nebo více virtuálních sítí. V tomto článku odkazujeme na virtuální síť síťové virtuální zařízení jako "nepřímá virtuální síť rozbočovače". Tyto virtuální sítě můžou být připojené k virtuální síti síťové virtuální zařízení pomocí partnerského vztahu virtuálních sítí.
*  Vytvořili jste 2 virtuální sítě. Budou použity jako paprskový virtuální sítě.

    * Pro toto cvičení jsou adresní prostory virtuálních sítí ve virtuální síti: VNet1:10.0.2.0/24 a VNet2:10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md).

    * Zajistěte, aby v žádném z virtuální sítě neexistovaly žádné brány virtuální sítě.
    * Pro tuto konfiguraci tyto virtuální sítě nevyžadují podsíť brány.

## <a name="signin"></a>1. přihlášení

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

## <a name="vwan"></a>2. vytvoření virtuální sítě WAN

Vytvořte virtuální síť WAN. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Název virtuální sítě WAN:** myVirtualWAN
* **Skupina prostředků:** testRG
* **Umístění:** Západní USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. vytvoření centra

Vytvořte centrum. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Umístění:** Západní USA
* **Název:** westushub
* **Privátní adresní prostor centra:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. vytvoření a použití tabulky směrování centra

Aktualizujte centrum s tabulkou směrování na rozbočovači. Pro účely tohoto cvičení můžete použít následující hodnoty:

* **Adresní prostory nepřímých propojení paprsků:** (VNet1 a VNet2) 10.0.2.0/24 a 10.0.3.0/24
* **Privátní IP adresa síťového rozhraní DMZ síťové virtuální zařízení:** 10.0.4.5

1. Přejděte k virtuální síti WAN.
2. Klikněte na centrum, pro které chcete vytvořit směrovací tabulku.
3. Klikněte na **...** a pak klikněte na **Upravit virtuální rozbočovač**.
4. Na stránce **Upravit virtuální rozbočovač** přejděte dolů a zaškrtněte políčko **použít tabulku pro směrování**.
5. Do sloupce **předpona cílového umístění** přidejte adresní prostory. Do sloupce **Odeslat do dalšího směrování** přidejte privátní IP adresu síťového rozhraní DMZ síťové virtuální zařízení.
6. Kliknutím na **Potvrdit** aktualizujte prostředek centra pomocí nastavení směrovací tabulky.

## <a name="connections"></a>5. vytvoření připojení virtuální sítě

Vytvořte připojení z každé nepřímo virtuální sítě rozbočovače (VNet1 a VNet2) do centra. Pak vytvořte připojení z virtuální sítě síťové virtuální zařízení k centru.

 Pro tento krok můžete použít následující hodnoty:

| Název virtuální sítě| Název připojení|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Pro každou virtuální síť, ke které se chcete připojit, zopakujte následující postup.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Kliknutím na tlačítko **OK** vytvořte připojení.

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).