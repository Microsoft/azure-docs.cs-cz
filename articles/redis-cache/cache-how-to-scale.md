---
title: Postup škálování Azure Redis Cache | Microsoft Docs
description: Zjistěte, jak se škálovat vaše instance služby Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: wesmc
ms.openlocfilehash: 0cf0e41fe03bf3be7ecf2172cff3e6ab5f3eb65d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-scale-azure-redis-cache"></a>Postup škálování Azure Redis Cache
Azure Redis Cache má jiný mezipaměti nabídky, které poskytují flexibilitu při výběru velikost mezipaměti a funkce. Po vytvoření mezipaměti je možné škálovat na velikost a cenovou úroveň mezipaměti, pokud se změní požadavky vaší aplikace. Tento článek ukazuje, jak se škálovat mezipaměti pomocí portálu Azure a nástroje, jako je Azure PowerShell a rozhraní příkazového řádku Azure.

## <a name="when-to-scale"></a>Kdy škálovat
Můžete použít [monitorování](cache-how-to-monitor.md) funkce Azure Redis Cache ke sledování stavu a výkonu vaší mezipaměti a zjistit, kdy škálovat mezipaměti. 

Můžete monitorovat následující metriky, které vám pomohou určit, pokud potřebujete škálování.

* Zatížení serveru Redis
* Využití paměti
* Šířka pásma sítě
* Využití procesoru

