---
title: Správa zón DNS v DNS Azure – Azure portal | Dokumentace Microsoftu
description: Můžete spravovat zóny DNS pomocí webu Azure portal. Tento článek popisuje, jak aktualizovat, odstranit a vytvářet zóny DNS v Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829678"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Správa zón DNS na webu Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klasické rozhraní příkazového řádku Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

V tomto článku se dozvíte, jak spravovat zóny DNS pomocí webu Azure portal. Můžete také spravovat zóny DNS pomocí napříč platformami [rozhraní příkazového řádku Azure](dns-operations-dnszones-cli.md) nebo Azure [Powershellu](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přihlášení k webu Azure Portal
2. V nabídce centra, přejděte na **vytvořit prostředek > sítě > zóna DNS** otevřít **vytvořit zónu DNS** okno.

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

Na webu Azure Portal, přejděte na **další služby** > **sítě** > **zóny DNS**. Každá zóna DNS je vlastní prostředek a informace, jako je počet sad záznamů a názvové servery, které jsou zobrazené v tomto zobrazení. Sloupec **NÁZVOVÉ servery** není ve výchozím zobrazení. Pokud chcete přidat, klikněte na tlačítko **sloupce**vyberte **názvové servery**a potom klikněte na tlačítko **provádí**.

![výpis zón DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Přejděte k zóně DNS na portálu. Na **zónu DNS** okna, klikněte na tlačítko **odstranit zónu**. Zobrazí se výzva k potvrzení, že chcete odstranit zónu DNS. Odstraňuje se zóna DNS se odstraní také všechny záznamy, které jsou obsaženy v dané zóně.

## <a name="next-steps"></a>Další postup

Informace o práci se záznamy a zóny DNS návštěvou [Začínáme s DNS Azure pomocí webu Azure portal](dns-getstarted-portal.md).