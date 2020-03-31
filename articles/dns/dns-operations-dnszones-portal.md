---
title: Správa zón DNS v Azure DNS – portál Azure | Dokumenty společnosti Microsoft
description: Zóny DNS můžete spravovat pomocí portálu Azure. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS ve službě Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936789"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak spravovat zóny DNS na webu Azure Portal

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

V tomto článku se ukazuje, jak spravovat zóny DNS pomocí portálu Azure. Zóny DNS můžete také spravovat pomocí azure [cli](dns-operations-dnszones-cli.md) pro různé platformy nebo Azure [PowerShellu](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přihlášení k webu Azure Portal
2. V nabídce Rozbočovač přejděte na **Vytvořit prostředek > síťovou zónu DNS > otevřete** okno **Vytvořit zónu DNS.**

    ![Zóna DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. V okně **Vytvořit zónu DNS** zadejte následující hodnoty a pak klikněte na **Vytvořit**:


   | **Nastavení** | **Hodnotu** | **Podrobnosti** |
   |---|---|---|
   |**Název**|contoso.com|Název zóny DNS|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém chcete vytvořit zónu DNS.|
   |**Skupina prostředků**|**Vytvořit novou:** contosoDNSRG|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný. Další informace o skupinách prostředků najdete v článku přehled [Správce prostředků.](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)|
   |**Umístění**|USA – západ||

> [!NOTE]
> Skupina prostředků označuje umístění skupiny prostředků a nemá žádný vliv na zónu DNS. Umístění zóny DNS je vždy globální a není zobrazeno.

## <a name="list-dns-zones"></a>Výpis zón DNS

Na webu Azure Portal přejděte na **další zóny** > **DNS****sítě** > . Každá zóna DNS je vlastním zdrojem a informace, jako je počet sad záznamů a názvové servery, lze zobrazit z tohoto zobrazení. Názvové **servery** sloupce nejsou ve výchozím zobrazení. Chcete-li ji přidat, klepněte na **položku Sloupce**, vyberte **položku Názvové servery**a potom klepněte na **tlačítko Hotovo**.

![výpis zón DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Přejděte do zóny DNS na portálu. V okně **zóny DNS** klepněte na tlačítko **Odstranit zónu**. Poté budete vyzváni k potvrzení, že chcete odstranit zónu DNS. Odstraněním zóny DNS odstraníte také všechny záznamy, které jsou v zóně obsaženy.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak pracovat se zónou DNS a záznamy, na stránce [Začínáme s Azure DNS na webu Azure Portal](dns-getstarted-portal.md).