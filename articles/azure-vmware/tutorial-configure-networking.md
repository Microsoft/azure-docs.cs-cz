---
title: Kurz – konfigurace sítě pro privátní cloud VMWare v Azure
description: Naučte se vytvářet a konfigurovat sítě potřebné k nasazení privátního cloudu v Azure.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: e8278eb3ab5e99d330e34fd9739ed2bc49aa7f08
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740210"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Kurz: konfigurace sítě pro privátní cloud VMWare v Azure

Privátní cloud řešení Azure VMware (AVS) vyžaduje virtuální síť. Protože aplikace AVS v rámci verze Preview nepodporuje vaše místní vCenter, je potřeba provést další kroky pro integraci s místním prostředím. Nastavení okruhu ExpressRoute a Virtual Network brány jsou také potřeba a budou řešeny v tomto kurzu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření Virtual Network brány
> * Připojení okruhu ExpressRoute k bráně
> * Vyhledání adres URL pro vCenter a NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://rc.portal.azure.com).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Přejděte do skupiny prostředků, kterou jste vytvořili v [předchozím kurzu](tutorial-create-private-cloud.md), a vyberte **+ Přidat** k definování nového prostředku.

Do textového pole **Hledat na Marketplace** zadejte **Virtual Network**. Vyhledejte prostředek Virtual Network a vyberte ho.

Na stránce Virtual Network vyberte **vytvořit** a nastavte svou virtuální síť pro svůj privátní cloud.

Na stránce **vytvořit Virtual Network** zadejte příslušné podrobnosti o vaší virtuální síti. Popis vlastností se zobrazí v následující tabulce:

> [!IMPORTANT]
> Je nutné použít adresní **prostor, který** se nepřekrývá s adresním prostorem, který jste použili při vytváření privátního cloudu v předchozím kurzu.

Na kartě **základy** zadejte název virtuální sítě a vyberte příslušnou oblast a vyberte **Další: IP adresy.**

Na kartě **IP adresy** v části **adresní prostor IPv4**zadejte adresní prostor, který jste vytvořili v předchozím kurzu.

Vyberte **+ Přidat podsíť**a na stránce **Přidat podsíť** zadejte název podsítě a příslušný rozsah adres. Po dokončení vyberte **Add** (Přidat).

Vybrat **kontrolu + vytvořit**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="vytvoření virtuální sítě" border="true":::

Ověřte informace a vyberte **vytvořit**. Po dokončení nasazení se ve skupině prostředků zobrazí vaše virtuální síť.

## <a name="create-a-virtual-network-gateway"></a>Vytvoření Virtual Network brány

Vytvořili jste virtuální síť v předchozí části. teď vytvoříte bránu Virtual Network.

V rámci skupiny prostředků vyberte **+ Přidat** a přidejte nový prostředek.

Do textového pole **Hledat na Marketplace** zadejte **virtuální síťová brána**. Vyhledejte prostředek Virtual Network a vyberte ho.

Na stránce **Virtual Network brána** vyberte **vytvořit**.

Na kartě základy stránky **vytvořit bránu virtuální sítě** zadejte hodnoty pro pole. popisy polí jsou uvedeny v následující tabulce:

| Pole | Hodnota |
| --- | --- |
| **Předplatné** | Tato hodnota se už naplní předplatným, ke kterému patří skupina prostředků. |
| **Skupina prostředků** | Tato hodnota je pro aktuální skupinu prostředků již vyplněna. Mělo by se jednat o skupinu prostředků, kterou jste vytvořili v předchozím testu. |
| **Název** | Zadejte jedinečný název pro bránu virtuální sítě. |
| **Oblast** | Vyberte zeměpisnou polohu brány virtuální sítě. |
| **Typ brány** | Vyberte **ExpressRoute**. |
| **Typ sítě VPN** | Vyberte **směrování založené na trasách**. |
| **SKLADOVÉ** | Ponechte výchozí hodnotu: **Standard**. |
| **Virtuální síť** | Vyberte virtuální síť, kterou jste předtím vytvořili. Pokud virtuální síť nevidíte, ujistěte se, že oblast brány odpovídá oblasti vaší virtuální sítě. |
| **Rozsah adres podsítě brány** | Tato hodnota se naplní při výběru virtuální sítě. Neměňte výchozí hodnotu. |
| **Veřejná IP adresa** | Vyberte, že chcete **vytvořit novou** IP adresu. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Vytvoření brány" border="true":::

Vyberte **zkontrolovat + vytvořit**, na další stránce Ověřte správnost podrobností a výběrem **vytvořit** spusťte nasazení brány virtuální sítě. Až se nasazení dokončí, přejděte k další části v tomto kurzu, abyste připojili připojení ExpressRoute k virtuální síti, která obsahuje váš privátní cloud.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Připojení ExpressRoute k bráně Virtual Network

V této části se dozvíte, jak přidat propojení mezi Vaším privátním cloudem služby AVS a branou virtuální sítě, kterou jste vytvořili.

Přejděte do privátního cloudu, který jste vytvořili v předchozím kurzu, a v části **Spravovat**vyberte **připojení** . Vyberte kartu **ExpressRoute** .

Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, budete ho muset vytvořit, abyste mohli vybrat **+ požádat o autorizační klíč** .

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="požádat o autorizační klíč" border="true":::

Přejděte do Virtual Network brány, kterou jste vytvořili v předchozím kroku, a v části **Nastavení**vyberte **připojení**. Na stránce **připojení** vyberte **+ Přidat**.

Na stránce **Přidat připojení** zadejte hodnoty pro pole. Popisy polí jsou uvedeny v následující tabulce:

| Pole | Hodnota |
| --- | --- |
| **Název**  | Zadejte název tohoto připojení.  |
| **Typ připojení**  | Vyberte **ExpressRoute**.  |
| **Uplatnit autorizaci**  | Ujistěte se, že je toto políčko zaškrtnuté.  |
| **Brána virtuální sítě** | Brána virtuální sítě, kterou jste vytvořili dříve  |
| **Autorizační klíč**  | Zkopírujte autorizační klíč a vložte ho z karty ExpressRoute pro vaši skupinu prostředků. |
| **Identifikátor URI rovnocenného okruhu**  | Zkopírujte a vložte ID ExpressRoute z karty ExpressRoute pro vaši skupinu prostředků.  |

Vyberte **OK**. Tím se vytvoří propojení mezi okruhem ExpressRoute a vaší virtuální sítí.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Přidat připojení" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Vyhledání adres URL pro vCenter a NSX Manager

Pokud se chcete přihlásit k vVenter a NSX Manageru, budete potřebovat adresy URL webového klienta vCenter a webu NSX-T Manager. Vyhledání adres URL:

Přejděte do privátního cloudu služby AVS, v části **Spravovat**vyberte **Identita**, tady najdete potřebné informace.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="vyhledat adresy URL vCenter" border="true":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření Virtual Network brány
> * Připojení okruhu ExpressRoute k bráně
> * Vyhledání adres URL pro vCenter a NSX Manager

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit můstek, který se používá k připojení k vašemu prostředí, abyste mohli místně spravovat svůj privátní cloud.

> [!div class="nextstepaction"]
> [Přístup k privátnímu cloudu](tutorial-access-private-cloud.md)