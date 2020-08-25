---
title: Kurz – konfigurace sítě pro privátní cloud VMware v Azure
description: Naučte se vytvářet a konfigurovat sítě potřebné k nasazení privátního cloudu v Azure.
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: ff071e0d6eaf1552634433a76e4eade530c603b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750503"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Kurz: konfigurace sítě pro privátní cloud VMware v Azure

Privátní cloud řešení Azure VMware vyžaduje Azure Virtual Network. Vzhledem k tomu, že řešení Azure VMware v rámci verze Preview nepodporuje vaše místní vCenter, je potřeba provést další kroky pro integraci s místním prostředím. Nastavení okruhu ExpressRoute a brány virtuální sítě jsou také vyžadovány a jsou pokryty v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány virtuální sítě
> * Připojení okruhu ExpressRoute k bráně
> * Vyhledání adres URL pro vCenter a NSX Manager

## <a name="prerequisites"></a>Předpoklady 
Než budete moct vytvořit virtuální síť, ujistěte se, že jste vytvořili [privátní cloud řešení Azure VMware](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

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
   | **Předplatné** | Tato hodnota se už naplní předplatným, ke kterému patří skupina prostředků. |
   | **Skupina prostředků** | Tato hodnota je pro aktuální skupinu prostředků již vyplněna. Mělo by se jednat o skupinu prostředků, kterou jste vytvořili v předchozím testu. |
   | **Name** (Jméno) | Zadejte jedinečný název pro bránu virtuální sítě. |
   | **Oblast** | Vyberte zeměpisnou polohu brány virtuální sítě. |
   | **Typ brány** | Vyberte **ExpressRoute**. |
   | **Skladová jednotka (SKU)** | Ponechte výchozí hodnotu: **Standard**. |
   | **Virtuální síť** | Vyberte virtuální síť, kterou jste předtím vytvořili. Pokud virtuální síť nevidíte, ujistěte se, že oblast brány odpovídá oblasti vaší virtuální sítě. |
   | **Rozsah adres podsítě brány** | Tato hodnota se naplní při výběru virtuální sítě. Neměňte výchozí hodnotu. |
   | **Veřejná IP adresa** | Vyberte, že chcete **vytvořit novou** IP adresu. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Na kartě základy stránky vytvořit bránu virtuální sítě zadejte hodnoty pro pole a pak vyberte zkontrolovat + vytvořit." border="true":::

1. Ověřte správnost podrobností a výběrem **vytvořit** spusťte nasazení brány virtuální sítě. 
1. Až se nasazení dokončí, přejděte k další části a připojte připojení ExpressRoute k bráně virtuální sítě, která obsahuje váš privátní cloud řešení Azure VMware.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Připojení ExpressRoute k bráně virtuální sítě

Teď, když jste nasadili bránu virtuální sítě, přidáte mezi ně připojení a privátní cloud řešení Azure VMware.

1. Přejděte do privátního cloudu, který jste vytvořili v předchozím kurzu, a v části **Spravovat**vyberte **připojení** . Vyberte kartu **ExpressRoute** .

1. Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, budete ho muset vytvořit, abyste mohli vybrat **+ požádat o autorizační klíč**.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, budete ho muset vytvořit, abyste mohli vybrat + požádat o autorizační klíč." border="true":::

1. Přejděte do Virtual Network brány, kterou jste vytvořili v předchozím kroku, a v části **Nastavení**vyberte **připojení**. Na stránce **připojení** vyberte **+ Přidat**.

1. Na stránce **Přidat připojení** zadejte hodnoty pro pole a vyberte **OK**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Název**  | Zadejte název tohoto připojení.  |
   | **Typ připojení**:  | Vyberte **ExpressRoute**.  |
   | **Uplatnit autorizaci**  | Ujistěte se, že je toto políčko zaškrtnuté.  |
   | **Brána virtuální sítě** | Brána Virtual Network, kterou jste předtím vytvořili.  |
   | **Autorizační klíč**  | Zkopírujte autorizační klíč a vložte ho z karty ExpressRoute pro vaši skupinu prostředků. |
   | **Identifikátor URI rovnocenného okruhu**  | Zkopírujte a vložte ID ExpressRoute z karty ExpressRoute pro vaši skupinu prostředků.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Na stránce Přidat připojení zadejte hodnoty pro pole a vyberte OK." border="true":::

Vytvoří se připojení mezi okruhem ExpressRoute a vaším Virtual Network.



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

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit můstek, který se používá k připojení k vašemu prostředí, abyste mohli místně spravovat svůj privátní cloud.

> [!div class="nextstepaction"]
> [Přístup k privátnímu cloudu](tutorial-access-private-cloud.md)
