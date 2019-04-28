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
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690277"
---
1. Pokud chcete vytvořit nový prostředek služby Azure SignalR, nejprve se přihlaste k webu [Azure Portal](https://portal.azure.com). V levém horním rohu stránky klikněte na **+ Vytvořit prostředek**. Do textového pole **Hledat na Marketplace** zadejte **Služba SignalR** a stiskněte **Enter**.

2. Ve výsledcích klikněte na **Služba SignalR** a pak na **Vytvořit**.

3. Na stránce nastavení nové služby **SignalR** přidejte následující nastavení pro nový prostředek služby SignalR:

    | Název | Doporučená hodnota | Popis |
    | ---- | ----------------- | ----------- |
    | Název prostředku | *testsignalr* | Zadejte jedinečný název prostředku, který se použije pro prostředek služby SignalR. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit znakem `-` a po sobě jdoucí znaky `-` nejsou platné.|
    | Předplatné | Zvolte vaše předplatné. |  Vyberte předplatné Azure, které chcete k otestování služby SignalR použít. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a rozevírací seznam **Předplatné** se nezobrazí.|
    | Skupina prostředků | Vytvořte novou skupinu prostředků *SignalRTestResources*.| Vyberte nebo vytvořte skupinu prostředků pro prostředek služby SignalR. Tato skupina umožňuje uspořádání více prostředků, které pak můžete odstranit najednou tím, že odstraníte skupinu prostředků. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Umístění | *USA – východ* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |
    | Cenová úroveň | *Free* | V současné době jsou k dispozici možnosti **Free** a **Standard**. |
    | Připnout na řídicí panel | ✔ | Zaškrtnutím tohoto políčka připnete prostředek na svůj řídicí panel, abyste si usnadnili jeho hledání. |

4. Klikněte na **Vytvořit**. Dokončení nasazení může trvat několik minut.

5. Po dokončení nasazení klikněte v části **NASTAVENÍ** na **Klíče**. Zkopírujte si primární připojovací řetězec klíče. Později ho použijete ke konfiguraci vaší aplikace pro používání prostředku služby Azure SignalR.

    Připojovací řetězec bude mít následující formát:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
