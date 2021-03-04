---
title: 'Rychlý Start: vytvoření mezipaměti Redis Enterprise'
description: V tomto rychlém startu se dozvíte, jak vytvořit instanci Azure cache pro Redis v podnikových vrstvách.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8dbde1f74c700e772823d61b3e35a464f9bbc17e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120959"
---
# <a name="quickstart-create-a-redis-enterprise-cache-preview"></a>Rychlý Start: vytvoření mezipaměti Redis Enterprise (Preview)

Azure cache pro podnikové úrovně Redis poskytuje plně integrovaný a spravovaný [Redis podnik](https://redislabs.com/redis-enterprise/) v Azure. Jsou aktuálně k dispozici ve verzi Preview. V této verzi Preview jsou dvě nové úrovně:
* Enterprise, který používá pro ukládání dat nestálou paměť (DRAM) na virtuálním počítači
* Enterprise Flash, který pro ukládání dat používá nestálou i nestálou paměť (NVMe nebo SSD).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat předplatné Azure. Pokud ho ještě nemáte, vytvořte [účet](https://azure.microsoft.com/). Další informace najdete v tématu [zvláštní požadavky na podnikové úrovně](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
1. Pokud chcete vytvořit mezipaměť, přihlaste se k Azure Portal pomocí odkazu v rámci pozvánky ve verzi Preview a vyberte **vytvořit prostředek**.

1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Vybrat mezipaměť Azure pro Redis":::
   
1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude *\<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Umístění** | Rozevírací seznam a vyberte umístění. | Úrovně Enterprise jsou dostupné v omezených oblastech Azure ve verzi Preview. |
   | **Typ mezipaměti** | Rozevírací seznam a vyberte úroveň *podnikového* a podnikového *Flash* a velikost. |  Úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro mezipaměť. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Karta základy na podnikové úrovni":::

   > [!NOTE] 
   > Než budete pokračovat, nezapomeňte zaškrtnout políčko "terms".
   >

1. Vyberte **Další: sítě** a přeskočit.

1. Vyberte **Další: Upřesnit** a nastavte **zásady clusteringu** na **Enterprise**. Povolit **přístup bez TLS jenom** v případě, že se chystáte připojit k nové mezipaměti bez použití protokolu TLS. To však nedoporučujeme.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Karta Upřesnit na podnikové úrovni":::

1. Vyberte **Další: značky** a přeskočit.

1. Vyberte **Další: Zkontrolovat a vytvořit**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Kontrola úrovně Enterprise + vytvořit kartu":::

1. Zkontrolujte nastavení a klikněte na **vytvořit**.
   
   Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit instanci podnikové vrstvy Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)

