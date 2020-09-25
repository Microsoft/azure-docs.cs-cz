---
title: Azure cache pro Redis s privátním propojením Azure (Preview)
description: Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. V tomto článku se naučíte, jak vytvořit Azure cache, Azure Virtual Network a privátní koncový bod pomocí Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 932d138a4b594aa51b73c365cc3e753f49f886f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328963"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure cache pro Redis s privátním propojením Azure (Public Preview)
V tomto článku se dozvíte, jak vytvořit virtuální síť a mezipaměť Azure pro instanci Redis s privátním koncovým bodem pomocí Azure Portal. Naučíte se také, jak přidat privátní koncový bod do existující služby Azure cache pro instanci Redis.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

> [!NOTE]
> Tato funkce je aktuálně ve verzi Public Preview pro omezené oblasti. Pokud nemáte možnost vytvořit privátní koncový bod, [kontaktujte nás](mailto:azurecache@microsoft.com). Chcete-li použít soukromé koncové body, je nutné, aby byla instance Azure cache for Redis vytvořena po 28. července 2020.
>
> Oblasti s přístupem Public Preview: Středozápadní USA, Střed USA – sever, Západní USA, Východní USA 2, Norsko – východ, Evropa – sever, východní Asie, Japonsko – východ a Indie – střed.
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Vytvoření privátního koncového bodu s novou mezipamětí Azure pro instanci Redis 

V této části vytvoříte novou mezipaměť Azure pro instanci Redis s privátním koncovým bodem.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Vyberte vytvořit prostředek.":::

2. Na stránce **Nový** vyberte **síť** a pak vyberte **virtuální síť**.

3. Pokud chcete vytvořit virtuální síť, vyberte **Přidat** .

4. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit tato virtuální síť. | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit virtuální síť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název** | Zadejte název virtuální sítě. | Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. | 
   | **Oblast** | Rozevírací seznam a vyberte oblast. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši virtuální síť. |

5. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

6. Na kartě **IP adresy** zadejte **adresní prostor IPv4** jako jednu nebo více předpon adres v zápisu CIDR (např. 192.168.1.0/24).

7. V části **název podsítě**klikněte na **výchozí** a upravte vlastnosti podsítě.

8. V podokně **Upravit podsíť** zadejte **název podsítě** a **Rozsah adres podsítě**. Rozsah adres podsítě by měl být v zápisu CIDR (např. 192.168.1.0/24). Musí být obsažený v adresním prostoru virtuální sítě.

9. Vyberte **Uložit**.

10. Vyberte kartu **Revize + vytvořit** nebo klikněte na tlačítko **Revize + vytvořit** .

11. Ověřte, jestli jsou všechny informace správné, a kliknutím na **vytvořit** zřídíte virtuální síť.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Vytvoření mezipaměti Azure pro instanci Redis s privátním koncovým bodem
Chcete-li vytvořit instanci mezipaměti, postupujte podle těchto kroků.

1. Vraťte se na domovskou stránku Azure Portal nebo otevřete nabídku bočního panelu a pak vyberte **vytvořit prostředek**. 
   
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

1. Vyberte kartu **síť** nebo klikněte na tlačítko **sítě** v dolní části stránky.

1. Na kartě **sítě** vyberte pro metodu připojení **privátní koncový bod** .

1. Klikněte na tlačítko **Přidat** a vytvořte tak privátní koncový bod.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="V článku sítě přidejte privátní koncový bod.":::

1. Na stránce **Vytvoření privátního koncového bodu** nakonfigurujte nastavení privátního koncového bodu pomocí virtuální sítě a podsítě, kterou jste vytvořili v poslední části, a vyberte **OK**. 

1. Vyberte kartu **Další: Upřesnit** nebo klikněte na tlačítko **Další: Upřesnit** v dolní části stránky.

1. Na kartě **Upřesnit** pro instanci mezipaměti Basic nebo Standard vyberte přepínač Povolit, pokud chcete povolit port bez TLS.

1. Na kartě **Upřesnit** pro instanci mezipaměti úrovně Premium nakonfigurujte nastavení pro port bez TLS, clusteringu a trvalost dat.


