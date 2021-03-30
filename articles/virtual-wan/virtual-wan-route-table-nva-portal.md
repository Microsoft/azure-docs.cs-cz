---
title: 'Virtuální síť WAN: vytvoření tabulky směrování virtuálního rozbočovače do síťové virtuální zařízení: Azure Portal'
description: Tabulka směrování virtuálních rozbočovačů sítě WAN pro řízení provozu do síťového virtuálního zařízení pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267138"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Vytvoření směrovací tabulky pro virtuální síť WAN pro síťová virtuální zařízení: Azure Portal

V tomto článku se dozvíte, jak řídit provoz z větve (místní lokality) připojených k virtuální síti WAN ke službě Virtual Network (VNet) prostřednictvím síťového virtuálního zařízení (síťové virtuální zařízení).

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Než začnete

Ověřte, že splňujete následující kritéria:

*  Máte síťové virtuální zařízení (síťové virtuální zařízení). Síťové virtuální zařízení je software od jiného výrobce, který je obvykle zřízený z Azure Marketplace ve virtuální síti.

    * Privátní IP adresa musí být přiřazena k síťové virtuální zařízení síťovému rozhraní.

    * SÍŤOVÉ virtuální zařízení není nasazený ve virtuálním centru. Musí být nasazena v samostatné virtuální síti.

    *  Virtuální síť síťové virtuální zařízení může mít připojenu jednu nebo více virtuálních sítí. V tomto článku odkazujeme na virtuální síť síťové virtuální zařízení jako na "nepřímý virtuální síť rozbočovače". Tyto virtuální sítě se dají připojit k virtuální síti síťové virtuální zařízení pomocí partnerského vztahu virtuálních sítí. Odkazy partnerských vztahů virtuálních sítí jsou znázorněny pomocí černých šipek na výše uvedeném obrázku mezi virtuální sítí VNet 1, VNet 2 a síťové virtuální zařízení VNet.
*  Vytvořili jste dvě virtuální sítě. Budou použity jako paprskový virtuální sítě.

    * Adresní prostory virtuální sítě jsou: VNet1:10.0.2.0/24 a VNet2:10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si téma [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md).

    * Zajistěte, aby v žádném z virtuální sítě neexistovaly žádné brány virtuální sítě.

    * Virtuální sítě nepotřebuje podsíť brány.

## <a name="1-sign-in"></a><a name="signin"></a>1. přihlášení

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. vytvoření virtuální sítě WAN

Vytvořte virtuální síť WAN. Použijte následující příklady hodnot:

* **Název virtuální sítě WAN:** myVirtualWAN
* **Skupina prostředků:** testRG
* **Umístění:** Západní USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. vytvoření centra

Vytvořte centrum. Použijte následující příklady hodnot:

* **Umístění:** Západní USA
* **Název:** westushub
* **Privátní adresní prostor centra:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. vytvoření a použití tabulky směrování centra

Aktualizujte centrum s tabulkou směrování na rozbočovači. Použijte následující příklady hodnot:

* **Adresní prostory virtuální sítě-paprsek:** (VNet1 a VNet2) 10.0.2.0/24 a 10.0.3.0/24
* **Privátní IP adresa síťového rozhraní DMZ síťové virtuální zařízení:** 10.0.4.5

1. Přejděte k virtuální síti WAN.
2. Klikněte na centrum, pro které chcete vytvořit směrovací tabulku.
3. Klikněte na **...** a pak klikněte na **Upravit virtuální rozbočovač**.
4. Na stránce **Upravit virtuální rozbočovač** přejděte dolů a zaškrtněte políčko **použít tabulku pro směrování**.
5. Do sloupce **předpona cílového umístění** přidejte adresní prostory. Do sloupce **Odeslat do dalšího směrování** přidejte privátní IP adresu síťového rozhraní DMZ síťové virtuální zařízení.

   > [!NOTE]
   > Síť síťové virtuální zařízení DMZ se vztahuje na místní centrum.
   
6. Kliknutím na **Potvrdit** aktualizujte prostředek centra pomocí nastavení směrovací tabulky.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. vytvoření připojení virtuální sítě

Vytvořte připojení k virtuální síti z každé nepřímo virtuální sítě rozbočovače (VNet1 a VNet2) do centra. Tato připojení k virtuální síti jsou znázorněna modrou šipkou na výše uvedeném obrázku. Pak vytvořte připojení virtuální sítě z virtuální sítě síťové virtuální zařízení do centra (černá šipka na obrázku).

 Pro tento krok můžete použít následující hodnoty:

| Název virtuální sítě| Název připojení|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Pro každou virtuální síť, kterou chcete připojit, opakujte následující postup.

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
