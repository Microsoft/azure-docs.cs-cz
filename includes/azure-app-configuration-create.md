---
title: zahrnout soubor
description: zahrnout soubor
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393302"
---
1. Chcete-li vytvořit novým úložištěm konfigurace aplikace, přihlaste se k [webu Azure portal](https://portal.azure.com). V levém horním rohu podokna vyberte **+ vytvořit prostředek**. V **Hledat na Marketplace** zadejte **konfigurace aplikace** a stiskněte Enter.

    ![Hledat konfigurace aplikace](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Vyberte **konfigurace aplikace** z výsledků hledání a pak vyberte **vytvořit**.

1. Na **konfigurace aplikace** > **vytvořit** podokně zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte název jedinečné prostředků chcete použít pro prostředek konfigurace aplikace pro store. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit `-` znak a po sobě jdoucích `-` znaky nejsou platné.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, který chcete použít k otestování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, je automaticky vybrána a **předplatné** seznamu nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro váš prostředek konfigurace aplikace pro store. Tato skupina je užitečné pro uspořádání různé prostředky, které můžete chtít odstranit tak, že odstraníte skupinu prostředků ve stejnou dobu. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA (střed)* | Použití **umístění** k určení zeměpisného umístění, ve které je hostované úložiště konfigurace vaší aplikace. Pro zajištění nejlepšího výkonu vytvořte prostředek ve stejné oblasti jako součásti vaší aplikace. |

    ![Vytvořte prostředek konfigurace aplikace pro store](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Vyberte **Vytvořit**. Nasazení může trvat několik minut déle.

1. Po dokončení nasazení vyberte **nastavení** > **přístupové klíče**. Poznamenejte buď primární jen pro čtení nebo primární čtení a zápis připojovací řetězec klíče. Tento připojovací řetězec budete používat později ke konfiguraci vaší aplikace ke komunikaci s úložištěm konfigurace aplikace, které jste vytvořili.
