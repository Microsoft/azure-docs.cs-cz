---
title: Povolení redundance zóny pro Azure cache pro Redis (Preview)
description: Zjistěte, jak nastavit redundanci zóny pro Azure cache na úrovni Premium pro instance Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 33c346fa2e4572799ad6341bd5115cdd6e3b9ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569978"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Povolení redundance zóny pro Azure cache pro Redis (Preview)
V tomto článku se dozvíte, jak nakonfigurovat instanci redundantní mezipaměti Azure v zóně pomocí Azure Portal.

Azure cache pro Redis úrovně Standard a Premium nabízí integrovanou redundanci tím, že hostuje každou mezipaměť na dvou vyhrazených virtuálních počítačích (VM). I když se tyto virtuální počítače nacházejí v samostatných [doménách selhání a aktualizacích Azure](/azure/virtual-machines/windows/manage-availability) a vysoce dostupné, jsou náchylné k selhání na úrovni datacentra. Azure cache pro Redis také podporuje redundanci zóny ve své úrovni Premium. Zóna – redundantní mezipaměť běží na virtuálních počítačích, které jsou rozloženy v několika [zónách dostupnosti](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures). Poskytuje vyšší odolnost a dostupnost.

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
   
1. Na stránce **základy** nakonfigurujte nastavení pro novou mezipaměť.
   
    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Předplatné** | Vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
    | **Skupina prostředků** | Vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
    | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude * \<DNS name> . Redis.cache.Windows.NET*. | 
    | **Umístění** | Vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
    | **Typ mezipaměti** | Vyberte mezipaměť [úrovně Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
   
1. Na stránce **Upřesnit** vyberte **počet replik**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::

1. Vyberte **zóny dostupnosti**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::

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
