---
title: 'Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak vytvořit a nakonfigurovat směrovací server pomocí Azure Portal.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547984"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure Portal

Tento článek vám pomůže nakonfigurovat partnerský server Azure tak, aby se ve vaší virtuální síti síťové virtuální zařízení (Network Virtual disk) ve vaší virtuální síti používal Azure Portal. Azure Route Server se dozví o trasách z síťové virtuální zařízení a jejich programování na virtuálních počítačích ve virtuální síti. Směrovací server Azure taky inzeruje trasy virtuální sítě do síťové virtuální zařízení. Další informace najdete v tématu [Azure Route Server](overview.md).

> [!IMPORTANT]
> Služba Azure Route Server (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zkontrolujte [omezení služby pro Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Vytvoření směrovacího serveru

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="create-a-route-server"></a>Vytvoření směrovacího serveru

1. Přejděte na https://aka.ms/routeserver.

1. Vyberte **+ vytvořit nový server trasy**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Snímek cílové stránky serveru Route"::: 

1. Na stránce **vytvořit server směrování** zadejte nebo vyberte požadované informace.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Snímek stránky pro vytvoření serveru tras":::     

    | Nastavení | Hodnota |
    |----------|-------|
    | Předplatné | Vyberte předplatné Azure, které chcete použít k nasazení serveru směrování. |
    | Skupina prostředků | Vyberte skupinu prostředků, ve které chcete vytvořit server směrování. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou. |
    | Name | Zadejte název serveru tras. |
    | Oblast | Vyberte oblast, ve které bude server trasy vytvořen. Vyberte stejnou oblast jako virtuální síť, kterou jste dříve vytvořili pro zobrazení virtuální sítě v rozevíracím seznamu. |
    | Virtual Network | Vyberte virtuální síť, ve které bude server trasy vytvořen. Můžete vytvořit novou virtuální síť nebo použít stávající virtuální síť. Pokud používáte existující virtuální síť, ujistěte se, že má stávající virtuální síť dostatek místa pro minimální velikost podsítě/27, aby odpovídala požadavku na podsíť serveru směrování. Pokud se vaše virtuální síť v rozevíracím seznamu nezobrazuje, ujistěte se, že jste vybrali správnou skupinu prostředků nebo oblast. |
    | Podsíť | Po vytvoření nebo výběru virtuální sítě se zobrazí pole podsíť. Tato podsíť je vyhrazená jenom pro směrování serveru. Vyberte **spravovat konfiguraci podsítě** a vytvořte podsíť Azure Route serveru. Vyberte **+ podsíť** a vytvořte podsíť pomocí následujících pokynů:</br><br>– Podsíť musí mít název *RouteServerSubnet*.</br><br>-Podsíť musí být minimálně/27 nebo větší.</br> |

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte souhrn a pak vyberte **vytvořit**. 

    > [!NOTE]
    > Nasazení serveru tras bude trvat přibližně 20 minut.

## <a name="set-up-peering-with-nva"></a>Nastavení partnerského vztahu s síťové virtuální zařízení

Oddíl vám pomůže nakonfigurovat partnerský vztah protokolu BGP s vaším síťové virtuální zařízení.

1. V Azure Portal přejít na [Server směrování](https://aka.ms/routeserver) a vyberte server směrování, který chcete nakonfigurovat.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Snímek obrazovky se seznamem serveru směrování."::: 

1. V části *Nastavení* v levém navigačním panelu vyberte **partnerské uzly** . Pak vyberte **+ Přidat** a přidejte nového partnera.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Snímek cílové stránky partnerských uzlů"::: 

1. Zadejte následující informace o partnerském uzlu síťové virtuální zařízení.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Snímek obrazovky s přidáním partnerské stránky":::

    | Nastavení | Hodnota |
    |----------|-------|
    | Název | Zadejte název partnerského vztahu mezi serverem tras a síťové virtuální zařízení. |
    | ASN |  Zadejte číslo autonomního systému (ASN) vašeho síťové virtuální ZAŘÍZENÍu. |
    | Adresa IPv4 | Zadejte IP adresu síťové virtuální zařízení, se kterou bude server trasy komunikovat, aby navázal protokol BGP. |

1. Pokud chcete přidat tohoto partnera, vyberte **Přidat** .

## <a name="complete-the-configuration-on-the-nva"></a>Dokončete konfiguraci na síťové virtuální zařízení

K dokončení konfigurace síťové virtuální zařízení pro vytvoření relace protokolu BGP budete potřebovat partnerské adresy IP a ASN serveru Směrování Azure. Tyto informace můžete získat na stránce Přehled na serveru tras.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Snímek stránky s přehledem serveru Směrování":::

## <a name="configure-route-exchange"></a>Konfigurace výměny trasy

Máte-li bránu brány ExpressRoute nebo bránu VPN a chcete, aby vyměňovat trasy k serveru tras, můžete povolit směrování.

1. V Azure Portal přejít na [Server směrování](https://aka.ms/routeserver) a vyberte server směrování, který chcete nakonfigurovat.

1. V části *Nastavení* v levém navigačním panelu vyberte **Konfigurace** .

1. Vyberte **Povolit** pro nastavení vytvoření **větve** a potom vyberte **Uložit**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Snímek obrazovky s postupem, jak povolit výměnu tras":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už server tras Azure nepotřebujete, vyberte **Odstranit** na stránce Přehled a zrušte zřízení serveru směrování.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Snímek obrazovky s postupem odstranění serveru Směrování":::

## <a name="next-steps"></a>Další kroky

Po vytvoření serveru Směrování Azure si přečtěte informace o tom, jak server tras Azure komunikuje s ExpressRoute a branami VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute a podpora Azure VPN](expressroute-vpn-support.md)
