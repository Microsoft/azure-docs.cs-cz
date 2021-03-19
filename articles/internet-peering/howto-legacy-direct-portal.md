---
title: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal
titleSuffix: Azure
description: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700259"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal

Tento článek popisuje, jak převést stávající starší verzi přímého partnerského vztahu na prostředek Azure pomocí Azure Portal.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Převod starší verze přímého partnerského vztahu

Jako poskytovatel internetových služeb můžete převádět starší verze přímých partnerských vztahů pomocí [vytvoření partnerského vztahu]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na stránce **vytvořit partnerský vztah** na kartě **základy** vyplňte pole, jak je znázorněno zde:

    > [!div class="mx-imgBorder"] 
    > ![Registrace služby Peering Service](./media/setup-basics-tab.png)

*    Vyberte své předplatné Azure.

* V případě skupiny prostředků můžete buď zvolit existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu výběrem možnosti vytvořit nový. V tomto příkladu vytvoříme novou skupinu prostředků.

* Název odpovídá názvu prostředku a může to být cokoli, co si zvolíte.

* Oblast se vybere automaticky, pokud jste zvolili existující skupinu prostředků. Pokud se rozhodnete vytvořit novou skupinu prostředků, musíte taky zvolit oblast Azure, ve které chcete prostředek umístit.

>[!NOTE]
>Oblast, ve které se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah s Microsoftem. Je ale osvědčeným postupem uspořádání prostředků partnerského vztahu do skupin prostředků, které se nacházejí v nejbližších oblastech Azure. Například pro partnerské vztahy v Ashburn můžete vytvořit skupinu prostředků v Východní USA nebo východní USA 2.

* V poli **PeerASN** vyberte své ASN.

>[!IMPORTANT] 
>Před odesláním žádosti o vytvoření partnerského vztahu můžete zvolit jenom číslo ASN s ValidationState jako schválenou. Pokud jste právě odeslali žádost PeerAsn, počkejte 12 hodin, nebo tak, aby bylo možné schválit přidružení ASN. Pokud vybrané číslo ASN čeká na ověření, zobrazí se chybová zpráva. Pokud nevidíte ASN, které potřebujete vybrat, zkontrolujte, jestli jste vybrali správné předplatné. Pokud ano, ověřte, jestli jste už vytvořili PeerAsn pomocí **[přidružení partnerského čísla ASN k předplatnému Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění prostředku a Konfigurace základního nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje informací

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
