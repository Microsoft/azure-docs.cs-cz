---
title: Postup škálování Azure mezipaměti Redis | Dokumentace Microsoftu
description: Zjistěte, jak se dá škálovat mezipaměť Azure pro instance Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: wesmc
ms.openlocfilehash: 009fbd3b86518758e9654fab547bab99bec369de
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105198"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Postup škálování Azure mezipaměti Redis
Mezipaměti Redis Azure má různé mezipaměti nabídek, které poskytují flexibilitu při výběru velikosti a funkcí mezipaměti. Po vytvoření mezipaměti je možné škálovat velikost a cenovou úroveň mezipaměti, pokud se změní požadavky aplikace. Tento článek popisuje, jak se dá škálovat mezipaměť pomocí webu Azure portal a nástrojů, jako je Azure PowerShell a rozhraní příkazového řádku Azure.

## <a name="when-to-scale"></a>Kdy škálovat
Můžete použít [monitorování](cache-how-to-monitor.md) funkce mezipaměti Azure pro určení toho, kdy se má změnit velikost mezipaměti a monitorovat stav a výkon mezipaměti Redis. 

Můžete monitorovat následující metriky pro určení toho, pokud je potřeba škálovat.

* Zatížení serveru Redis
* Využití paměti
* Šířka pásma sítě
* Využití procesoru