Pokud zjistíte, že mezipaměť již splňuje požadavky vaší aplikace, je možné škálovat do mezipaměti větší nebo menší cenovou úroveň, která je pro vaši aplikaci nejvhodnější. Další informace o zjištění, které mezipaměti cenová úroveň používat najdete v tématu [jaké mezipaměť Redis nabídky a velikosti použít](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Změnit velikost mezipaměti
Škálování vaší mezipaměti [přejděte do mezipaměti](cache-configure.md#configure-redis-cache-settings) v [portál Azure](https://portal.azure.com) a klikněte na tlačítko **škálování** z **prostředků nabídky**.

![Měřítko](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Vyberte požadovanou cenovou úroveň z **vyberte cenová úroveň** a klikněte na **vyberte**.

![Cenová úroveň][redis-cache-pricing-tier-blade]


Je možné škálovat na jinou cenovou úroveň, s následujícími omezeními:

* Z používat vyšší cenová úroveň je nelze škálovat na nižší cenovou úroveň.
  * Nelze škálovat od **Premium** dolů do mezipaměti **standardní** nebo **základní** mezipaměti.
  * Nelze škálovat od **standardní** dolů do mezipaměti **základní** mezipaměti.
* Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
* Nelze škálovat od **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálovat od **základní** k **standardní** v rámci jedné operace škálování a potom z **standardní** k **Premium** v následných škálování operace.
* Nelze škálovat z větší velikost dolů na **C0 (250 MB)** velikost.
 
Při mezipaměti je škálování na nové cenovou úroveň, **škálování** stav se zobrazí v **Redis Cache** okno.

![Škálování][redis-cache-scaling]

Po dokončení škálování se stav změní z **škálování** k **systémem**.

## <a name="how-to-automate-a-scaling-operation"></a>Jak automatizovat škálování operace
Kromě škálování vaší instance služby cache na portálu Azure, je možné škálovat pomocí rutin prostředí PowerShell, rozhraní příkazového řádku Azure a pomocí Microsoft Azure Management knihovny (MAML). 

* [Škálování pomocí prostředí PowerShell](#scale-using-powershell)
* [Škálování pomocí rozhraní příkazového řádku Azure](#scale-using-azure-cli)
* [Škálování pomocí MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Škálování pomocí prostředí PowerShell
Je možné škálovat vaše instance služby Azure Redis Cache pomocí prostředí PowerShell pomocí [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) rutiny při `Size`, `Sku`, nebo `ShardCount` jsou upraveny vlastnosti. Následující příklad ukazuje postup škálování mezipaměti s názvem `myCache` do mezipaměti 2,5 GB. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Další informace o škálování pomocí prostředí PowerShell najdete v tématu [škálování mezipaměti Redis pomocí prostředí Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Škálování pomocí rozhraní příkazového řádku Azure
Chcete-li škálovat vaše instance služby Azure Redis Cache pomocí rozhraní příkazového řádku Azure, volejte `azure rediscache set` příkazů a předat změny požadované konfigurace, které zahrnují novou velikost, sku nebo velikost clusteru, v závislosti na požadované operace škálování.

Další informace o škálování s Azure CLI, najdete v části [změnit nastavení existujícího Redis Cache](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Škálování pomocí MAML
Škálování Azure Redis Cache instance pomocí [Microsoft Azure Management knihovny (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), volání `IRedisOperations.CreateOrUpdate` metoda a předejte jí novou velikost `RedisProperties.SKU.Capacity`.

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

Další informace najdete v tématu [spravovat Redis Cache pomocí MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) ukázka.

## <a name="scaling-faq"></a>Škálování – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o škálování Azure Redis Cache.

* [Možné škálovat na, z a do mezipaměti Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Po škálování, je nutné změnit mé klíče název nebo přístupu k mezipaměti?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak funguje škálování](#how-does-scaling-work)
* [Bude možné ztrátě dat z mé mezipaměti při škálování?](#will-i-lose-data-from-my-cache-during-scaling)
* [Je databází. vlastní nastavení ovlivněných během změny měřítka?](#is-my-custom-databases-setting-affected-during-scaling)
* [Moje mezipaměti bude k dispozici během změny měřítka?](#will-my-cache-be-available-during-scaling)
* [S geografickou replikací, nakonfigurovali, Proč nemohu škálování Moje mezipaměti nebo změňte horizontálních oddílů v clusteru?](#scaling-limitations-with-geo-relication)
* [Operace, které nejsou podporovány](#operations-that-are-not-supported)
* [Jak dlouho škálování trvá?](#how-long-does-scaling-take)
* [Jak poznám, škálování po dokončení?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Možné škálovat na, z a do mezipaměti Premium?
* Nelze škálovat od **Premium** dolů do mezipaměti **základní** nebo **standardní** cenová úroveň.
* Je možné škálovat od jednoho **Premium** mezipaměti cenová úroveň na jiný.
* Nelze škálovat od **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálovat od **základní** k **standardní** v rámci jedné operace škálování a potom z **standardní** k **Premium** v následných škálování operace.
* Pokud jste povolili clustering při vytváření vašeho **Premium** mezipaměti, můžete [změnit velikost clusteru](cache-how-to-premium-clustering.md#cluster-size). Pokud vaše mezipaměť byl vytvořen bez clusteringu povoleno, nelze nakonfigurovat clustering později.
  
  Další informace najdete v článku [Postup konfigurace clusterů pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Po škálování, je nutné změnit mé klíče název nebo přístupu k mezipaměti?
Ne, název mezipaměti a klíče jsou beze změny během operace škálování.

### <a name="how-does-scaling-work"></a>Jak funguje škálování
* Když **základní** mezipaměti se mění podle různých velikosti, se vypne a zřizovat nové mezipaměti pomocí nové velikosti. Během této doby je k dispozici mezipaměti a dojde ke ztrátě všech dat v mezipaměti.
* Když **základní** mezipaměti rozšířit tak, aby **standardní** mezipaměti repliky mezipaměti je zřízený a data budou zkopírována z primární mezipaměti do mezipaměti repliky. Mezipaměti v zůstal dostupný během procesu škálování.
* Když **standardní** mezipaměti je škálovat různé velikost nebo na **Premium** mezipaměti, jednu z replik se vypne a znovu poskytnuto novou velikost a přenosu dat a další repliky provede převzetí služeb při selhání, než znovu poskytnuto, je podobný procesu, ke kterému dochází při selhání jednoho z uzlů mezipaměti.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Bude možné ztrátě dat z mé mezipaměti při škálování?
* Když **základní** mezipaměti se mění podle nové velikosti, dojde ke ztrátě všech dat a mezipaměti není k dispozici v průběhu operace škálování.
* Když **základní** mezipaměti rozšířit tak, aby **standardní** mezipaměti, data v mezipaměti je obvykle zachovaná.
* Když **standardní** mezipaměti je škálovat na větší velikost nebo vrstvy, nebo **Premium** mezipaměti je škálovat na větší velikost, je obvykle zachovají všechna data. Při zvětšení velikosti **standardní** nebo **Premium** mezipaměti na menší velikost, data mohou být ztraceny v závislosti na tom, kolik dat je v mezipaměti související s novou velikost, když bude změněno měřítko. Pokud dojde ke ztrátě dat při škálování směrem dolů, jsou vyřazování klíče pomocí [allkeys lru](http://redis.io/topics/lru-cache) zásady vyřazení. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Je databází. vlastní nastavení ovlivněných během změny měřítka?
Pokud jste nakonfigurovali vlastní hodnotu `databases` nastavení během vytváření mezipaměti, mějte na paměti, že některé cenové úrovně mají různé [databáze omezení](cache-configure.md#databases). Zde jsou některé aspekty při škálování v tomto scénáři:

* Při zvětšení velikosti na cenovou úroveň s nižší `databases` limit než současná vrstva:
  * Pokud používáte výchozí počet `databases`, což je 16 pro všechny cenové úrovně, dojde ke ztrátě žádná data.
  * Pokud používáte vlastní počet `databases` který spadá do limity pro vrstvu, do které jste se škálování, to `databases` se zachovává nastavení a dojde ke ztrátě žádná data.
  * Pokud používáte vlastní počet `databases` který překračuje omezení novou vrstvu `databases` nastavení je snížena na omezení novou vrstvu a dojde ke ztrátě všech dat v odebrané databáze.
* Při škálování cenové úrovně se stejným nebo vyšším `databases` limit než současná vrstva vaší `databases` se zachovává nastavení a dojde ke ztrátě žádná data.

I když Standard a Premium mezipamětí má SLA 99,9 % dostupnosti, není žádná SLA ztráty dat.

### <a name="will-my-cache-be-available-during-scaling"></a>Moje mezipaměti bude k dispozici během změny měřítka?
* **Standardní** a **Premium** mezipamětí zůstanou dostupné během operace škálování. Ale blips připojení situace může nastat při škálování Standard a Premium mezipaměti a také při škálování ze základní standardní mezipamětí. Očekávány malé blips těchto připojení a klientů redis musí být schopen okamžitě znovu vytvořit připojení.
* **Základní** mezipamětí jsou offline během změny měřítka na jinou velikost operací. Základní mezipamětí zůstanou dostupné, když škálování z **základní** k **standardní** ale může dojít k blip malé připojení. Pokud dojde k připojení blip, klienti redis byste měli mít okamžitě znovu navázat připojení.


### <a name="scaling-limitations-with-geo-relication"></a>Omezení s relication geografické škálování

Po přidání geografická replikace propojení mezi dvěma mezipamětí, budete moci zahájit škálování operaci nebo změňte počet horizontálních oddílů v clusteru. Je nutné zrušit mezipaměti vystavit tyto příkazy. Další informace najdete v tématu [geografická replikace konfigurovat](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operace, které nejsou podporovány
* Z používat vyšší cenová úroveň je nelze škálovat na nižší cenovou úroveň.
  * Nelze škálovat od **Premium** dolů do mezipaměti **standardní** nebo **základní** mezipaměti.
  * Nelze škálovat od **standardní** dolů do mezipaměti **základní** mezipaměti.
* Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
* Nelze škálovat od **základní** přímo do mezipaměti **Premium** mezipaměti. Nejprve škálovat od **základní** k **standardní** jednu operaci škálování a potom na škále od **standardní** k **Premium** v následné operace.
* Nelze škálovat z větší velikost dolů na **C0 (250 MB)** velikost.

Pokud škálování operace selže, služba se pokusí vrátit operaci a mezipaměti se vrátí k původní velikost.


### <a name="how-long-does-scaling-take"></a>Jak dlouho škálování trvá?
Škálování trvá přibližně 20 minut v závislosti na tom, kolik dat je v mezipaměti.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak poznám, škálování po dokončení?
Na portálu Azure uvidíte probíhající operaci škálování. Po dokončení škálování stav mezipaměti se změní na **systémem**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



