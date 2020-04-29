---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77619293"
---
1. Pokud chcete vytvořit nové úložiště konfigurace aplikace, přihlaste se k [Azure Portal](https://portal.azure.com). V levém horním rohu domovské stránky vyberte **vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte **Konfigurace aplikace** a vyberte Enter.

    ![Vyhledat konfiguraci aplikace](media/azure-app-configuration-create/azure-portal-search.png)

1. Ve výsledcích hledání vyberte **Konfigurace aplikace** a pak vyberte **vytvořit**.

    ![Výběr možnosti Vytvořit](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. V podokně > **vytvořit** **konfiguraci aplikace**zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte jedinečný název prostředku, který se má použít pro prostředek úložiště konfigurace aplikace. Název musí být řetězec o 5 až 50 znaků a obsahovat pouze čísla, písmena a `-` znak. Název nesmí začínat ani končit `-` znakem.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, které chcete použít k testování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a seznam **předplatných** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace aplikace. Tato skupina je užitečná k organizování více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA – střed* | **Umístění** použijte k určení geografického umístění, ve kterém je úložiště konfigurace aplikace hostované. Nejlepšího výkonu dosáhnete vytvořením prostředku ve stejné oblasti jako jiné součásti aplikace. |
    | **Cenová úroveň** | *Free* | Vyberte požadovanou cenovou úroveň. Další podrobnosti najdete na [stránce s cenami konfigurace aplikace](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Vytvoření prostředku úložiště konfigurace aplikace](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

1. Po dokončení nasazení vyberte **Nastavení** > **přístupové klíče**. Poznamenejte si primární připojovací řetězec klíče jen pro čtení. Pomocí tohoto připojovacího řetězce později nakonfigurujete aplikaci tak, aby komunikovala s úložištěm konfigurace aplikace, které jste vytvořili.
