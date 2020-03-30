---
title: zahrnout soubor
description: zahrnout soubor
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73719108"
---
1. Pokud chcete vytvořit mezipaměť, přihlaste se na [portál Azure](https://portal.azure.com) a vyberte **Vytvořit prostředek**. 
   
   ![Vyberte Vytvořit zdroj.](media/redis-cache-create/create-a-resource.png)
   
1. Na stránce **Nový** vyberte **Databáze** a pak vyberte **Azure Cache for Redis**.
   
   ![Vyberte Azure Cache pro Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na stránce **Nová mezipaměť Redis** nakonfigurujte nastavení nové mezipaměti.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec mezi 1 a 63 znaky, který obsahuje pouze čísla, písmena nebo pomlčky. Název musí začínat a končit číslem nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* instance mezipaměti bude * \<název DNS>.redis.cache.windows.net*. | 
   | **Předplatné** | Rozbalte a vyberte předplatné. | Předplatné, pod kterým chcete vytvořit tuto novou instanci Azure Cache for Redis. | 
   | **Skupina prostředků** | Rozevírací seznam a výběr skupiny prostředků nebo vyberte **Vytvořit nový** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve kterém chcete vytvořit mezipaměť a další prostředky. Umístěním všech prostředků aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Umístění** | Rozbalte a vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) v blízkosti jiných služeb, které budou používat vaši mezipaměť. |
   | **Cenová úroveň** | Rozevírací položky a vyberte [cenovou úroveň](https://azure.microsoft.com/pricing/details/cache/). |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v [tématu Azure Cache for Redis Overview](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Vyberte **Vytvořit**. 
   
   ![Vytvoření mezipaměti Azure pro Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Vytvoření mezipaměti chvíli trvá. Průběh můžete sledovat na **stránce** Přehled mezipaměti Azure Cache for Redis. **Pokud se stav** zobrazuje jako **Spuštěno**, je mezipaměť připravena k použití.
   
   ![Vytvořena mezipaměť Azure pro redis](media/redis-cache-create/redis-cache-cache-created.png)

