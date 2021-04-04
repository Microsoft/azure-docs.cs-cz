---
title: Nastavte verzi Redis pro Azure cache pro Redis (Preview).
description: Informace o tom, jak nakonfigurovat verzi Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9f48de7ef5d9525a995af4ebbd12c5f14f40189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93349133"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Nastavte verzi Redis pro Azure cache pro Redis (Preview).
V tomto článku se dozvíte, jak nakonfigurovat verzi Redis softwaru, která se má použít s instancí mezipaměti. Azure cache for Redis nabízí nejnovější hlavní verzi Redis a alespoň jednu předchozí verzi. Tyto verze se pravidelně aktualizují, protože se uvolní novější software Redis. Můžete si vybrat mezi dvěma dostupnými verzemi. Mějte na paměti, že mezipaměť bude upgradována na další verzi automaticky v případě, že verze, kterou aktuálně používá, již není podporována.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

## <a name="create-a-cache"></a>Vytvoření mezipaměti
Mezipaměť vytvoříte pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.
  
1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::
   
1. Na stránce **základy** nakonfigurujte nastavení pro novou mezipaměť.
   
    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Předplatné** | Vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
    | **Skupina prostředků** | Vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
    | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude *\<DNS name> . Redis.cache.Windows.NET*. | 
    | **Umístění** | Vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
    | **Typ mezipaměti** | Vyberte [úroveň mezipaměti a velikost](https://azure.microsoft.com/pricing/details/cache/). |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
   
1. Na stránce **Upřesnit** vyberte verzi Redis, kterou chcete použít.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Verze Redis":::

1. Klikněte na **Vytvořit**. 
   
    Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

    > [!NOTE]
    > V tuto chvíli není možné po vytvoření mezipaměti změnit verzi Redis.
    >

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)
