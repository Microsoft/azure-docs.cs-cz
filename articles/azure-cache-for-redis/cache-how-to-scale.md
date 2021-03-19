---
title: Škálování mezipaměti Azure pro instanci Redis
description: Naučte se škálovat mezipaměť Azure pro instance Redis pomocí Azure Portal a nástrojů jako Azure PowerShell a Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d91b62afacad31d78feb7d4743cd4050fcc1bd4e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581639"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Škálování mezipaměti Azure pro instanci Redis
Mezipaměť Azure pro Redis má různé nabídky mezipaměti, které poskytují flexibilitu v výběru velikosti a funkcí mezipaměti. V případě mezipaměti Basic, Standard nebo Premium můžete změnit její velikost a úroveň, aby se zajistilo, že budou splňovat požadavky vaší aplikace. V tomto článku se dozvíte, jak škálovat mezipaměť pomocí Azure Portal a nástrojů jako Azure PowerShell a Azure CLI.

## <a name="when-to-scale"></a>Kdy škálovat
Funkce [monitorování](cache-how-to-monitor.md) mezipaměti Azure cache pro Redis můžete použít k monitorování stavu a výkonu mezipaměti a k určení, kdy se má mezipaměť škálovat. 

Můžete monitorovat následující metriky, abyste zjistili, jestli potřebujete škálovat.

* Zatížení serveru Redis
* Využití paměti
* Šířka pásma sítě
* Využití procesoru

