---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: c344d7e1aa1f6d45131295ba9aad1294c5ba548c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930703"
---
1. Pokud chcete vytvořit nové úložiště konfigurace aplikace, přihlaste se k [Azure Portal](https://portal.azure.com). V levém horním rohu domovské stránky vyberte **vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte *Konfigurace aplikace* a vyberte <kbd>ENTER</kbd>.

    ![Vyhledat konfiguraci aplikace](media/azure-app-configuration-create/azure-portal-search.png)

1. Ve výsledcích hledání vyberte **Konfigurace aplikace** a pak vyberte **vytvořit**.

    ![Výběr možnosti Vytvořit](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. V podokně **vytvořit konfiguraci aplikace** zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, které chcete použít k testování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a seznam **předplatných** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace aplikace. Tato skupina je užitečná k organizování více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/management/overview.md). |
    | **Název prostředku** | Globálně jedinečný název | Zadejte jedinečný název prostředku, který se má použít pro prostředek úložiště konfigurace aplikace. Název musí být řetězec o 5 až 50 znaků a obsahovat pouze čísla, písmena a `-` znak. Název nesmí začínat ani končit `-` znakem. |
    | **Umístění** | *USA – střed* | **Umístění** použijte k určení geografického umístění, ve kterém je úložiště konfigurace aplikace hostované. Nejlepšího výkonu dosáhnete vytvořením prostředku ve stejné oblasti jako jiné součásti aplikace. |
    | **Cenová úroveň** | *Free* | Vyberte požadovanou cenovou úroveň. Další informace najdete na stránce s [cenami konfigurace aplikací](https://azure.microsoft.com/pricing/details/app-configuration). |

1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení.

1. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

1. Po dokončení nasazení přejděte do prostředku konfigurace aplikace. Vyberte **Nastavení** > **Přístupové klíče**. Poznamenejte si primární připojovací řetězec klíče jen pro čtení. Pomocí tohoto připojovacího řetězce později nakonfigurujete aplikaci tak, aby komunikovala s úložištěm konfigurace aplikace, které jste vytvořili.