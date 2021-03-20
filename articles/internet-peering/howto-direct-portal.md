---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537204"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal

Tento článek popisuje, jak vytvořit přímý partnerský vztah Microsoftu pro poskytovatele internetových služeb nebo poskytovatele internetového Exchange pomocí Azure Portal. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí Azure [PowerShellu](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .
* Pokud už máte přímá připojení partnerského vztahu se společností Microsoft, která nejsou převedená na prostředky Azure, přečtěte si téma [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Vytvoření přímého partnerského vztahu

Jako poskytovatel internetových služeb nebo poskytovatel internetového Exchange můžete vytvořit novou žádost o přímý partnerský vztah [vytvořením partnerského]( https://go.microsoft.com/fwlink/?linkid=2129593)vztahu.

1. Na stránce **vytvořit partnerský vztah** na kartě **základy** vyplňte pole, jak je znázorněno zde:


    ![Snímek obrazovky se zobrazí karta základy vytvoření partnerského vztahu pomocí odkazu vytvořit nový, který se vyvolal.](./media/setup-basics-tab.png)

2. Vyberte své předplatné Azure.

3. V případě skupiny prostředků můžete buď zvolit existující skupinu prostředků z rozevíracího seznamu, nebo vytvořit novou skupinu výběrem možnosti vytvořit nový. V tomto příkladu vytvoříme novou skupinu prostředků.

4. Název odpovídá názvu prostředku a může to být cokoli, co si zvolíte.

5. Oblast se vybere automaticky, pokud jste zvolili existující skupinu prostředků. Pokud se rozhodnete vytvořit novou skupinu prostředků, musíte taky zvolit oblast Azure, ve které chcete prostředek umístit.

    >[!NOTE]
    > Oblast, ve které se nachází skupina prostředků, je nezávislá na umístění, kde chcete vytvořit partnerský vztah s Microsoftem. Je ale osvědčeným postupem uspořádání prostředků partnerského vztahu do skupin prostředků, které se nacházejí v nejbližších oblastech Azure. Například pro partnerské vztahy v Ashburn můžete vytvořit skupinu prostředků v Východní USA nebo východní USA 2.

6. V poli **PeerASN** vyberte své ASN.

    >[!IMPORTANT]
    >Před odesláním žádosti o vytvoření partnerského vztahu můžete zvolit jenom číslo ASN s ValidationState jako schválenou. Pokud jste právě odeslali žádost PeerAsn, počkejte 12 hodin, nebo tak, aby bylo možné schválit přidružení ASN. Pokud vybrané číslo ASN čeká na ověření, zobrazí se chybová zpráva. Pokud nevidíte ASN, které potřebujete vybrat, zkontrolujte, jestli jste vybrali správné předplatné. Pokud ano, ověřte, jestli jste už vytvořili PeerAsn pomocí **[přidružení partnerského čísla ASN k předplatnému Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Chcete-li pokračovat, vyberte možnost **Další: Konfigurace** .



    ![Snímek obrazovky se zobrazí karta základy stránky vytvořit partnerský vztah se všemi zadanými hodnotami.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje informací

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).
