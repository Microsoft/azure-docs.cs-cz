---
title: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal
titleSuffix: Azure
description: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: e4f9f5b59b61065c300b58fb1cdb88e12b7ddbe0
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247219"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí Azure Portal.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure

Jako poskytovatel internetového Exchange můžete vytvořit partnerský vztah Exchange vytvořením [partnerského]( https://go.microsoft.com/fwlink/?linkid=2129593)vztahu.

1. Na stránce **vytvořit partnerský vztah** na kartě **základy** vyplňte pole, jak je znázorněno zde:

   ![Registrace služby Peering Service](./media/setup-basics-tab.png)

* Vyberte své předplatné Azure.

* V případě skupiny prostředků můžete buď zvolit existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu výběrem možnosti vytvořit nový. V tomto příkladu vytvoříme novou skupinu prostředků.

* Název odpovídá názvu prostředku a může to být cokoli, co si zvolíte.

* Oblast se vybere automaticky, pokud jste zvolili existující skupinu prostředků. Pokud se rozhodnete vytvořit novou skupinu prostředků, musíte taky zvolit oblast Azure, ve které chcete prostředek umístit.

  >[!NOTE]
  >Oblast, ve které se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah s Microsoftem. Je ale osvědčeným postupem uspořádání prostředků partnerského vztahu do skupin prostředků, které se nacházejí v nejbližších oblastech Azure. Například pro partnerské vztahy v Ashburn můžete vytvořit skupinu prostředků v Východní USA nebo východní USA 2.

* V poli **PeerASN** vyberte své ASN.

  >[!IMPORTANT]  
  >Před odesláním žádosti o vytvoření partnerského vztahu můžete zvolit jenom číslo ASN s ValidationState jako schválenou. Pokud jste právě odeslali žádost PeerAsn, počkejte 12 hodin, nebo tak, aby bylo možné schválit přidružení ASN. Pokud vybrané číslo ASN čeká na ověření, zobrazí se chybová zpráva. Pokud nevidíte ASN, které potřebujete vybrat, zkontrolujte, jestli jste vybrali správné předplatné. Pokud ano, ověřte, jestli jste už vytvořili PeerAsn pomocí **[přidružení partnerského čísla ASN k předplatnému Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

* Chcete-li pokračovat, vyberte možnost **Další: Konfigurace** .


#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
