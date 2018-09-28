---
title: Vytvoření připojení ExpressRoute k Azure a místním prostředím pomocí služby Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí služby Azure Virtual WAN vytvořit připojení ExpressRoute k Azure a místním prostředím.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporoate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 46a48c6e06f37968ab3f41b30d983f2664785811
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990548"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Kurz: Vytvoření přidružení ExpressRoute pomocí služby Azure Virtual WAN (Preview)

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure s využitím okruhu a přidružení ExpressRoute. Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření rozbočovače
> * Vyhledání a přidružení okruhu k rozbočovači
> * Přidružení okruhu k rozbočovači nebo rozbočovačům
> * Připojení virtuální sítě k rozbočovači
> * Zobrazení virtuální sítě WAN
> * Zobrazení stavu prostředků
> * Monitorování připojení

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]


## <a name="vnet"></a>1. Vytvoření virtuální sítě

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Stránka Začínáme

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Vytvoření rozbočovače

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Vyhledání a přidružení okruhu k rozbočovači

1. Vyberte svou virtuální síť WAN a v části **Architektura virtuální sítě WAN** vyberte **Okruhy ExpressRoute**.
2. Pokud je okruh ExpressRoute ve stejném předplatném jako vaše virtuální síť WAN, klikněte na **Vybrat okruh ExpressRoute** a vyberte okruh ExpressRoute z vašeho předplatného. 
3. V rozevírací nabídce vyberte svůj okruh ExpressRoute, který chcete přidružit k rozbočovači.
4. Pokud okruh ExpressRoute není ve stejném předplatném nebo jste obdrželi [autorizační klíč a ID partnerského okruhu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), vyberte **Vyhledat okruh uplatňující autorizační klíč**.
5. Zadejte následující podrobnosti:
* **Autorizační klíč** – Autorizační klíč vygenerovaný vlastníkem okruhu, jak je popsáno výše.
* **Identifikátor URI partnerského okruhu** – Identifikátor URI okruhu poskytnutý vlastníkem okruhu, který je jedinečným identifikátorem okruhu.
* **Váha směrování** - [Váha směrování](../expressroute/expressroute-optimize-routing.md) umožňuje upřednostňovat určité cesty v případě, že je k jednomu rozbočovači připojených více okruhů z různých partnerských umístění.
6. Klikněte na **Vyhledat okruh**, a pokud se najde nějaký okruh, vyberte ho.
7. V rozevíracím seznamu vyberte 1 nebo více rozbočovačů a klikněte na **Uložit**.

## <a name="vnet"></a>5. Připojení virtuální sítě k rozbočovači

V tomto kroku vytvoříte partnerské připojení mezi rozbočovačem a určitou virtuální sítí. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Virtual network connection** (Připojení k virtuální síti).
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.


## <a name="viewwan"></a>6. Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač. Podržením ukazatele na některém z těchto bodů zobrazíte souhrn stavu rozbočovače.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="viewhealth"></a>7. Zobrazení stavu prostředků

1. Přejděte na svoji síť WAN.
2. Na stránce sítě WAN v části **SUPPORT + Troubleshooting** (Podpora a řešení potíží) klikněte na **Health** (Stav) a prohlédněte si stav svého prostředku.

## <a name="connectmon"></a>8. Monitorování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem Azure a vzdálenou lokalitou. Informace o tom, jak nastavit monitorování připojení, najdete v článku [Monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Do pole zdroje zadejte IP adresu virtuálního počítače v Azure a cílovou IP adresou je IP adresa lokality.

## <a name="cleanup"></a>9. Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuální sítě WAN
> * Vytvoření rozbočovače
> * Vyhledání a přidružení okruhu k rozbočovači
> * Přidružení okruhu k rozbočovači nebo rozbočovačům
> * Připojení virtuální sítě k rozbočovači
> * Zobrazení virtuální sítě WAN
> * Zobrazení stavu prostředků
> * Monitorování připojení

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).