---
title: 'Kurz: vytvoření připojení ExpressRoute pomocí Azure Virtual WAN'
description: V tomto kurzu se naučíte používat Azure Virtual WAN k vytváření připojení ExpressRoute k prostředím Azure a místním prostředím.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 080136b8fc25b08a6b96464f0a61115a4bb2f3f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426603"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Kurz: vytvoření přidružení ExpressRoute pomocí Azure Virtual WAN

V tomto kurzu se dozvíte, jak pomocí virtuální sítě WAN se připojit k prostředkům v Azure přes okruh ExpressRoute. Další informace o virtuálních sítích WAN a virtuálních sítích WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření centra a brány
> * Připojení virtuální sítě k rozbočovači
> * Připojení okruhu k bráně centra
> * Test připojení
> * Změna velikosti brány
> * Inzerování výchozí trasy

## <a name="prerequisites"></a>Předpoklady

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Také se nemůže překrývat s rozsahy adres, ke kterým se připojujete místně. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Okruh ExpressRoute musí být okruhem Standard nebo Standard, aby se mohl připojit k bráně centra.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtual WAN. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte Enter.
2. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN kliknutím na **vytvořit** otevřete stránku vytvořit síť WAN.
3. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   ![Create WAN (Vytvořit síť WAN)](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Resource Group** (Skupina prostředků) – vytvořte novou nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Typ** – vyberte **standardní**. Nemůžete vytvořit ExpressRoute bránu pomocí základní SKU.
4. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
5. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Vytvoření virtuálního rozbočovače a brány

Virtuální rozbočovač je virtuální síť, kterou vytváří a používá virtuální síť WAN. Může obsahovat různé brány, například VPN a ExpressRoute. V této části vytvoříte bránu ExpressRoute pro virtuální rozbočovač. Bránu můžete vytvořit buď při [vytváření nového virtuálního rozbočovače](#newhub), nebo můžete bránu vytvořit v [existujícím centru](#existinghub) úpravou. 

Brány ExpressRoute se zřídí v jednotkách 2 GB/s. 1 jednotka škálování = 2 GB/s podporuje až 10 jednotek škálování = 20 GB/s. Vytvoření virtuálního rozbočovače a brány může trvat přibližně 30 minut.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Vytvoření nového virtuálního rozbočovače a brány

Vytvoří nové virtuální centrum. Po vytvoření centra se vám bude účtovat centrum, i když nepřipojíte žádné weby.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Vytvoření brány v existujícím centru

Bránu můžete vytvořit také v existujícím centru úpravou.

1. Přejděte do virtuálního centra, které chcete upravit, a vyberte ho.
2. Na stránce **Upravit virtuální rozbočovač** zaškrtněte políčko **Zahrnout bránu ExpressRoute**.
3. Vyberte **Potvrdit** a potvrďte provedené změny. Vytvoření prostředků rozbočovače a centra bude trvat přibližně 30 minut.

   ![existující centrum](./media/virtual-wan-expressroute-portal/edithub.png "Úprava centra")

### <a name="to-view-a-gateway"></a>Zobrazení brány

Jakmile vytvoříte bránu ExpressRoute, můžete zobrazit podrobnosti o bráně. Přejděte do centra, vyberte **ExpressRoute** a podívejte se na bránu.

![Zobrazit bránu](./media/virtual-wan-expressroute-portal/viewgw.png "Zobrazit bránu")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Připojení virtuální sítě k centru

V této části vytvoříte připojení partnerských vztahů mezi centrem a virtuální sítí. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Virtual network connection** (Připojení k virtuální síti).
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nemůže mít již existující bránu virtuální sítě (ani VPN ani ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Připojení okruhu k bráně centra

Po vytvoření brány můžete k ní připojit [okruh ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) . ExpressRoute úrovně Standard nebo Premium, které jsou v ExpressRoute Global Reach – podporovaná umístění se můžou připojit k virtuální síti WAN ExpressRoute Gateway a využívat všechny možnosti služby Virtual WAN pro přenos dat (VPN-to-VPN, VPN a ExpressRoute Transit). ExpressRoute úrovně Standard a Premium, které se nacházejí v neGlobal Reachch umístěních, se můžou připojit k prostředkům Azure, ale nebudou moct využívat možnosti služby Virtual WAN pro přenos. ExpressRoute Local se podporuje u rozbočovačů Azure Virtual WAN, pokud je paprskový virtuální sítě připojený k virtuálnímu rozbočovači WAN ve stejné oblasti jako virtuální centrum sítě WAN.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Připojení okruhu k bráně centra

Na portálu otevřete stránku **virtuální rozbočovač – > připojení – > ExpressRoute** . Pokud máte ve svém předplatném přístup k okruhu ExpressRoute, v seznamu okruhů se zobrazí okruh, který chcete použít. Pokud nevidíte žádné okruhy, ale byly k dispozici autorizační klíč a identifikátor URI rovnocenného okruhu, můžete uplatnit a propojit okruh. Další informace najdete v tématu [připojení pomocí autorizačního klíče](#authkey).

1. Vyberte okruh.
2. Vyberte **připojit okruhy**.

   ![připojit okruhy](./media/virtual-wan-expressroute-portal/cktconnect.png "připojit okruhy")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Připojení pomocí autorizačního klíče

Použijte autorizační klíč a identifikátor URI okruhu, který jste zadali za účelem připojení.

1. Na stránce ExpressRoute klikněte na **+ uplatnit autorizační klíč** .

   ![Snímek obrazovky zobrazuje ExpressRoute pro virtuální rozbočovač s vybraným autorizačním klíčem uplatnit.](./media/virtual-wan-expressroute-portal/redeem.png "uplatnit")
2. Na stránce autorizační klíč uplatnit zadejte hodnoty.

   ![hodnoty klíčů pro uplatnění](./media/virtual-wan-expressroute-portal/redeemkey2.png "hodnoty klíčů pro uplatnění")
3. Vyberte **Přidat** a přidejte klíč.
4. Zobrazit okruh. Provedený okruh zobrazuje pouze název (bez typu, poskytovatele a další informace), protože je v jiném předplatném než uživatel.

## <a name="to-test-connectivity"></a>Testování připojení

Po navázání připojení okruhu bude stav připojení centra označovat "Toto centrum", což znamená, že připojení k bráně ExpressRoute hub je navázáno. Počkejte přibližně 5 minut, než otestujete připojení od klienta za vaším okruhem ExpressRoute, například virtuální počítač ve virtuální síti, kterou jste vytvořili dříve.

Pokud máte weby připojené k virtuální síti WAN VPN Gateway ve stejném centru jako bránu ExpressRoute, můžete mít obousměrné připojení mezi koncovými body VPN a ExpressRoute. Dynamické směrování (BGP) je podporováno. ASN bran v centru jsou pevně dané a v tuto chvíli nelze upravovat.

## <a name="to-change-the-size-of-a-gateway"></a>Změna velikosti brány

Pokud chcete změnit velikost brány ExpressRoute, vyhledejte v centru ExpressRoute bránu a v rozevíracím seznamu vyberte jednotky škálování. Uložte změnu. Aktualizace brány centra bude trvat přibližně 30 minut.

![změnit velikost brány](./media/virtual-wan-expressroute-portal/changescale.png "změnit velikost brány")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Inzerování výchozí trasy 0.0.0.0/0 koncovým bodům

Pokud chcete, aby virtuální rozbočovač Azure inzeroval výchozí trasu 0.0.0.0/0 do ExpressRoute koncových bodů, budete muset povolit možnost "rozšířit výchozí trasu".

1. Vyberte **> okruhu...-> upravit připojení**.

   ![Upravit připojení](./media/virtual-wan-expressroute-portal/defaultroute1.png "Upravit připojení")

2. Vyberte **Povolit** pro rozšíření výchozí trasy.

   ![Rozšířit výchozí trasu](./media/virtual-wan-expressroute-portal/defaultroute2.png "Rozšířit výchozí trasu")

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte je. Některé virtuální prostředky sítě WAN je potřeba z důvodu závislostí odstranit v určitém pořadí. Dokončení odstranění může trvat přibližně 30 minut.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete tady:

> [!div class="nextstepaction"]
> * [Nejčastější dotazy ke službě Virtual WAN](virtual-wan-faq.md)
