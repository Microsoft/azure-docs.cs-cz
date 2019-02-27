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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884862"
---
1. Pokud chcete vytvořit novým úložištěm konfigurace aplikace, nejdřív přihlásit k [webu Azure portal](https://aka.ms/azconfig/portal). V levém horním rohu stránky klikněte na **+ Vytvořit prostředek**. V **Hledat na Marketplace** textové pole, typ **konfigurace aplikace** a stiskněte klávesu **Enter**.

    ![Hledat konfigurace aplikace](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Klikněte na tlačítko **konfigurace aplikace** ve výsledcích hledání a následně **vytvořit**.

3. V **konfigurace aplikace** > **vytvořit** stránky, zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte název jedinečné prostředků pro prostředek aplikace konfigurace úložiště. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit znakem `-` a po sobě jdoucí znaky `-` nejsou platné.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, který chcete použít k otestování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a rozevírací seznam **Předplatné** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace vaší aplikace. Tato skupina umožňuje uspořádání více prostředků, které pak můžete odstranit najednou tím, že odstraníte skupinu prostředků. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA (střed)* | Použití **umístění** k určení zeměpisného umístění, ve které je hostované úložiště konfigurace vaší aplikace. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |

    ![Vytvořte prostředek aplikace konfigurace úložiště](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Klikněte na možnost **Vytvořit**. Dokončení nasazení může trvat několik minut.

5. Po dokončení nasazení klikněte na tlačítko **nastavení** > **přístupové klíče**. Poznamenejte si buď primární jen pro čtení nebo primární čtení a zápis připojovací řetězec klíče. Bude to použít později ke konfiguraci vaší aplikace ke komunikaci s úložištěm aplikace konfigurace, kterou jste právě vytvořili.

6. Klikněte na tlačítko **klíč/hodnota Explorer** a **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:BackgroundColor | Prázdné |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Černá |
    | TestApp:Settings:Message | Data z konfigurace aplikace Azure |

    Ukončíte **popisek** a **typ obsahu** zatím prázdný.
