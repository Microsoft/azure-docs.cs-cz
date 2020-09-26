---
title: Přidání replik do Azure cache pro Redis (Preview)
description: Přečtěte si, jak přidat další repliky do mezipaměti Azure úrovně Premium pro instance Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342587"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Přidání replik do Azure cache pro Redis (Preview)
V tomto článku se dozvíte, jak nastavit instanci Azure cache s dalšími replikami pomocí Azure Portal.

Služba Azure cache pro Redis úrovně Standard a Premium nabízí redundanci hostováním každé mezipaměti na dvou vyhrazených virtuálních počítačích (VM). Tyto virtuální počítače jsou nakonfigurované jako primární a replikované. Když dojde k nedostupnosti primárního virtuálního počítače, replika ho detekuje a převezme se automaticky jako nové primární. Nyní můžete zvýšit počet replik v mezipaměti Premium na tři, což vám poskytne celkem čtyři virtuální počítače, které mezipaměť zálohují. Výsledkem více replik může být vyšší odolnost, než může jedna replika poskytnout.

> [!IMPORTANT]
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview. 
> 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview – Pokud vás zajímáte, [kontaktujte nás](mailto:azurecache@microsoft.com) .
>

## <a name="create-a-cache"></a>Vytvoření mezipaměti
Mezipaměť vytvoříte pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.
  
1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::
   
1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť.
   
    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude * \<DNS name> . Redis.cache.Windows.NET*. | 
    | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
    | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
    | **Umístění** | Rozevírací seznam a vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
    | **Cenová úroveň** | Rozevírací seznam a vyberte mezipaměť [úrovně Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
    | **Počet replik** | Posunutím vyberte počet replik. | Výchozí hodnota je 1. |
   
1. Po výběru mezipaměti úrovně Premium se zobrazí dotaz, zda chcete povolit cluster Redis nebo ne. Vynechejte **clustering** *zakázaný*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Nakonfigurujte cluster Redis.":::

    > [!NOTE]
    > Podpora více replik funguje pouze v aktuálně neclusterovaných mezipamětích.
    >

1. Klikněte na **Vytvořit**. 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Vytvořte mezipaměť Azure pro Redis.":::
   
    Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

    > [!NOTE]
    > Počet replik v mezipaměti nelze po vytvoření změnit.
    >

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)