1. Vyberte kartu **Další: značky** nebo klikněte na tlačítko **Další: značky** v dolní části stránky.

1. Volitelně můžete na kartě **značky** zadat název a hodnotu, pokud chcete prostředek zařadit do kategorií. 

1. Vyberte **zkontrolovat + vytvořit**. Přejdete na kartu Revize + vytvořit, kde Azure ověřuje vaši konfiguraci.

1. Po zobrazení zprávy se zobrazeným zeleným ověřením vyberte **vytvořit**.

Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled**služby Azure cache pro Redis   . Pokud se **stav**   zobrazuje jako **spuštěno**, mezipaměť je připravena k použití. 
    

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Vytvoření privátního koncového bodu s existující službou Azure cache pro instanci Redis 

V této části přidáte privátní koncový bod do existující služby Azure cache pro instanci Redis. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě 
Pokud chcete vytvořit virtuální síť, postupujte podle těchto kroků.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.

2. Na stránce **Nový** vyberte **síť** a pak vyberte **virtuální síť**.

3. Pokud chcete vytvořit virtuální síť, vyberte **Přidat** .

4. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit tato virtuální síť. | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit virtuální síť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název** | Zadejte název virtuální sítě. | Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. | 
   | **Oblast** | Rozevírací seznam a vyberte oblast. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši virtuální síť. |

5. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

6. Na kartě **IP adresy** zadejte **adresní prostor IPv4** jako jednu nebo více předpon adres v zápisu CIDR (např. 192.168.1.0/24).

7. V části **název podsítě**klikněte na **výchozí** a upravte vlastnosti podsítě.

8. V podokně **Upravit podsíť** zadejte **název podsítě** a **Rozsah adres podsítě**. Rozsah adres podsítě by měl být v zápisu CIDR (např. 192.168.1.0/24). Musí být obsažený v adresním prostoru virtuální sítě.

9. Vyberte **Uložit**.

10. Vyberte kartu **Revize + vytvořit** nebo klikněte na tlačítko **Revize + vytvořit** .

11. Ověřte, jestli jsou všechny informace správné, a kliknutím na **vytvořit** zřídíte virtuální síť.

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu 

Pokud chcete vytvořit privátní koncový bod, postupujte podle těchto kroků.

1. V Azure Portal vyhledejte **mezipaměť Azure pro Redis** a stiskněte klávesu ENTER nebo ji vyberte v návrzích hledání.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Vyhledejte službu Azure cache pro Redis.":::

2. Vyberte instanci mezipaměti, do které chcete přidat privátní koncový bod.

3. Na levé straně obrazovky vyberte **(Preview) soukromý koncový bod**.

4. Kliknutím na tlačítko **privátního koncového bodu** vytvořte soukromý koncový bod.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Přidat privátní koncový bod":::

5. Na **stránce vytvoření privátního koncového bodu**nakonfigurujte nastavení privátního koncového bodu.

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tento soukromý koncový bod | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit privátní koncový bod a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název** | Zadejte název privátního koncového bodu. | Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. | 
   | **Oblast** | Rozevírací seznam a vyberte oblast. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat váš privátní koncový bod. |

6. Klikněte na tlačítko **Další: prostředek** ve spodní části stránky.

7. Na kartě **prostředek** vyberte své předplatné, zvolte typ prostředku `Microsoft.Cache/Redis` a pak vyberte mezipaměť, ke které chcete privátní koncový bod připojit.

8. Klikněte na tlačítko **Další: Konfigurace** v dolní části stránky.

9. Na kartě **Konfigurace** vyberte virtuální síť a podsíť, kterou jste vytvořili v předchozí části.

10. Klikněte na tlačítko **Další: značky** v dolní části stránky.

11. Volitelně můžete na kartě **značky** zadat název a hodnotu, pokud chcete prostředek zařadit do kategorií.

12. Vyberte **zkontrolovat + vytvořit**. Přejdete na kartu **Revize + vytvořit**   , kde Azure ověřuje vaši konfiguraci.

13. Po zobrazení zprávy se zobrazeným zeleným **ověřením** vyberte **vytvořit**.


## <a name="next-steps"></a>Další kroky

Další informace o privátním propojení Azure najdete v [dokumentaci k privátním odkazům Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

