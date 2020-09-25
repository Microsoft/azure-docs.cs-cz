---
title: Kurz – konfigurace sítě pro privátní cloud VMware v Azure
description: Naučte se vytvářet a konfigurovat sítě potřebné k nasazení privátního cloudu v Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 503043391b717753ab054197508483827565ef39
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254770"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Kurz: konfigurace sítě pro privátní cloud VMware v Azure

Privátní cloud řešení Azure VMware vyžaduje Azure Virtual Network. Vzhledem k tomu, že řešení Azure VMware nepodporuje vaše místní vCenter, je potřeba provést další kroky pro integraci s místním prostředím. Vyžaduje se taky nastavení okruhu ExpressRoute a Brána virtuální sítě.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány virtuální sítě
> * Připojení okruhu ExpressRoute k bráně
> * Vyhledání adres URL pro vCenter a NSX Manager

## <a name="prerequisites"></a>Požadavky 
Virtuální síť, kterou jste vytvořili [privátní cloud řešení Azure VMware](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejděte do skupiny prostředků, kterou jste vytvořili v [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) , a vyberte **+ Přidat** k definování nového prostředku. 

1. Do textového pole **Hledat na Marketplace** zadejte **Virtual Network**. Vyhledejte prostředek Virtual Network a vyberte ho.

1. Na stránce **Virtual Network** vyberte **vytvořit** a nastavte svou virtuální síť pro svůj privátní cloud.

1. Na stránce **vytvořit Virtual Network** zadejte podrobnosti k vaší virtuální síti.

1. Na kartě **základy** zadejte název virtuální sítě a vyberte příslušnou oblast a vyberte **Další: IP adresy**.

1. Na kartě **IP adresy** v části **adresní prostor IPv4**zadejte adresní prostor, který jste vytvořili v předchozím kurzu.

   > [!IMPORTANT]
   > Je nutné použít adresní **prostor, který** se nepřekrývá s adresním prostorem, který jste použili při vytváření privátního cloudu v předchozím kurzu.

1. Vyberte **+ Přidat podsíť**a na stránce **Přidat podsíť** zadejte název podsítě a příslušný rozsah adres. Po dokončení vyberte **Add** (Přidat).

1. Vyberte **Zkontrolovat a vytvořit**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Vyberte zkontrolovat + vytvořit." border="true":::

1. Ověřte informace a vyberte **vytvořit**. Po dokončení nasazení se ve skupině prostředků zobrazí vaše virtuální síť.

## <a name="create-a-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

Teď, když jste vytvořili virtuální síť, vytvoříte bránu virtuální sítě.

1. V rámci skupiny prostředků vyberte **+ Přidat** a přidejte nový prostředek.

1. Do textového pole **Hledat na Marketplace** zadejte **virtuální síťová brána**. Vyhledejte prostředek Virtual Network a vyberte ho.

1. Na stránce **Virtual Network brána** vyberte **vytvořit**.

1. Na kartě základy stránky **vytvořit bránu virtuální sítě** zadejte hodnoty pro pole a pak vyberte **zkontrolovat + vytvořit**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Předplatné** | Předem vyplněná hodnota s předplatným, ke kterému patří skupina prostředků. |
   | **Skupina prostředků** | Předem vyplněná hodnota pro aktuální skupinu prostředků Hodnota by měla být skupina prostředků, kterou jste vytvořili v předchozím testu. |
   | **Název** | Zadejte jedinečný název pro bránu virtuální sítě. |
   | **Oblast** | Vyberte zeměpisnou polohu brány virtuální sítě. |
   | **Typ brány** | Vyberte **ExpressRoute**. |
   | **Skladová jednotka (SKU)** | Ponechte výchozí hodnotu: **Standard**. |
   | **Virtuální síť** | Vyberte virtuální síť, kterou jste předtím vytvořili. Pokud virtuální síť nevidíte, ujistěte se, že oblast brány odpovídá oblasti vaší virtuální sítě. |
   | **Rozsah adres podsítě brány** | Tato hodnota se naplní při výběru virtuální sítě. Neměňte výchozí hodnotu. |
   | **Veřejná IP adresa** | Vyberte, že chcete **vytvořit novou** IP adresu. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Zadejte hodnoty pro pole a pak vyberte zkontrolovat + vytvořit." border="true":::

1. Ověřte správnost podrobností a výběrem **vytvořit** spusťte nasazení brány virtuální sítě. 
1. Až se nasazení dokončí, přejděte k další části a připojte připojení ExpressRoute k bráně virtuální sítě, která obsahuje váš privátní cloud řešení Azure VMware.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Připojení ExpressRoute k bráně virtuální sítě

Teď, když jste nasadili bránu virtuální sítě, přidáte mezi ně připojení a privátní cloud řešení Azure VMware.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-to-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Vyhledání adres URL pro vCenter a NSX Manager

K přihlášení do vCenter a NSX Manager budete potřebovat adresy URL webového klienta vCenter a webu NSX-T Manager. 

Přejděte do privátního cloudu řešení Azure VMware. v části **Spravovat**vyberte **Identita**, tady najdete potřebné informace.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Přejděte do privátního cloudu řešení Azure VMware. v části Spravovat vyberte identita, tady najdete potřebné informace." border="true":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány virtuální sítě
> * Připojení okruhu ExpressRoute k bráně
> * Vyhledání adres URL pro vCenter a NSX Manager

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit segmenty sítě NSX-T, které se používají pro virtuální počítače v vCenter.

> [!div class="nextstepaction"]
> [Vytvoření segmentu sítě NSX-T](tutorial-nsx-t-network-segment.md)