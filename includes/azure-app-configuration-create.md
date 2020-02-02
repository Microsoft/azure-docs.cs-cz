---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 017833403beca6fb8689bd6f90cc8f460d56c65e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961518"
---
1. Pokud chcete vytvořit nové úložiště konfigurace aplikace, přihlaste se k [Azure Portal](https://portal.azure.com). V levém horním rohu domovské stránky vyberte **vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte **Konfigurace aplikace** a vyberte Enter.

    ![Vyhledat konfiguraci aplikace](media/azure-app-configuration-create/azure-portal-search.png)

1. Ve výsledcích hledání vyberte **Konfigurace aplikace** a pak vyberte **vytvořit**.

    ![Výběr možnosti Vytvořit](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. V podokně **Konfigurace aplikace** > **vytvořit** zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte jedinečný název prostředku, který se má použít pro prostředek úložiště konfigurace aplikace. Název musí být řetězec o 5 až 50 znaků a musí obsahovat jenom číslice, písmena a `-` znak. Název nesmí začínat ani končit znakem `-`.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, které chcete použít k testování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a seznam **předplatných** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace aplikace. Tato skupina je užitečná k organizování více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *Střed USA* | **Umístění** použijte k určení geografického umístění, ve kterém je úložiště konfigurace aplikace hostované. Nejlepšího výkonu dosáhnete vytvořením prostředku ve stejné oblasti jako jiné součásti aplikace. |

    ![Vytvoření prostředku úložiště konfigurace aplikace](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

1. Po dokončení nasazení vyberte **nastavení** > **přístupové klíče**. Poznamenejte si primární připojovací řetězec klíče jen pro čtení. Pomocí tohoto připojovacího řetězce později nakonfigurujete aplikaci tak, aby komunikovala s úložištěm konfigurace aplikace, které jste vytvořili.
