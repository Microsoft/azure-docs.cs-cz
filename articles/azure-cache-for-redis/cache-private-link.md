---
title: Azure cache pro Redis s privátním propojením Azure (Preview)
description: Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. V tomto článku se naučíte, jak vytvořit Azure cache, Azure Virtual Network a privátní koncový bod pomocí Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 31ae4605b6cc9e26c89beea692fe61fcbda49c4c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621497"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure cache pro Redis s privátním propojením Azure (Public Preview)
V tomto článku se dozvíte, jak vytvořit virtuální síť a mezipaměť Azure pro instanci Redis s privátním koncovým bodem pomocí Azure Portal. Naučíte se také, jak přidat privátní koncový bod do existující služby Azure cache pro instanci Redis.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě Azure cache pro Redis využívající privátní propojení Azure. 

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

> [!IMPORTANT]
> Chcete-li použít soukromé koncové body, je nutné, aby byla instance Azure cache for Redis vytvořena po 28. července 2020.
> V současné době se geografická replikace, pravidla brány firewall, podpora konzoly portálu, více koncových bodů na mezipamětní mezipaměť, trvalost do firewallu a virtuální sítě vložené do mezipaměti nepodporují. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Vytvoření privátního koncového bodu s novou mezipamětí Azure pro instanci Redis 

V této části vytvoříte novou mezipaměť Azure pro instanci Redis s privátním koncovým bodem.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Vyberte vytvořit prostředek.":::

2. Na stránce **Nový** vyberte **síť** a pak vyberte **virtuální síť**.

3. Pokud chcete vytvořit virtuální síť, vyberte **Přidat** .

4. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit tato virtuální síť. | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit virtuální síť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název** | Zadejte název virtuální sítě. | Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. | 
   | **Oblast** | Rozevírací seznam a vyberte oblast. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši virtuální síť. |

5. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

6. Na kartě **IP adresy** zadejte **adresní prostor IPv4** jako jednu nebo více předpon adres v zápisu CIDR (např. 192.168.1.0/24).

7. V části **název podsítě** klikněte na **výchozí** a upravte vlastnosti podsítě.

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
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude *\<DNS name> . Redis.cache.Windows.NET*. | 
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

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na kartu Revize + vytvořit, kde Azure ověřuje vaši konfiguraci.

1. Po zobrazení zprávy se zobrazeným zeleným ověřením vyberte **vytvořit**.

Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití. 
    
> [!IMPORTANT]
> 
> K dispozici je `publicNetworkAccess` příznak, který je `Disabled` ve výchozím nastavení nastaven. 
> Tento příznak slouží k tomu, aby bylo možné volitelně dovolit přístup k mezipaměti veřejného i privátního koncového bodu, pokud je nastaven na hodnotu `Enabled` . Pokud je tato možnost nastavená na `Disabled` , umožní přístup jenom k privátnímu koncovému bodu. Tuto hodnotu můžete nastavit na `Disabled` nebo `Enabled` s následující žádostí o opravu. Upravte hodnotu tak, aby odrážela příznak, který chcete mít v mezipaměti.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Aby bylo možné se připojit ke clusterované mezipaměti, je `publicNetworkAccess` nutné nastavit na `Disabled` a může to být pouze jedno připojení privátního koncového bodu. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Vytvoření privátního koncového bodu s existující službou Azure cache pro instanci Redis 

V této části přidáte privátní koncový bod do existující služby Azure cache pro instanci Redis. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě 
Pokud chcete vytvořit virtuální síť, postupujte podle těchto kroků.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**.

2. Na stránce **Nový** vyberte **síť** a pak vyberte **virtuální síť**.

3. Pokud chcete vytvořit virtuální síť, vyberte **Přidat** .

4. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit tato virtuální síť. | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit virtuální síť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název** | Zadejte název virtuální sítě. | Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. | 
   | **Oblast** | Rozevírací seznam a vyberte oblast. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši virtuální síť. |

5. Vyberte kartu **IP adresy** nebo klikněte na tlačítko **Další: IP adresy** v dolní části stránky.

6. Na kartě **IP adresy** zadejte **adresní prostor IPv4** jako jednu nebo více předpon adres v zápisu CIDR (např. 192.168.1.0/24).

7. V části **název podsítě** klikněte na **výchozí** a upravte vlastnosti podsítě.

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

5. Na **stránce vytvoření privátního koncového bodu** nakonfigurujte nastavení privátního koncového bodu.

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

12. Vyberte **Zkontrolovat a vytvořit**. Přejdete na kartu **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

13. Po zobrazení zprávy se zobrazeným zeleným **ověřením** vyberte **vytvořit**.

## <a name="faq"></a>Časté otázky

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Proč se nemůžu připojit k privátnímu koncovému bodu?
Pokud vaše mezipaměť již není vloženou mezipamětí virtuální sítě, nelze použít privátní koncové body s instancí mezipaměti. Pokud vaše instance mezipaměti používá nepodporovanou funkci (uvedená níže), nebudete se moct připojit k vaší privátní instanci koncového bodu. Kromě toho je potřeba instance mezipaměti vytvořit po 27 od července po použití privátních koncových bodů.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Jaké funkce nejsou podporovány soukromými koncovými body?
Geografická replikace, pravidla brány firewall, podpora konzoly portálu, více koncových bodů na Clusterovou mezipaměť, trvalost pro pravidla firewallu a redundanci zóny. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Jak můžu změnit svůj soukromý koncový bod tak, aby byl zakázán nebo povolen z veřejného přístupu k síti?
K dispozici je `publicNetworkAccess` příznak, který je `Disabled` ve výchozím nastavení nastaven. Tento příznak slouží k tomu, aby bylo možné volitelně dovolit přístup k mezipaměti veřejného i privátního koncového bodu, pokud je nastaven na hodnotu `Enabled` . Pokud je tato možnost nastavená na `Disabled` , umožní přístup jenom k privátnímu koncovému bodu. Tuto hodnotu můžete nastavit na `Disabled` nebo `Enabled` s následující žádostí o opravu. Upravte hodnotu tak, aby odrážela příznak, který chcete mít v mezipaměti.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Jsou povoleny skupiny zabezpečení sítě (NSG) pro privátní koncové body?
Ne, jsou zakázané u privátních koncových bodů. Pokud jsou však v podsíti jiné prostředky, bude na tyto prostředky platit vynucování NSG.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Jak se můžu připojit ke clusterované mezipaměti?
`publicNetworkAccess` musí být nastavená na `Disabled` a může to být jenom jedno připojení privátního koncového bodu.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Vzhledem k tomu, že instance privátního koncového bodu není ve virtuální síti, jak je přidružená k virtuální síti?
Je propojena pouze s vaší virtuální sítí. Vzhledem k tomu, že není ve vaší virtuální síti, nemusíte u závislých koncových bodů měnit pravidla NSG.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Jak můžu migrovat vloženou mezipaměť virtuální sítě do mezipaměti privátního koncového bodu?
Bude nutné odstranit vloženou mezipaměť virtuální sítě a vytvořit novou instanci mezipaměti s privátním koncovým bodem.

## <a name="next-steps"></a>Další kroky

* Další informace o privátním propojení Azure najdete v [dokumentaci k privátním odkazům Azure](../private-link/private-link-overview.md).
* Informace o porovnání různých možností izolace sítě pro instanci mezipaměti najdete v [dokumentaci k možnostem izolace sítě Azure cache pro Redis](cache-network-isolation.md).