---
title: Jak škálovat Azure Cache pro Redis
description: Zjistěte, jak škálovat instance Azure Cache for Redis pomocí portálu Azure a nástrojů, jako je Azure PowerShell a Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277996"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Jak škálovat Azure Cache pro Redis
Azure Cache for Redis má různé nabídky mezipaměti, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí. Po vytvoření mezipaměti můžete škálovat velikost a cenovou úroveň mezipaměti, pokud se změní požadavky na vaši aplikaci. Tento článek ukazuje, jak škálovat mezipaměť pomocí portálu Azure a nástrojů, jako je Azure PowerShell a Azure CLI.

## <a name="when-to-scale"></a>Kdy škálovat
Pomocí funkcí [monitorování](cache-how-to-monitor.md) služby Azure Cache for Redis můžete sledovat stav a výkon mezipaměti a zjistit, kdy má být mezipaměť škálovat. 

Můžete sledovat následující metriky, které vám pomohou určit, zda je třeba škálovat.

* Zatížení serveru Redis
* Využití paměti
* Šířka pásma sítě
* Využití procesoru

Pokud zjistíte, že vaše mezipaměť již nesplňuje požadavky vaší aplikace, můžete škálovat na větší nebo menší úroveň ocenění mezipaměti, která je pro vaši aplikaci vhodná. Další informace o určení, kterou úroveň oceňování mezipaměti použít, najdete v článku [Co Azure Cache pro Redis nabídky a velikost i použít](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Škálování mezipaměti
Pokud chcete změnit velikost mezipaměti, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) na [webu Azure Portal](https://portal.azure.com) a z nabídky **Prostředky**klikněte na **Měřítko** .

![Škálování](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Vyberte požadovanou cenovou úroveň z okna **Vybrat cenovou úroveň** a klepněte na **tlačítko Vybrat**.

![Cenová úroveň][redis-cache-pricing-tier-blade]


Můžete škálovat na jinou cenovou úroveň s následujícími omezeními:

* Nelze škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
  * Z mezipaměti **Premium** nelze škálovat na **standardní** nebo **základní** mezipaměť.
  * Nelze škálovat ze **standardní** mezipaměti na **základní** mezipaměť.
* Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nelze změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
* Z **mezipaměti Basic** nelze škálovat přímo do mezipaměti **Premium.** Nejprve škálovat ze **základní** na **standardní** v jedné operaci škálování a potom ze **standardu** na **premium** v následné operaci škálování.
* Nelze škálovat z větší velikosti až na velikost **C0 (250 MB).**
 
Zatímco se mezipaměť škáluje na novou cenovou úroveň, v okně **Azure Cache for Redis** se zobrazí stav **škálování.**

![Škálování][redis-cache-scaling]

Po dokončení škálování se stav změní z **škálování** na **spuštěno**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatizace operace škálování
Kromě škálování instancí mezipaměti na webu Azure Portal můžete škálovat pomocí rutin PowerShellu, rozhraní příkazového příkazového příkazu Azure a pomocí knihoven Microsoft Azure Management Libraries (MAML). 

* [Škálování pomocí PowerShellu](#scale-using-powershell)
* [Škálování pomocí azure cli](#scale-using-azure-cli)
* [Škálování pomocí MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Škálování pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete škálovat vaše Azure Cache pro Redis instance s PowerShell pomocí [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) rutina `Size`při změně , `Sku`nebo `ShardCount` vlastnosti. Následující příklad ukazuje, jak škálovat mezipaměť s názvem `myCache` do mezipaměti o velikosti 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Další informace o škálování pomocí PowerShellu najdete v [tématu Škálování mezipaměti Azure pro Redis pomocí Powershellu](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Škálování pomocí azure cli
Chcete-li škálovat instance Azure Cache for Redis `azure rediscache set` pomocí azure cli, zavolejte příkaz a předejte požadované změny konfigurace, které zahrnují novou velikost, sku nebo velikost clusteru, v závislosti na požadované operaci škálování.

Další informace o škálování pomocí azure cli najdete v [tématu změna nastavení existující mezipaměti Azure pro Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Škálování pomocí MAML
Chcete-li škálovat instance Azure Cache for Redis pomocí [knihoven Microsoft Azure Management Libraries (MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)zavolejte metodu `IRedisOperations.CreateOrUpdate` a předejte novou velikost pro . `RedisProperties.SKU.Capacity`

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

Další informace najdete v tématu [Správa mezipaměti Azure pro Redis pomocí ukázky MAML.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Nejčastější dotazy k škálování
Následující seznam obsahuje odpovědi na běžně kladené otázky týkající se azure cache pro redis škálování.

* [Mohu škálovat do, z nebo v rámci prémiové mezipaměti?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Po změně měřítka musím změnit název mezipaměti nebo přístupové klíče?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak škálování funguje?](#how-does-scaling-work)
* [Dojde ke ztrátě dat z mezipaměti během škálování?](#will-i-lose-data-from-my-cache-during-scaling)
* [Je nastavení vlastních databází ovlivněno během škálování?](#is-my-custom-databases-setting-affected-during-scaling)
* [Bude moje mezipaměť k dispozici během škálování?](#will-my-cache-be-available-during-scaling)
* Proč s konfigurací geografické replikace nelze škálovat mezipaměť nebo měnit horizontální oddíly v clusteru?
* [Operace, které nejsou podporovány](#operations-that-are-not-supported)
* [Jak dlouho trvá škálování?](#how-long-does-scaling-take)
* [Jak poznám, že je změna měřítka dokončena?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Mohu škálovat do, z nebo v rámci prémiové mezipaměti?
* Z mezipaměti **Premium** nelze škálovat na **základní** nebo **standardní** cenovou úroveň.
* Můžete škálovat z jedné úrovně ocenění mezipaměti **Premium** na jinou.
* Z **mezipaměti Basic** nelze škálovat přímo do mezipaměti **Premium.** Nejprve škálovat ze **základní** na **standardní** v jedné operaci škálování a potom ze **standardu** na **premium** v následné operaci škálování.
* Pokud jste při vytváření mezipaměti **Premium** povolili clustering, můžete [změnit velikost clusteru](cache-how-to-premium-clustering.md#cluster-size). Pokud byla vaše mezipaměť vytvořena bez povoleného clusteringu, můžete clustering nakonfigurovat později.
  
  Další informace naleznete v tématu [Jak nakonfigurovat clustering pro premium Azure Cache pro Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Po změně měřítka musím změnit název mezipaměti nebo přístupové klíče?
Ne, název mezipaměti a klíče se během operace škálování nezmění.

### <a name="how-does-scaling-work"></a>Jak škálování funguje?
* Při škálování **základní** mezipaměti na jinou velikost, je vypnuta a nová mezipaměť je zřízena pomocí nové velikosti. Během této doby je mezipaměť nedostupná a všechna data v mezipaměti budou ztracena.
* Při škálování **základní** mezipaměti na **standardní** mezipaměti je zřízena mezipaměť replika a data se zkopírují z primární mezipaměti do mezipaměti repliky. Mezipaměť zůstane k dispozici během procesu škálování.
* Při škálování **standardní** mezipaměti na jinou velikost nebo do mezipaměti **Premium,** jedna z replik je vypnuta a znovu zřízena na novou velikost a data přenesená přes a pak druhá replika provede převzetí služeb při selhání před jeho opětovné zřízení, podobně jako proces, ke kterému dochází během selhání jednoho z uzlů mezipaměti.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Dojde ke ztrátě dat z mezipaměti během škálování?
* Při škálování **základní** mezipaměti na novou velikost dojde ke ztrátě všech dat a během operace škálování není k dispozici.
* Při škálování **základní** mezipaměti na **standardní** mezipaměti jsou data v mezipaměti obvykle zachována.
* Při škálování **standardní** mezipaměti na větší velikost nebo úroveň nebo **premium** mezipaměti je škálovat na větší velikost, všechna data se obvykle zachovají. Při škálování **standardní** nebo **premium** mezipaměti až na menší velikost, data mohou být ztraceny v závislosti na tom, kolik dat je v mezipaměti vztahující se k nové velikosti při škálování. Pokud dojde ke ztrátě dat při škálování dolů, klíče jsou vyřazeny pomocí zásady vyřazování [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Je nastavení vlastních databází ovlivněno během škálování?
Pokud jste nakonfigurovali vlastní hodnotu pro `databases` nastavení během vytváření mezipaměti, mějte na paměti, že některé cenové úrovně mají různá omezení [databází](cache-configure.md#databases). Zde jsou některé důležité informace při škálování v tomto scénáři:

* Při škálování na cenovou `databases` úroveň s nižším limitem, než je aktuální úroveň:
  * Pokud používáte výchozí číslo `databases`, což je 16 pro všechny cenové úrovně, dojde ke ztrátě žádných dat.
  * Pokud používáte vlastní `databases` počet, který spadá do limitů pro úroveň, `databases` na kterou škálování, toto nastavení je zachována a žádná data se ztratí.
  * Pokud používáte vlastní `databases` počet, který překračuje limity nové vrstvy, `databases` nastavení se sníží na limity nové vrstvy a všechna data v odebraných databázích dojde ke ztrátě.
* Při škálování na cenovou úroveň `databases` se stejným nebo vyšším `databases` limitem, než je aktuální úroveň, je vaše nastavení zachováno a nedojde ke ztrátě žádných dat.

Zatímco standardní a prémiové mezipaměti mají 99,9% sla pro dostupnost, neexistuje žádná sla pro ztrátu dat.

### <a name="will-my-cache-be-available-during-scaling"></a>Bude moje mezipaměť k dispozici během škálování?
* **Standardní** a **Premium** mezipaměti zůstávají k dispozici během operace škálování. K chybám připojení však může dojít při škálování mezipamětí Standard a Premium a také při škálování ze základních na standardní mezipaměti. Očekává se, že tyto chyby připojení budou malé a klienti redis by měli být schopni okamžitě obnovit své připojení.
* **Základní** mezipaměti jsou během operací škálování na jinou velikost offline. Základní mezipaměti zůstávají k dispozici při škálování ze **základní** na **standardní,** ale může dojít k malé připojení výkyv. Pokud dojde k výkyvu připojení, redis klienti by měli být schopni obnovit jejich připojení okamžitě.


### <a name="scaling-limitations-with-geo-replication"></a>Omezení škálování pomocí geografické replikace

Po přidání propojení geografické replikace mezi dvě mezipaměti již nebudete moci zahájit operaci škálování nebo změnit počet horizontálních oddílů v clusteru. Chcete-li tyto příkazy vydat, je nutné zrušit propojení mezipaměti. Další informace naleznete v [tématu Konfigurace geografické replikace](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operace, které nejsou podporovány
* Nelze škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
  * Z mezipaměti **Premium** nelze škálovat na **standardní** nebo **základní** mezipaměť.
  * Nelze škálovat ze **standardní** mezipaměti na **základní** mezipaměť.
* Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nelze změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
* Z **mezipaměti Basic** nelze škálovat přímo do mezipaměti **Premium.** Nejprve škálovat ze **základní** ho **standardního** v jedné operaci škálování a pak škálovat ze **standardu** na **premium** v následující operaci.
* Nelze škálovat z větší velikosti až na velikost **C0 (250 MB).**

Pokud se operace škálování nezdaří, služba se pokusí vrátit operaci a mezipaměť se vrátí do původní velikosti.


### <a name="how-long-does-scaling-take"></a>Jak dlouho trvá škálování?
Škálování čas závisí na tom, kolik dat je v mezipaměti, s větší množství dat trvá delší dobu k dokončení. Škálování trvá přibližně 20 minut. Pro clusterované mezipaměti škálování trvá přibližně 20 minut na horizontálního oddílu.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak poznám, že je změna měřítka dokončena?
Na webu Azure Portal uvidíte probíhající operaci škálování. Po dokončení škálování se stav mezipaměti změní na **Spuštěno**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
