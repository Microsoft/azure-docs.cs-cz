---
title: Správa zón DNS v Azure DNS-Azure Portal | Microsoft Docs
description: Zóny DNS můžete spravovat pomocí Azure Portal. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965795"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Správa Zóny DNS v Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Rozhraní příkazového řádku Azure Classic](./dns-operations-dnszones-cli.md)
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
   |**Skupina prostředků**|**Vytvořit novou:** contosoDNSRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný. Další informace o skupinách prostředků najdete v článku Přehled [Správce prostředků](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) .|
   |**Umístění**|USA – západ||

> [!NOTE]
> Skupina prostředků označuje umístění skupiny prostředků a nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a není zobrazeno.

## <a name="list-dns-zones"></a>Výpis zón DNS

V Azure Portal přejděte na **Další služby**  >  **síťové**  >  **zóny DNS**. Každá zóna DNS je vlastním prostředkem a v tomto zobrazení se zobrazí informace, jako je například počet sad záznamů a názvové servery. **NÁZVOVÉ servery** sloupce nejsou ve výchozím zobrazení. Pokud ho chcete přidat, klikněte na **sloupce**, vyberte **Názvové servery** a potom klikněte na **Hotovo**.

![výpis zón DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Přejděte k zóně DNS na portálu. V okně **zóna DNS** klikněte na **Odstranit zónu**. Pak se zobrazí výzva k potvrzení, že chcete odstranit zónu DNS. Odstraněním zóny DNS dojde také k odstranění všech záznamů, které jsou v zóně obsaženy.

## <a name="next-steps"></a>Další kroky

Seznamte se s tím, jak pracovat se zónou DNS a záznamy, na webu [Začínáme s Azure DNS pomocí Azure Portal](dns-getstarted-portal.md).