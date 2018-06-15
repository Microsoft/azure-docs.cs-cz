---
title: zahrnout soubor
description: zahrnout soubor
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e035b49d9e386287baf67bba756f7b58a764acc5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34173114"
---
Pokud chcete vytvořit mezipaměť, přihlaste se nejdříve k webu [Azure Portal](https://portal.azure.com) a klikněte na **Vytvořit prostředek** > **Databáze** > **Redis Cache**.

![Nová mezipaměť](media/redis-cache-create/redis-cache-new-cache-menu.png)

V části **Nová mezipaměť Redis** nakonfigurujte nastavení pro novou mezipaměť.

| Nastavení      | Navrhovaná hodnota  | Popis |
| ------------ |  ------- | -------------------------------------------------- |
| **Název DNS** | Globálně jedinečný název | Název mezipaměti musí být řetězec o délce 1 až 63 znaků a smí obsahovat jenom čísla, písmena a znak `-`. Název mezipaměti nesmí začínat ani končit znakem `-` a po sobě jdoucí znaky `-` nejsou platné.  | 
| **Předplatné** | Vaše předplatné | Předplatné, ve kterém se tato nová Azure Redis Cache vytvoří | 
| **Skupina prostředků** |  *TestResources* | Název nové skupiny prostředků, ve které chcete vytvořit mezipaměť. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. | 
| **Umístění** | Východ USA | Zvolte [oblast](https://azure.microsoft.com/regions/) v blízkosti jiných služeb, které budou používat danou mezipaměť. |
| **[Cenová úroveň](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (mezipaměť 250 MB) |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v [přehledu Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
| **Připnout na řídicí panel** |  Vybráno | Připněte novou mezipaměť na řídicí panel, aby ji bylo možné snadno najít. |

![Vytvoření mezipaměti](media/redis-cache-create/redis-cache-cache-create.png) 

Po konfiguraci nastavení nové mezipaměti klikněte na **Vytvořit**. 

Vytvoření mezipaměti může několik minut trvat. Pokud chcete zkontrolovat stav, můžete průběh sledovat na řídicím panelu. Po vytvoření má nová mezipaměť stav **Spuštěno** a je připravená k použití.

![Mezipaměť vytvořena](media/redis-cache-create/redis-cache-cache-created.png)

