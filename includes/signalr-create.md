---
title: zahrnout soubor
description: zahrnout soubor
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560706"
---
1. Pokud chcete vytvořit prostředek služby Azure SignalR, nejdřív přihlásit k [webu Azure portal](https://portal.azure.com). V levém horním okraji stránky vyberte **+ vytvořit prostředek**. V **Hledat na Marketplace** textové pole, zadejte **služby SignalR**.

2. Vyberte **služby SignalR** výsledků a vyberte **vytvořit**.

3. Na novém **SignalR** nastavení stránky, přidáním následujícího nastavení pro nový prostředek SignalR:

    | Název | Doporučená hodnota | Popis |
    | ---- | ----------------- | ----------- |
    | Název prostředku | *testsignalr* | Zadejte jedinečný název prostředku, který se použije pro prostředek služby SignalR. Název musí být řetězec dlouhý 1 až 63 znaků a obsahovat jenom čísla, písmena a spojovník (`-`) znaků. Název nesmí začínat ani končit znakem spojovníku, a znaky po sobě jdoucí pomlčky nejsou platné.|
    | Předplatné | Zvolte vaše předplatné. |  Vyberte předplatné Azure, které chcete k otestování služby SignalR použít. Pokud má váš účet jenom jedno předplatné, je automaticky vybrána a **předplatné** rozevíracím seznamu nezobrazí.|
    | Skupina prostředků | Vytvořte skupinu prostředků s názvem *SignalRTestResources*| Vyberte nebo vytvořte skupinu prostředků pro prostředek služby SignalR. Tato skupina je užitečné pro uspořádání různé prostředky, které můžete chtít odstranit tak, že odstraníte skupinu prostředků ve stejnou dobu. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Location | *USA – východ* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |
    | Cenová úroveň | *Free* | V současné době **Free** a **standardní** možnosti jsou k dispozici. |
    | Připnout na řídicí panel | ✔ | Zaškrtněte toto políčko, aby připnout na řídicí panel, takže je jednodušší zjistit, prostředků. |

4. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

5. Po dokončení nasazení vyberte **klíče** pod **nastavení**. Zkopírování připojovacího řetězce pro primární klíč. Tento řetězec budete používat později konfigurace aplikace pro použití prostředků služby Azure SignalR.

    Připojovací řetězec bude mít následující formát:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
