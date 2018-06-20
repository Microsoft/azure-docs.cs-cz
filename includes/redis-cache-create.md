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
ms.openlocfilehash: 6a96bd7c3d7d02f181a7d7513edb3bb39881274f
ms.sourcegitcommit: 7de1432648c4ff3bcd09530c079418477d9f4d00
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35719422"
---
1. Pokud chcete vytvořit mezipaměť, přihlaste se nejdříve k webu [Azure Portal](https://portal.azure.com). Potom vyberte **Vytvořit prostředek** > **Databáze** > **Redis Cache**.

    ![Nová mezipaměť](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. V části **Nová mezipaměť Redis** nakonfigurujte nastavení pro novou mezipaměť.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název DNS** | Globálně jedinečný název | Název mezipaměti. Musí to být řetězec o délce 1 až 63 znaků a smí obsahovat jenom čísla, písmena a znak `-`. Název mezipaměti nesmí začínat ani končit znakem `-` a po sobě jdoucí znaky `-` nejsou platné.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se tato nová instance Azure Redis Cache vytvoří | 
    | **Skupina prostředků** |  *TestResources* | Název nové skupiny prostředků, ve které chcete vytvořit mezipaměť. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků se například odstraní všechny prostředky, které jsou přidružené k dané aplikaci. | 
    | **Umístění** | Východ USA | Zvolte [oblast](https://azure.microsoft.com/regions/) v blízkosti jiných služeb, které budou používat danou mezipaměť. |
    | **[Cenová úroveň](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (mezipaměť 250 MB) |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v [přehledu Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
    | **Připnout na řídicí panel** |  Vybráno | Připněte novou mezipaměť na řídicí panel, aby ji bylo možné snadno najít. |

    ![Vytvoření mezipaměti](media/redis-cache-create/redis-cache-cache-create.png) 

3. Po dokončení konfigurace nastavení nové mezipaměti vyberte **Vytvořit**. 

    Vytvoření mezipaměti může několik minut trvat. Pokud chcete zkontrolovat stav, můžete průběh sledovat na řídicím panelu. Po vytvoření je nová mezipaměť ve stavu **Spuštěno** a je připravená k použití.

    ![Mezipaměť vytvořena](media/redis-cache-create/redis-cache-cache-created.png)

