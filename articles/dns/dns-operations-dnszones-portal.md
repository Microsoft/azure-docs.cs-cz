---
title: Správa zón DNS v Azure DNS-Azure Portal | Microsoft Docs
description: Zóny DNS můžete spravovat pomocí Azure Portal. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211909"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Správa Zóny DNS v Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

V tomto článku se dozvíte, jak spravovat zóny DNS pomocí Azure Portal. Zóny DNS můžete spravovat i pomocí [Azure CLI](dns-operations-dnszones-cli.md) pro různé platformy nebo Azure [PowerShellu](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přihlášení k webu Azure Portal
2. V nabídce centra přejděte na **vytvořit prostředek > síti > zóně DNS** . otevře se okno **vytvořit zónu DNS** .

    ![Zóna DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. V okně **Vytvořit zónu DNS** zadejte následující hodnoty a pak klikněte na **Vytvořit**:


   | **Nastavení** | **Hodnota** | **Podrobnosti** |
   |---|---|---|
   |**Název**|contoso.com|Název zóny DNS|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém chcete vytvořit zónu DNS.|
   |**Skupina prostředků**|**Vytvořit novou:** contosoDNSRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný. Další informace o skupinách prostředků najdete v článku s přehledem [Resource Manageru](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Umístění**|Západní USA||

> [!NOTE]
> Skupina prostředků označuje umístění skupiny prostředků a nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a není zobrazeno.

## <a name="list-dns-zones"></a>Výpis zón DNS

V Azure Portal přejděte na **Další služby** > **sítě** > **zóny DNS**. Každá zóna DNS je vlastním prostředkem a v tomto zobrazení se zobrazí informace, jako je například počet sad záznamů a názvové servery. **NÁZVOVÉ servery** sloupce nejsou ve výchozím zobrazení. Pokud ho chcete přidat, klikněte na **sloupce**, vyberte **Názvové servery**a potom klikněte na **Hotovo**.

![výpis zón DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Přejděte k zóně DNS na portálu. V okně **zóna DNS** klikněte na **Odstranit zónu**. Pak se zobrazí výzva k potvrzení, že chcete odstranit zónu DNS. Odstraněním zóny DNS dojde také k odstranění všech záznamů, které jsou v zóně obsaženy.

## <a name="next-steps"></a>Další kroky

Seznamte se s tím, jak pracovat se zónou DNS a záznamy, na webu [Začínáme s Azure DNS pomocí Azure Portal](dns-getstarted-portal.md).