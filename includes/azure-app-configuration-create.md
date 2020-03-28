---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619293"
---
1. Pokud chcete vytvořit nové úložiště konfigurace aplikací, přihlaste se na [portál Azure](https://portal.azure.com). V levém horním rohu domovské stránky vyberte **Vytvořit zdroj**. Do pole **Hledat na marketplace** zadejte **Konfiguraci aplikace** a vyberte Enter.

    ![Hledání konfigurace aplikace](media/azure-app-configuration-create/azure-portal-search.png)

1. Ve **výsledcích** hledání vyberte Konfigurace aplikace a pak vyberte **Vytvořit**.

    ![Výběr možnosti Vytvořit](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. V podokně**Vytvoření** **konfigurace** > aplikace zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte jedinečný název prostředku, který se má použít pro prostředek obchodu Konfigurace aplikace. Název musí být řetězec mezi 5 a 50 znaky a `-` musí obsahovat pouze čísla, písmena a znak. Název nemůže začínat ani `-` končit znakem.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, které chcete použít k testování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, je automaticky vybrané a seznam **Odběr** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek obchodu Konfigurace aplikací. Tato skupina je užitečná pro uspořádání více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete [v tématu Použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA – střed* | Pomocí **funkce Umístění** můžete určit geografické umístění, ve kterém je obchod s konfigurací aplikace hostován. Chcete-li dosáhnout nejlepšího výkonu, vytvořte prostředek ve stejné oblasti jako ostatní součásti aplikace. |
    | **Cenová úroveň** | *Zdarma* | Vyberte požadovanou cenovou úroveň. Další podrobnosti naleznete na [stránce s cenami konfigurace aplikace](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Vytvoření prostředku úložiště konfigurace aplikace](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

1. Po dokončení nasazení vyberte **možnost Nastavení** > **přístupových klíčů**. Poznamenejte si připojovací řetězec primárního klíče jen pro čtení. Tento připojovací řetězec budete později používat ke konfiguraci aplikace pro komunikaci s úložištěm konfigurace aplikací, které jste vytvořili.