Pokud zjistíte, že vaše mezipaměť již nesplňuje požadavky vaší aplikace, můžete škálovat na větší nebo menší cenovou úroveň mezipaměti, která je pro vaši aplikaci nejvhodnější. Další informace o určení cenové úrovně, která se má použít, najdete v tématu [Volba správné úrovně](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Škálování mezipaměti
Pokud chcete škálovat mezipaměť, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) v [Azure Portal](https://portal.azure.com) a v **nabídce prostředků** klikněte na **škálovat** .

![Měřítko](./media/cache-how-to-scale/redis-cache-scale-menu.png)

V okně **vyberte cenovou úroveň** vyberte požadovanou cenovou úroveň a klikněte na **Vybrat**.

![Cenová úroveň][redis-cache-pricing-tier-blade]


Můžete škálovat na jinou cenovou úroveň s následujícími omezeními:

* Nemůžete škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
  * Mezipaměť **Premium** nejde škálovat do **úrovně Standard** nebo **Basic** .
  * **Standardní** mezipaměť nejde škálovat do **základní** mezipaměti.
* Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nemůžete změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
* Nemůžete škálovat ze **základní** mezipaměti přímo do mezipaměti **Premium** . Nejprve Škálujte z úrovně **Basic** na **Standard** v rámci jedné operace škálování a pak od **Standard** na **Premium** v následné operaci škálování.
* Velikost se nedá škálovat z větší velikosti na velikost **C0 (250 MB/s)** . V rámci stejné cenové úrovně ale můžete horizontální navýšení kapacity snížit na jinou velikost. Můžete například horizontální navýšení kapacity od C5 Standard až C1.
 
I když je mezipaměť škálovatelná na novou cenovou úroveň, v okně **Azure cache pro Redis** se zobrazí stav **škálování** .

![Škálování][redis-cache-scaling]

Po dokončení škálování se stav změní z **škálování** na **spuštěno**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatizace operace škálování
Kromě škálování instancí mezipaměti v Azure Portal můžete škálovat pomocí rutin PowerShellu, rozhraní příkazového řádku Azure a pomocí knihoven pro správu Microsoft Azure (MAML). 

* [Škálování pomocí PowerShellu](#scale-using-powershell)
* [Škálování pomocí Azure CLI](#scale-using-azure-cli)
* [Škálování pomocí MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Škálování pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mezipaměť Azure můžete škálovat pro instance Redis pomocí prostředí PowerShell pomocí rutiny [set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) při `Size` `Sku` změně vlastností,, nebo `ShardCount` . Následující příklad ukazuje, jak škálovat mezipaměť s názvem `myCache` na 2,5 GB mezipaměti. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Další informace o škálování pomocí PowerShellu najdete v tématu [škálování mezipaměti Azure pro Redis pomocí PowerShellu](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Škálování pomocí Azure CLI
Pokud chcete škálovat mezipaměť Azure pro instance Redis pomocí rozhraní příkazového řádku Azure, zavolejte `azure rediscache set` příkaz a předejte požadované změny konfigurace, které budou zahrnovat novou velikost, skladovou položku nebo velikost clusteru, v závislosti na požadované operaci škálování.

Další informace o škálování pomocí Azure CLI najdete v tématu [Změna nastavení stávající mezipaměti Azure pro Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Škálování pomocí MAML
Pokud chcete škálovat mezipaměť Azure pro instance Redis pomocí [knihoven pro správu Microsoft Azure (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), zavolejte `IRedisOperations.CreateOrUpdate` metodu a předejte novou velikost pro `RedisProperties.SKU.Capacity` .

```csharp
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
```

Další informace najdete v tématu [Správa mezipaměti Azure pro Redis pomocí ukázky MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Nejčastější dotazy k škálování
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se škálování Azure cache pro Redis.

* [Můžu škálovat na, z nebo do mezipaměti Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Po škálování je třeba změnit název mezipaměti nebo přístupové klíče?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak funguje škálování?](#how-does-scaling-work)
* [Ztratím během škálování data z mezipaměti?](#will-i-lose-data-from-my-cache-during-scaling)
* [Ovlivňuje toto nastavení vlastní databáze při škálování?](#is-my-custom-databases-setting-affected-during-scaling)
* [Bude při škálování k dispozici moje mezipaměť?](#will-my-cache-be-available-during-scaling)
* Když máte nakonfigurovanou geografickou replikaci a nemůžete škálovat mezipaměť nebo změnit horizontálních oddílů v clusteru?
* [Operace, které nejsou podporovány](#operations-that-are-not-supported)
* [Jak dlouho trvá škálování?](#how-long-does-scaling-take)
* [Jak poznám, kdy je možné škálování dokončit?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Můžu škálovat na, z nebo do mezipaměti Premium?
* Z mezipaměti **Premium** nejde škálovat do cenové úrovně **Basic** nebo **Standard** .
* Můžete škálovat z jedné cenové úrovně mezipaměti **Premium** na jinou.
* Nemůžete škálovat ze **základní** mezipaměti přímo do mezipaměti **Premium** . Nejprve Škálujte z úrovně **Basic** na **Standard** v rámci jedné operace škálování a pak od **Standard** na **Premium** v následné operaci škálování.
* Pokud jste povolili clusteringu při vytváření mezipaměti **Premium** , můžete [změnit velikost clusteru](cache-how-to-premium-clustering.md#cluster-size). Pokud byla vaše mezipaměť vytvořena bez povoleného clusteringu, můžete nakonfigurovat clustering později.
  
  Další informace najdete v tématu [Konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Po škálování je třeba změnit název mezipaměti nebo přístupové klíče?
Ne, název a klíče mezipaměti se během operace škálování nezměnily.

### <a name="how-does-scaling-work"></a>Jak funguje škálování?
* Pokud je **základní** mezipaměť škálovaná na jinou velikost, je vypnutá a nová velikost mezipaměti je zřízena pomocí nové velikosti. Během této doby není mezipaměť k dispozici a všechna data v mezipaměti jsou ztracena.
* Pokud je mezipaměť úrovně **Basic** škálovaná na **standardní** mezipaměť, zřídí se mezipaměť repliky a data se zkopírují z primární mezipaměti do mezipaměti repliky. Mezipaměť zůstává během procesu škálování dostupná.
* Pokud je **standardní** mezipaměť škálovaná na jinou velikost nebo mezipaměť **Premium** , jedna z replik se vypne a znovu zřídí s novou velikostí a data přenesená a druhá replika provede převzetí služeb při selhání, než se znovu zřídí, podobně jako u procesu, ke kterému dojde během selhání jednoho z uzlů mezipaměti.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Ztratím během škálování data z mezipaměti?
* Pokud je mezipaměť úrovně **Basic** škálovaná na novou velikost, ztratí se všechna data a mezipaměť není během operace škálování k dispozici.
* Pokud je mezipaměť úrovně **Basic** škálovaná na **standardní** mezipaměť, jsou data v mezipaměti obvykle zachovaná.
* Když se **standardní** mezipaměť škáluje na větší velikost nebo úroveň, nebo se mezipaměť **Premium** škáluje na větší velikost, obvykle se zachovají všechna data. Když velikost mezipaměti **úrovně Standard** nebo **Premium** zmenšíte do menší velikosti, může dojít ke ztrátě dat v závislosti na tom, kolik dat je v mezipaměti, které souvisí s novou velikostí při škálování. Pokud dojde ke ztrátě dat při horizontálním navýšení kapacity, klíče se vyloučí pomocí zásad vyřazení [AllKeys-LRU](https://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Ovlivňuje toto nastavení vlastní databáze při škálování?
Pokud jste při vytváření mezipaměti nakonfigurovali vlastní hodnotu pro toto `databases` nastavení, pamatujte na to, že některé cenové úrovně mají různé [limity databáze](cache-configure.md#databases). Tady jsou některé předpoklady při škálování v tomto scénáři:

* Při škálování na cenovou úroveň s nižším `databases` limitem než má aktuální úroveň:
  * Pokud používáte výchozí počet `databases` , který je pro všechny cenové úrovně 16, neztratí se žádná data.
  * Pokud používáte vlastní počet `databases` , který spadá do omezení pro vrstvu, na kterou chcete škálovat, toto `databases` nastavení se zachová a ztratí se žádná data.
  * Pokud používáte vlastní počet `databases` , který překračuje limity nové vrstvy, `databases` nastavení se sníží na limity nové úrovně a ztratí se všechna data v odebraných databázích.
* Při škálování na cenovou úroveň se stejným nebo vyšším `databases` limitem, než je aktuální vrstva, `databases` se nastavení zachová a ztratí se žádná data.

I když mají mezipaměť úrovně Standard a Premium dostupnost 99,9%, není k dispozici žádná smlouva SLA pro ztrátu dat.

### <a name="will-my-cache-be-available-during-scaling"></a>Bude při škálování k dispozici moje mezipaměť?
* Mezipaměť **Standard** a **Premium** zůstávají během operace škálování k dispozici. Výkyvů připojení ale může nastat při škálování na úrovni Standard a Premium, ale také při škálování z úrovně Basic na standardní mezipaměti. Očekává se, že tyto výkyvů připojení budou malé a klienti Redis by měli být schopní okamžitě znovu vytvořit připojení.
* **Základní** mezipaměti jsou během operací škálování v režimu offline na jinou velikost. Základní mezipaměti zůstávají dostupné při škálování z úrovně **Basic** na **Standard** , ale můžou se setkat s malým blipm připojení. Pokud dojde k blipí připojení, klienti Redis by měli být schopní okamžitě znovu vytvořit připojení.


### <a name="scaling-limitations-with-geo-replication"></a>Omezení škálování pomocí geografické replikace

Po přidání odkazu na geografickou replikaci mezi dvěma mezipamětmi už nebudete moct iniciovat operaci škálování ani měnit počet horizontálních oddílů v clusteru. Aby bylo možné tyto příkazy vydávat, je nutné zrušit propojení mezipaměti. Další informace najdete v tématu [Konfigurace geografické replikace](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operace, které nejsou podporovány
* Nemůžete škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
  * Mezipaměť **Premium** nejde škálovat do **úrovně Standard** nebo **Basic** .
  * **Standardní** mezipaměť nejde škálovat do **základní** mezipaměti.
* Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nemůžete změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
* Nemůžete škálovat ze **základní** mezipaměti přímo do mezipaměti **Premium** . První škálování z úrovně **Basic** na **Standard** v rámci jedné operace škálování a pak při následné operaci Škálujte ze **Standard** na **Premium** .
* Velikost se nedá škálovat z větší velikosti na velikost **C0 (250 MB/s)** .

Pokud operace škálování selže, služba se pokusí operaci vrátit zpět a mezipaměť se vrátí k původní velikosti.


### <a name="how-long-does-scaling-take"></a>Jak dlouho trvá škálování?
Doba škálování závisí na tom, kolik dat je v mezipaměti, přičemž dokončení větších objemů dat trvá delší dobu. Škálování trvá přibližně 20 minut. U clusterovaných mezipamětí zabere škálování přibližně 20 minut na horizontálních oddílů.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak poznám, kdy je možné škálování dokončit?
V Azure Portal vidíte, že probíhá operace škálování. Po dokončení škálování se stav mezipaměti změní na **spuštěno**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
