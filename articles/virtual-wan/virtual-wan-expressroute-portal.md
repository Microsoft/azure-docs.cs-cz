---
title: Kurz – vytvoření připojení ExpressRoute pomocí azure virtuální sítě WAN
description: V tomto kurzu se dozvíte, jak pomocí azure virtuální sítě WAN vytvořit připojení ExpressRoute k Azure a místním prostředím.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209422"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Kurz: Vytvoření přidružení ExpressRoute pomocí azure virtuální sítě WAN

Tento kurz ukazuje, jak používat virtuální WAN pro připojení k prostředkům v Azure přes okruh ExpressRoute. Další informace o prostředcích virtuální sítě WAN a virtuální sítě WAN naleznete v [tématu Přehled virtuální sítě WAN](virtual-wan-about.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření rozbočovače a brány
> * Připojení virtuální sítě k rozbočovači
> * Připojení okruhu k bráně rozbočovače
> * Test připojení
> * Změna velikosti brány
> * Inzerce výchozí trasy

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, zda se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť na webu Azure Portal, přečtěte si [úvodní příručku](../virtual-network/quick-create-portal.md).

* Virtuální síť nemá žádné brány virtuální sítě. Pokud vaše virtuální síť má bránu (vpn nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby virtuální sítě byly připojeny místo toho k bráně centra Virtuální WAN.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť, která je vytvořena a používávirtuální WAN. Rozsah adres, který zadáte pro rozbočovač, se nemůže překrývat s žádnou z existujících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud nejste obeznámeni s rozsahy IP adres umístěnými v místní konfiguraci sítě, koordinujte je s někým, kdo vám tyto podrobnosti může poskytnout.

* Okruh ExpressRoute musí být obvod Premium pro připojení k bráně rozbočovače.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtuální wan. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální wan** a vyberte Enter.
2. Z výsledků vyberte **virtuální wan.** Na stránce Virtuální wan kliknutím na **Vytvořit** otevřete stránku Vytvořit wan.
3. Na stránce **Vytvořit wan** vyplňte na kartě **Základy** následující pole:

   ![Create WAN (Vytvořit síť WAN)](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Resource Group** (Skupina prostředků) – vytvořte novou nebo použijte existující.
   * **Umístění skupiny prostředků** – z rozevíracího souboru zvolte umístění prostředků. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – Zadejte název, který chcete volat do sítě WAN.
   * **Typ** - Vyberte **standardní**. Nelze vytvořit bránu ExpressRoute pomocí základní skladové položky.
4. Po vyplnění polí vyberte **Zkontrolovat +Vytvořit**.
5. Jakmile ověření projde, vyberte **Vytvořit** a vytvořte virtuální WAN.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Vytvoření virtuálního rozbočovače a brány

Virtuální rozbočovač je virtuální síť, která je vytvořena a používávirtuální WAN. Může obsahovat různé brány, například VPN a ExpressRoute. V této části vytvoříte bránu ExpressRoute pro virtuální rozbočovač. Bránu můžete vytvořit buď při [vytváření nového virtuálního rozbočovače](#newhub), nebo ji můžete vytvořit v [existujícím rozbočovači](#existinghub) úpravou. 

Brány ExpressRoute jsou zřazeny v jednotkách 2 Gb/s. 1 jednotka škálování = 2 Gb/s s podporou až 10 jednotek v měřítku = 20 Gb/s. Virtuální rozbočovač a brána se mohou plně vytvořit přibližně 30 minut.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Vytvoření nového virtuálního rozbočovače a brány

Vytvořte nové virtuální rozbočovač. Po vytvoření rozbočovače vám bude účtován poplatek za centrum, i když nepřipojíte žádné weby.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Vytvoření brány v existujícím rozbočovači

Bránu můžete také vytvořit v existujícím rozbočovači úpravou.

1. Přejděte do virtuálního rozbočovače, který chcete upravit, a vyberte ho.
2. Na stránce **Upravit virtuální rozbočovač** zaškrtněte políčko **Zahrnout bránu ExpressRoute**.
3. Chcete-li potvrdit změny, vyberte **potvrdit.** Trvá přibližně 30 minut, než se prostředky centra a centra plně vytvoří.

   ![stávající rozbočovač](./media/virtual-wan-expressroute-portal/edithub.png "úprava rozbočovače")

### <a name="to-view-a-gateway"></a>Zobrazení brány

Po vytvoření brány ExpressRoute můžete zobrazit podrobnosti brány. Přejděte do rozbočovače, vyberte **ExpressRoute**a zobrazte bránu.

![Zobrazit bránu](./media/virtual-wan-expressroute-portal/viewgw.png "zobrazit bránu")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Připojení virtuální sítě k rozbočovači

V této části vytvoříte připojení partnerského vztahu mezi rozbočovačem a virtuální sítí. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Virtual network connection** (Připojení k virtuální síti).
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nemůže mít již existující bránu virtuální sítě (ani VPN, ani ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Připojení okruhu k bráně rozbočovače

Po vytvoření brány k ní můžete připojit [okruh ExpressRoute.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Okruhy ExpressRoute Premium, které jsou v místech s podporou globálního dosahu ExpressRoute, se mohou připojit k bráně Virtual WAN ExpressRoute.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Připojení okruhu k bráně rozbočovače

Na portálu přejděte na stránku **Virtual hub -> Connectivity -> ExpressRoute.** Pokud máte přístup v předplatném okruhu ExpressRoute, uvidíte okruh, který chcete použít v seznamu obvodů. Pokud nevidíte žádné obvody, ale byly opatřeny autorizačním klíčem a identifikátorem URI okruhu rovnocenných dat, můžete jej uplatnit a připojit. Viz [Připojení uplatněním autorizačního klíče](#authkey).

1. Vyberte obvod.
2. Vyberte **Připojit obvody .**

   ![připojení obvodů](./media/virtual-wan-expressroute-portal/cktconnect.png "připojení obvodů")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Připojení uplatněním autorizačního klíče

Použijte autorizační klíč a identifikátor URI okruhu, který jste zadali k připojení.

1. Na stránce ExpressRoute klikněte na **+Uplatnit autorizační klíč.**

   ![Uplatnit](./media/virtual-wan-expressroute-portal/redeem.png "Uplatnit")
2. Na stránce Uplatnit autorizační klíč vyplňte hodnoty.

   ![uplatnit klíčové hodnoty](./media/virtual-wan-expressroute-portal/redeemkey2.png "uplatnit klíčové hodnoty")
3. Chcete-li přidat klíč, vyberte **Přidat.**
4. Prohlédněte si obvod. Uplatněný okruh zobrazuje pouze jméno (bez typu, zprostředkovatele a další informace), protože je v jiném předplatném než uživatel.

## <a name="to-test-connectivity"></a>Testování připojení

Po navázání připojení okruhu bude stav připojení rozbočovače označovat "tento rozbočovač", což znamená, že připojení je navázáno k bráně expressroute rozbočovače. Počkejte přibližně 5 minut, než otestujete připojení z klienta za okruhem ExpressRoute, například virtuální počítač ve virtuální síti, kterou jste vytvořili dříve.

Pokud máte weby připojené k bráně Virtuální SÍTĚ VPN ve stejném rozbočovači jako brána ExpressRoute, můžete mít obousměrné připojení mezi koncovými body VPN a ExpressRoute. Dynamické směrování (BGP) je podporováno. ASN bran v rozbočovači je pevná a nelze upravovat v tomto okamžiku.

## <a name="to-change-the-size-of-a-gateway"></a>Změna velikosti brány

Pokud chcete změnit velikost brány ExpressRoute, vyhledejte bránu ExpressRoute uvnitř rozbočovače a v rozevíracím souboru vyberte jednotky škálování. Uložte si drobné. Aktualizace brány rozbočovače bude trvat přibližně 30 minut.

![změna velikosti brány](./media/virtual-wan-expressroute-portal/changescale.png "změna velikosti brány")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Inzerce výchozí trasy 0.0.0.0/0 do koncových bodů

Pokud chcete, aby virtuální centrum Azure inzeroval výchozí trasu 0.0.0.0/0 do koncových bodů ExpressRoute, budete muset povolit "Propagovat výchozí trasu".

1. Vyberte **připojení Pro úpravy ->...->**.

   ![Upravit připojení](./media/virtual-wan-expressroute-portal/defaultroute1.png "Upravit připojení")

2. Vyberte **Povolit,** chcete-li šířit výchozí trasu.

   ![Šíření výchozí trasy](./media/virtual-wan-expressroute-portal/defaultroute2.png "Šíření výchozí trasy")

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
