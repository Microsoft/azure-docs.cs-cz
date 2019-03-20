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
ms.openlocfilehash: 0f066915046e363932652c104fabe96ed0a9d4c4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227180"
---
1. Pokud chcete vytvořit novým úložištěm konfigurace aplikace, přihlaste se k [webu Azure portal](https://aka.ms/azconfig/portal). V levém horním rohu stránky vyberte **+ vytvořit prostředek**. V **Hledat na Marketplace** zadejte **konfigurace aplikace** a stiskněte klávesu Enter.

    ![Hledat konfigurace aplikace](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Vyberte **konfigurace aplikace** z výsledků hledání a pak vyberte **vytvořit**.

3. Na **konfigurace aplikace** > **vytvořit** stránky, zadejte následující nastavení.

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte název jedinečné prostředků pro prostředek aplikace konfigurace úložiště. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit `-` znak a po sobě jdoucích `-` znaky nejsou platné.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, který chcete použít k otestování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, je automaticky vybrána a **předplatné** rozevíracím seznamu nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace vaší aplikace. Tato skupina je užitečné pro uspořádání různé prostředky, které můžete chtít odstranit tak, že odstraníte skupinu prostředků ve stejnou dobu. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA (střed)* | Použití **umístění** k určení zeměpisného umístění, ve které je hostované úložiště konfigurace vaší aplikace. Pro zajištění nejlepšího výkonu vytvořte prostředek ve stejné oblasti jako součásti vaší aplikace. |

    ![Vytvořte prostředek aplikace konfigurace úložiště](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Vyberte **Vytvořit**. Nasazení může trvat několik minut déle.

5. Po dokončení nasazení vyberte **nastavení** > **přístupové klíče**. Poznamenejte buď primární jen pro čtení nebo primární čtení a zápis připojovací řetězec klíče. Tento připojovací řetězec použijete později ke konfiguraci vaší aplikace ke komunikaci s úložišti konfigurace aplikace, kterou jste vytvořili.

6. Vyberte **klíč/hodnota Explorer** > **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:BackgroundColor | White |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Black |
    | TestApp:Settings:Message | Data z konfigurace aplikace Azure |

    Ponechte **popisek** a **typ obsahu** zatím prázdný.
