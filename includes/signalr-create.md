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
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021037"
---
1. Chcete-li vytvořit prostředek služby Azure SignalR, nejprve se přihlaste k [portálu Azure](https://portal.azure.com). V levé horní části stránky vyberte **+ Vytvořit prostředek**. Do **textového** pole Hledat na Marketplace zadejte **službu SignalR**.

2. Ve výsledcích vyberte **Službu SignalR** a vyberte **vytvořit**.

3. Na nové stránce nastavení **SignalR** přidejte následující nastavení pro nový prostředek SignalR:

    | Name (Název) | Doporučená hodnota | Popis |
    | ---- | ----------------- | ----------- |
    | Název prostředku | *testsignalr* | Zadejte jedinečný název prostředku, který se použije pro prostředek služby SignalR. Název musí být řetězec 1 až 63 znaků a musí obsahovat`-`pouze čísla, písmena a pomlčku ( ) znak. Název nemůže začínat ani končit pomlčkou a po sobě jdoucí pomlčky nejsou platné.|
    | Předplatné | Zvolte vaše předplatné. |  Vyberte předplatné Azure, které chcete k otestování služby SignalR použít. Pokud má váš účet jenom jedno předplatné, je automaticky vybrané a rozevírací možnost **Předplatné** se nezobrazí.|
    | Skupina prostředků | Vytvoření skupiny prostředků s názvem *SignalRTestResources*| Vyberte nebo vytvořte skupinu prostředků pro prostředek služby SignalR. Tato skupina je užitečná pro uspořádání více prostředků, které můžete chtít odstranit současně odstraněním skupiny prostředků. Další informace najdete [v tématu Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/management/overview.md). |
    | Umístění | *USA – východ* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |
    | Cenová úroveň | *Zdarma* | V současné době jsou k dispozici **možnosti Free** a **Standard.** |
    | Připnout na řídicí panel | ✔ | Toto políčko zaškrtněte, chcete-li mít prostředek připnutý na řídicí panel, aby byl snadněji k nalezení. |

4. Vyberte **Vytvořit**. Nasazení může trvat několik minut.

5. Po dokončení nasazení vyberte **položku Klíče** v části **NASTAVENÍ**. Zkopírujte připojovací řetězec pro primární klíč. Tento řetězec později použijete ke konfiguraci aplikace tak, aby používala prostředek služby Azure SignalR.

    Připojovací řetězec bude mít následující formát:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
