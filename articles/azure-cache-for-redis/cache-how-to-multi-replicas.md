---
title: Přidání replik do Azure cache pro Redis (Preview)
description: Přečtěte si, jak přidat další repliky do mezipaměti Azure úrovně Premium pro instance Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91566784"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Přidání replik do Azure cache pro Redis (Preview)
V tomto článku se dozvíte, jak nastavit instanci Azure cache s dalšími replikami pomocí Azure Portal.

Služba Azure cache pro Redis úrovně Standard a Premium nabízí redundanci hostováním každé mezipaměti na dvou vyhrazených virtuálních počítačích (VM). Tyto virtuální počítače jsou nakonfigurované jako primární a replikované. Když dojde k nedostupnosti primárního virtuálního počítače, replika ho detekuje a převezme se automaticky jako nové primární. Nyní můžete zvýšit počet replik v mezipaměti Premium na tři, což vám poskytne celkem čtyři virtuální počítače, které mezipaměť zálohují. Výsledkem více replik může být vyšší odolnost, než může jedna replika poskytnout.

> [!IMPORTANT]
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview. 
> 

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview – Pokud vás zajímáte, [kontaktujte nás](mailto:azurecache@microsoft.com) .
>

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
    | **Typ mezipaměti** | Vyberte mezipaměť [úrovně Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
   
1. Na stránce **Upřesnit** vyberte **počet replik**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Počet replik.":::

1. Ve výchozím nastavení ponechte ostatní možnosti. 

    > [!NOTE]
    > Podpora více replik funguje pouze v aktuálně neclusterovaných mezipamětích.
    >

1. Klikněte na **Vytvořit**.
   
    Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

    > [!NOTE]
    > Počet replik v mezipaměti nelze po vytvoření změnit.
    >

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)
