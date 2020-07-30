---
title: Azure cache pro Redis s privátním propojením Azure (Preview)
description: Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. V tomto článku se naučíte, jak pomocí Azure Portal vytvořit mezipaměť Azure, virtuální síť Azure a soukromý koncový bod.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5db756b60330cdac4e43e13bfe29d9397f87af50
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421650"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure cache pro Redis s privátním propojením Azure (Preview)
Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. 

V tomto článku se dozvíte, jak vytvořit službu Azure cache, virtuální síť Azure a soukromý koncový bod pomocí Azure Portal.  

> [!IMPORTANT]
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview. 
> 

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview – Pokud vás zajímáte, [kontaktujte nás](mailto:azurecache@microsoft.com) .
>

## <a name="create-a-cache"></a>Vytvoření mezipaměti
1. Pokud chcete vytvořit mezipaměť, přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Vyberte vytvořit prostředek.":::
   
1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Vyberte mezipaměť Azure pro Redis.":::
   
1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Umístění** | Rozevírací seznam a vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
   | **Cenová úroveň** | Rozevírací seznam a vyberte [cenovou úroveň](https://azure.microsoft.com/pricing/details/cache/). |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |
   
1. Vyberte **Vytvořit**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Vytvořte mezipaměť Azure pro Redis.":::
   
   Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Mezipaměť Azure pro Redis se vytvořila.":::

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. Vyberte **Vytvořit prostředek**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Vyberte vytvořit prostředek.":::

2. Na stránce **Nový** vyberte **síť** a pak vyberte **virtuální síť**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Vytvořte virtuální síť.":::

3. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Rozevírací seznam a vyberte své předplatné.                                  |
    | Skupina prostředků   | Rozevírací seznam a vyberte skupinu prostředků. |
    | **Podrobnosti instance** |                                                                 |
    | Název             | Napište**\<virtual-network-name>**                                    |
    | Oblast           | Vybrali**\<region-name>** |

4. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

5. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor IPv4 | Napište**\<IPv4-address-space>** |

6. V části **název podsítě**vyberte slovo **výchozí**.

7. V **Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Napište**\<subnet-name>** |
    | Rozsah adres podsítě | Napište**\<subnet-address-range>**

8. Vyberte **Uložit**.

9. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

10. Vyberte **Vytvořit**.


## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu 

V této části vytvoříte privátní koncový bod a připojíte ho k mezipaměti, kterou jste vytvořili dříve.

1. Vyhledejte **privátní odkaz** a stiskněte klávesu ENTER nebo ho vyberte v návrzích hledání.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Vyhledejte privátní odkaz.":::

2. Na levé straně obrazovky vyberte **soukromé koncové body**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Vyberte privátní odkaz.":::

3. Vyberte tlačítko **+ Přidat** a vytvořte tak privátní koncový bod. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Přidejte privátní odkaz.":::

4. Na **stránce vytvoření privátního koncového bodu**nakonfigurujte nastavení privátního koncového bodu.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Rozevírací seznam a vyberte své předplatné. |
    | Skupina prostředků | Rozevírací seznam a vyberte skupinu prostředků. |
    | **PODROBNOSTI INSTANCE** |  |
    | Název |Zadejte název privátního koncového bodu.  |
    | Oblast |Rozevírací seznam a vyberte umístění. |
    |||

5. V dolní části stránky vyberte tlačítko **Další: prostředek** .

6. Na kartě **prostředek** vyberte své předplatné, zvolte typ prostředku Microsoft. cache/Redis a pak vyberte mezipaměť, kterou jste provedli v předchozí části.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Prostředky pro privátní propojení.":::

7. V dolní části stránky vyberte tlačítko **Další: Konfigurace** .

8. Na kartě **Konfigurace** vyberte virtuální síť a podsíť, kterou jste vytvořili v předchozí části.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Konfigurace pro privátní propojení.":::

9. V dolní části stránky vyberte tlačítko **Další: značky** .

10. Pokud chcete prostředek kategorizovat, zadejte na kartě **značky** název a hodnotu. Tento krok je volitelný.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Značky pro privátní propojení":::

11. Vyberte **zkontrolovat + vytvořit**. Přejdete na kartu **Revize + vytvořit**   , kde Azure ověřuje vaši konfiguraci.

12. Po zobrazení zeleného **ověření** se zobrazí zpráva vyberte **vytvořit**.


## <a name="next-steps"></a>Další kroky

Další informace o privátních odkazech najdete v [dokumentaci k privátním odkazům Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

