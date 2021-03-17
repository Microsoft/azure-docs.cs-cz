---
title: Povolení redundance zóny pro Azure cache pro Redis (Preview)
description: Přečtěte si, jak nastavit redundanci zóny pro Redis instance úrovně Premium a Enterprise pro Azure.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0623f47528d0530838f62c28cf5546e1e66c187b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508261"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Povolení redundance zóny pro Azure cache pro Redis (Preview)
V tomto článku se dozvíte, jak nakonfigurovat instanci redundantní mezipaměti Azure v zóně pomocí Azure Portal.

Azure cache pro Redis úrovně Standard, Premium a Enterprise nabízí integrovanou redundanci tím, že hostuje každou mezipaměť na dvou vyhrazených virtuálních počítačích (VM). I když se tyto virtuální počítače nacházejí v samostatných [doménách selhání a aktualizacích Azure](../virtual-machines/availability.md) a vysoce dostupné, jsou náchylné k selhání na úrovni datacentra. Azure cache pro Redis také podporuje redundanci zóny ve svých úrovních Premium a Enterprise. Zóna – redundantní mezipaměť běží na virtuálních počítačích, které jsou rozloženy v několika [zónách dostupnosti](../availability-zones/az-overview.md). Poskytuje vyšší odolnost a dostupnost.

## <a name="prerequisites"></a>Požadavky
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
    | **Typ mezipaměti** | Vyberte mezipaměť [úrovně Premium nebo Enterprise](https://azure.microsoft.com/pricing/details/cache/) . |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
   
1. Na stránce **Upřesnit** pro mezipaměť úrovně Premium vyberte **počet replik**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Počet replik":::

1. Vyberte **zóny dostupnosti**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Zóny dostupnosti":::

1. Ve výchozím nastavení ponechte ostatní možnosti. 

    > [!NOTE]
    > Podpora redundance zóny funguje jenom s aktuálně neclusterovanými a neclusterovanými mezipamětmi, které jsou momentálně replikované. Kromě toho nepodporuje privátní propojení, škálování, trvalost dat ani import/export.
    >

1. Klikněte na **Vytvořit**. 
   
    Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.
   
    > [!NOTE]
    > Po vytvoření mezipaměti se zóny dostupnosti nedají změnit.
    >

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)