Pokud zjistíte, že mezipaměť už splňuje požadavky vaší aplikace, můžete škálovat pro větší nebo menší mezipaměti cenovou úroveň, která je nejvhodnější pro vaši aplikaci. Další informace o určení, která mezipaměť cenová úroveň používat, naleznete v tématu [jaké mezipaměti Azure Redis a jeho velikost pomocí](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Škálování mezipaměti
Se dá škálovat mezipaměť, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) v [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **škálování** z **nabídce prostředků**.

![Měřítko](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Vyberte požadovanou cenovou úroveň z **Vyberte cenovou úroveň** okna a kliknutím na **vyberte**.

![Cenová úroveň][redis-cache-pricing-tier-blade]


Je možné škálovat na jinou cenovou úroveň s následujícími omezeními:

* Nejde škálovat z vyšší cenovou úroveň na nižší cenovou úroveň.
  * Nejde škálovat z **Premium** dolů na do mezipaměti **standardní** nebo **základní** mezipaměti.
  * Nejde škálovat z **standardní** dolů na do mezipaměti **základní** mezipaměti.
* Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
* Nejde škálovat z **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálování od **základní** k **standardní** v rámci jedné operace škálování a pak **standardní** k **Premium** následné škálování operace.
* Nejde škálovat z větší velikost dolů na **C0 (250 MB)** velikost.
 
Zatímco mezipaměti je škálování na novou cenovou úroveň **škálování** stav se zobrazuje v **mezipaměti Azure Redis** okno.

![Škálování][redis-cache-scaling]

Po dokončení škálování stav se změní z **škálování** k **systémem**.

## <a name="how-to-automate-a-scaling-operation"></a>Jak automatizovat operaci škálování
Kromě škálování vaše instance mezipaměti na webu Azure Portal, můžete škálovat pomocí rutin prostředí PowerShell, rozhraní příkazového řádku Azure a pomocí knihoven správy Azure společnosti Microsoft (MAML). 

* [Škálování pomocí Powershellu](#scale-using-powershell)
* [Škálování pomocí Azure CLI](#scale-using-azure-cli)
* [Škálování pomocí MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Škálování pomocí Powershellu
Mezipaměť Azure pro instance Redis pomocí prostředí PowerShell můžete škálovat s použitím [Set-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/set-azurermrediscache?view=azurermps-6.6.0) rutiny při `Size`, `Sku`, nebo `ShardCount` jsou upraveny vlastnosti Description. Následující příklad ukazuje, jak na škálování mezipaměti s názvem `myCache` 2,5 GB mezipaměti. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Další informace o škálování s využitím Powershellu najdete v tématu [škálování pro Redis pomocí prostředí Powershell Azure Cache](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Škálování pomocí Azure CLI
Se dá škálovat mezipaměť Azure Redis instance pomocí rozhraní příkazového řádku Azure, zavolejte `azure rediscache set` příkazu a předejte mu změny požadované konfigurace, které obsahují novou velikost, sku nebo velikosti clusteru, v závislosti na požadované operace škálování.

Další informace o škálování pomocí Azure CLI najdete v tématu [změnit nastavení existujícího mezipaměti Azure Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Škálování pomocí MAML
Se dá škálovat mezipaměť Azure Redis instance pomocí [Microsoft Azure Management knihovny (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), volání `IRedisOperations.CreateOrUpdate` metoda a předejte jí nové velikost `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Další informace najdete v tématu [Správa mezipaměti Azure Redis pomocí MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) vzorku.

## <a name="scaling-faq"></a>Škálování – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o mezipaměti Azure Redis škálování.

* [Můžete škálovat do, z nebo v rámci cache ve verzi Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Po škálování, budu muset změnit Moje klíče název nebo přístup k mezipaměti?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak funguje škálování](#how-does-scaling-work)
* [Přijdu o data z mezipaměť během změny měřítka?](#will-i-lose-data-from-my-cache-during-scaling)
* [Je Moje vlastní databáze nastavení ovlivněných během změny měřítka?](#is-my-custom-databases-setting-affected-during-scaling)
* [Mezipaměť bude k dispozici během změny měřítka?](#will-my-cache-be-available-during-scaling)
* [S geografickou replikací nakonfigurovaná, proč mi nejde škálovat mezipaměť, nebo změňte horizontálních oddílů v clusteru?](#scaling-limitations-with-geo-relication)
* [Operace, které nejsou podporovány](#operations-that-are-not-supported)
* [Jak dlouho škálování trvá?](#how-long-does-scaling-take)
* [Jak poznám, že při škálování je kompletní?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Můžete škálovat do, z nebo v rámci cache ve verzi Premium?
* Nejde škálovat z **Premium** dolů na do mezipaměti **základní** nebo **standardní** cenovou úroveň.
* Je možné škálovat z jednoho **Premium** mezipaměti cenovou úroveň na jiný.
* Nejde škálovat z **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálování od **základní** k **standardní** v rámci jedné operace škálování a pak **standardní** k **Premium** následné škálování operace.
* Pokud povolíte clustering při vytváření vašeho **Premium** mezipaměti, můžete [velikost clusteru změnit](cache-how-to-premium-clustering.md#cluster-size). Pokud mezipaměti byl vytvořen bez clusteringu s povolená, můžete nakonfigurovat clustering později.
  
  Další informace najdete v tématu [postup konfigurace clusterů pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Po škálování, budu muset změnit Moje klíče název nebo přístup k mezipaměti?
Ne, název mezipaměti a klíče jsou beze změny během operace škálování.

### <a name="how-does-scaling-work"></a>Jak funguje škálování
* Když **základní** mezipaměti je škálovaný na jinou velikost, je vypnout a nové mezipaměti je zajištěno používat novou velikost. Během této doby je mezipaměť nedostupná a dojde ke ztrátě všech dat v mezipaměti.
* Při **základní** mezipaměti škálovat tak, aby **standardní** mezipaměti, repliky mezipaměti je zřízený a kopírují data z primární mezipaměti do mezipaměti se repliky. Mezipaměti zůstávají dostupná během procesu změny velikosti.
* Při **standardní** mezipaměti je škálovat na jinou velikost nebo položky **Premium** mezipaměti, jednu z replik se vypne a znovu zajištěny novou velikost a data přenesená prostřednictvím a jiná replika provádí převzetí služeb při selhání dřív, než bude znovu zajištěny, podobně jako proces, ke kterému dochází k selhání jednoho uzly mezipaměti.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Přijdu o data z mezipaměť během změny měřítka?
* Když **základní** mezipaměti se mění podle nové velikosti, dojde ke ztrátě všech dat a mezipaměť není k dispozici během operace škálování.
* Při **základní** mezipaměti škálovat tak, aby **standardní** mezipaměti, data v mezipaměti je obvykle zachovány.
* Když **standardní** mezipaměti je škálovat na větší velikost nebo vrstvy, nebo **Premium** mezipaměti je škálovat pro větší velikost, obvykle se zachovají všechna data. Při horizontálním škálování **standardní** nebo **Premium** mezipaměti na menší velikost dat může dojít ke ztrátě v závislosti na tom, kolik dat je v mezipaměti je horizontální související s novou velikost. Pokud dojde ke ztrátě dat při škálování, použití klíče se vyřadí [allkeys lru](https://redis.io/topics/lru-cache) zásady vyřazení. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Je Moje vlastní databáze nastavení ovlivněných během změny měřítka?
Pokud jste nakonfigurovali vlastní hodnotu `databases` nastavení během vytváření mezipaměti, uvědomte si, že některé cenové úrovně mají odlišné [databáze omezení](cache-configure.md#databases). Tady jsou některé aspekty při horizontálním škálování v tomto scénáři:

* Při škálování na cenovou úroveň s nižší `databases` limit než aktuální úroveň:
  * Pokud používáte výchozí počet `databases`, což je 16 pro všechny cenové úrovně, se neztratila žádná data.
  * Pokud použijete vlastní číslo `databases` , která spadá do omezení pro vrstvu, do kterého jste se škálování, to `databases` zachována nastavení a se neztratila žádná data.
  * Pokud použijete vlastní číslo `databases` , který překračuje omezení na nové úrovni, `databases` klesnou nastavení omezení na novou úroveň a dojde ke ztrátě všech dat v odebrané databáze.
* Při škálování na cenovou úroveň se stejnou nebo větší `databases` limit než aktuální úroveň vaše `databases` zachována nastavení a se neztratila žádná data.

Mezipaměti Standard a Premium mají SLA 99,9 % dostupnost, neexistuje žádná smlouva SLA pro únikem informací.

### <a name="will-my-cache-be-available-during-scaling"></a>Mezipaměť bude k dispozici během změny měřítka?
* **Standardní** a **Premium** mezipaměti zůstávají dostupná během operace škálování. Ale připojení blips situace může nastat při škálování mezipaměti Standard a Premium a také při škálování z úrovně Basic na Standard mezipamětí. Blips těchto připojení jsou očekávány malé a klientů redis by měl být schopný okamžitě znovu vytvořit připojení.
* **Základní** jsou během změny měřítka operací na jinou velikost offline mezipaměti. Základní mezipamětí zůstanou dostupné i při škálování z **základní** k **standardní** ale může docházet k blip malé připojení. Pokud dojde k připojení blip, by měl klientů redis moci okamžitě znovu navázat připojení.


### <a name="scaling-limitations-with-geo-replication"></a>Škálování omezení s geografickou replikací

Po přidání odkazu geografickou replikaci mezi dvěma mezipamětí, budete moct zahájit operaci škálování nebo změnit počet horizontálních oddílů v clusteru. Je nutné zrušit mezipaměti vydat tyto příkazy. Další informace najdete v tématu [konfigurace geografické replikace](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operace, které nejsou podporovány
* Nejde škálovat z vyšší cenovou úroveň na nižší cenovou úroveň.
  * Nejde škálovat z **Premium** dolů na do mezipaměti **standardní** nebo **základní** mezipaměti.
  * Nejde škálovat z **standardní** dolů na do mezipaměti **základní** mezipaměti.
* Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
* Nejde škálovat z **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálování od **základní** k **standardní** v jedné operaci škálování a škálování od **standardní** k **Premium** v následné operace.
* Nejde škálovat z větší velikost dolů na **C0 (250 MB)** velikost.

Pokud selže operaci škálování, služba se pokusí vrátit operace a mezipaměti změní zpět na původní velikost.


### <a name="how-long-does-scaling-take"></a>Jak dlouho škálování trvá?
Škálování trvá přibližně 20 minut, v závislosti na tom, kolik dat je v mezipaměti.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak poznám, že při škálování je kompletní?
Na webu Azure Portal uvidíte škálování probíhá operace. Po dokončení změny velikosti mezipaměti stav změní na **systémem**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



