---
title: 'Rychlý Start: vytvoření mezipaměti podnikové úrovně'
description: V tomto rychlém startu se dozvíte, jak vytvořit instanci Azure cache pro úroveň Redis Enterprise.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 95f6e2cf7803ff0b152f33c08f170725b5a0e94e
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95795675"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Rychlý Start: vytvoření mezipaměti podnikové vrstvy (Preview)

Azure cache pro podnikové úrovně Redis poskytuje plně integrovaný a spravovaný [Redis podnik](https://redislabs.com/redis-enterprise/) v Azure. Jsou aktuálně k dispozici ve verzi Preview. V této verzi Preview jsou dvě nové úrovně:
* Enterprise, který používá pro ukládání dat nestálou paměť (DRAM) na virtuálním počítači
* Enterprise Flash, který pro ukládání dat používá nestálou i nestálou paměť (NVMe nebo SSD).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat předplatné Azure. Pokud ho ještě nemáte, vytvořte [účet](https://azure.microsoft.com/). Další informace najdete v tématu [požadavky na podnikovou vrstvu](cache-overview.md#enterprise-tier-requirements).

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
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Základy na podnikové úrovni":::

   > [!NOTE] 
   > Než budete pokračovat, nezapomeňte zaškrtnout políčko "terms".
   >

1. Vyberte **Další: sítě** a přeskočit.

   > [!NOTE] 
   > Možnost privátního odkazu je zastaralá a nemusí být k dispozici okamžitě ve vaší oblasti.
   >

1. Vyberte **Další: Upřesnit** a nastavte **zásady clusteringu** na **Enterprise**.
   
   Můžete ponechat výchozí nastavení nebo je podle potřeby změnit. Když zapnete **Povolení přístupu přes protokol TLS**, musíte k přístupu k nové mezipaměti z aplikace použít protokol TLS.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Rozšířená úroveň Enterprise":::

   > [!NOTE] 
   > Redis moduly se zatím nepodporují ve vrstvě Enterprise Flash. Pokud máte v úmyslu použít modul Redis, nezapomeňte zvolit mezipaměť podnikové vrstvy.
   >
   
1. Vyberte **Další: značky** a přeskočit.

1. Vyberte **Další: Zkontrolovat a vytvořit**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Souhrn úrovně Enterprise":::

1. Zkontrolujte nastavení a klikněte na **vytvořit**.
   
   Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit instanci podnikové vrstvy Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)

