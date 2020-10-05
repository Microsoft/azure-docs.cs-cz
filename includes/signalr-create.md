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
ms.openlocfilehash: ba46d4dbe90b696398ed4c78383e127861c1a066
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "86050443"
---
1. Pokud chcete vytvořit prostředek služby signalizace Azure, nejdřív se přihlaste k [Azure Portal](https://portal.azure.com). V levé horní části stránky vyberte **+ vytvořit prostředek**. Do textového pole **Hledat na Marketplace** zadejte **službu Signal**.

2. Ve výsledcích vyberte **službu signaler** a vyberte **vytvořit**.

3. Na stránce Nová nastavení **signálu** přidejte následující nastavení pro nový prostředek signalizace:

    | Name | Doporučená hodnota | Popis |
    | ---- | ----------------- | ----------- |
    | Název prostředku | *testsignalr* | Zadejte jedinečný název prostředku, který se použije pro prostředek služby SignalR. Název musí být řetězec o 1 až 63 znaků a obsahovat jenom číslice, písmena a spojovníky ( `-` ). Název nesmí začínat ani končit znakem spojovníku a po sobě jdoucí znaky spojovníku nejsou platné.|
    | Předplatné | Zvolte vaše předplatné. |  Vyberte předplatné Azure, které chcete k otestování služby SignalR použít. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a rozevírací seznam **předplatné** se nezobrazí.|
    | Skupina prostředků | Vytvořte skupinu prostředků s názvem *SignalRTestResources* .| Vyberte nebo vytvořte skupinu prostředků pro prostředek služby SignalR. Tato skupina je užitečná k organizování více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/management/overview.md). |
    | Umístění | *USA – východ* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |
    | Cenová úroveň | Zadejte možnost pro *bezplatnou* SKU. | V současné době jsou dostupné **bezplatné** a **standardní** možnosti. |
    | Připnout na řídicí panel | ✔ | Toto políčko zaškrtněte, pokud chcete prostředek připnout na řídicí panel, aby bylo snazší ho najít. |

4. Vyberte **Vytvořit**. Dokončení nasazení může trvat několik minut.

5. Až se nasazení dokončí, v části **Nastavení**vyberte **klíče** . Zkopírujte připojovací řetězec pro primární klíč. Pomocí tohoto řetězce později nakonfigurujete aplikaci tak, aby používala prostředek služby Azure Signal.

    Připojovací řetězec bude mít následující formát:
    
    `Endpoint=<service_endpoint>;AccessKey=<access_key>;`
