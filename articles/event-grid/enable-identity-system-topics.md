---
title: Povolení spravované identity v Azure Event Grid systémových tématech
description: Tento článek popisuje, jak povolit identitu spravované služby pro Azure Event Grid systémové téma.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630415"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Přiřazení identity spravované systémem k Event Grid systémovému tématu
V tomto článku se dozvíte, jak povolit systémově spravovanou identitu pro existující Event Grid v tématu. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> V současné době není možné povolit systémově spravovanou identitu při vytváření nového systémového tématu, to znamená při vytváření odběru událostí v prostředku Azure, který podporuje systémová témata. 


## <a name="use-azure-portal"></a>Použití webu Azure Portal
Následující postup ukazuje, jak povolit systémově spravovanou identitu pro systémové téma. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Na panelu hledání v horní části vyhledejte **Systémová témata Event gridu** .
3. Vyberte **systémové téma** , pro které chcete spravovanou identitu povolit. 
4. V nabídce vlevo vyberte **Identita** . Tuto možnost nevidíte pro systémové téma, které se nachází v globálním umístění. 
5. **Zapnutím přepínače povolte identitu** . 
1. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Stránka identita pro systémové téma"::: 
1. V potvrzovací zprávě vyberte **Ano** . 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Přiřazení identity k systémovému tématu – potvrzení"::: 
1. Ověřte, že se zobrazí ID objektu spravované identity přiřazené systémem, a podívejte se na odkaz pro přiřazení rolí. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Přiřazení identity k systémovému tématu – dokončeno"::: 

## <a name="global-azure-sources"></a>Globální zdroje Azure
Identitu spravovanou systémem můžete povolit jenom pro místní prostředky Azure. Nemůžete ho povolit pro systémová témata přidružená k globálním prostředkům Azure, jako jsou například předplatná Azure, skupiny prostředků nebo Azure Maps. Systémová témata pro tyto globální zdroje nejsou také přidružena k konkrétní oblasti. Nevidíte stránku **Identita** pro systémové téma, jehož umístění je nastavené na **globální**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Systémové téma s umístěním nastaveným na globální"::: 



## <a name="next-steps"></a>Další kroky
Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue). Podrobný postup najdete v tématu [Přidání identity do rolí Azure v umístěních](add-identity-roles.md). 