---
title: Konfigurace virtuální sítě pro mezipaměť Azure Redis Cache Premium | Microsoft Docs
description: Naučte se vytvářet a spravovat podpory služby Virtual Network pro vaše instance služby Azure Redis Cache úrovně Premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 250c66c3a39519a6eddc1ecb51259ec1944c88a9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Postup konfigurace podpory služby Virtual Network pro mezipaměť Azure Redis Cache Premium
Azure Redis Cache má jiný mezipaměti nabídky, které poskytují flexibilitu při výběru velikost mezipaměti a funkce, včetně funkce úrovně Premium, jako je clustering, trvalosti a podpory služby virtual network. Virtuální síť je privátní síť v cloudu. Pokud instanci služby Azure Redis Cache je konfigurován s virtuální síť, není veřejně adresovatelné a můžete přistupovat pouze z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje postup konfigurace podpory služby virtual network pro instanci služby Azure Redis Cache premium.

> [!NOTE]
> Azure Redis Cache podporuje obě classic a Resource Manager virtuální sítě.
> 
> 

Informace o dalších funkcí mezipaměti premium najdete v tématu [Úvod do Azure Redis Cache Premium vrstvy](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Proč virtuální síť?
[Virtuální síť Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) nasazení poskytuje lepší zabezpečení a izolaci pro vaši Azure Redis Cache, stejně jako podsítě, zásady řízení přístupu a další funkce k dalšímu omezení přístupu.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Podpora služby Virtual Network (VNet) je nakonfigurovaná na **nová mezipaměť Redis** okno při vytvoření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Jakmile vyberete cenová úroveň premium, můžete nakonfigurovat integraci Redis virtuální sítě tak, že vyberete virtuální síť, která je ve stejném předplatném a umístění jako mezipaměť. Pokud chcete použít novou virtuální síť, je nejprve vytvořte podle kroků v [vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo [vytvoření virtuální sítě (klasické) pomocí portálu Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a pak se vraťte **Nová mezipaměť Redis** okno vytvořit a nakonfigurovat mezipaměť premium.

Chcete-li nakonfigurovat virtuální síť pro nové mezipaměti, klikněte na tlačítko **virtuální sítě** na **nová mezipaměť Redis** a vyberte požadované virtuální sítě z rozevíracího seznamu.

![Virtuální síť][redis-cache-vnet]

Vyberte požadovanou podsíť z **podsíť** rozevíracího seznamu a zadejte požadované **statická IP adresa**. Pokud používáte klasické virtuální sítě **statická IP adresa** pole je volitelné, a pokud není zadaný žádný, jedna je vybrána z vybrané podsítě.

> [!IMPORTANT]
> Při nasazení virtuální sítě Resource Manageru v Azure Redis Cache, mezipaměti musí být ve vyhrazené podsíť, která obsahuje žádné další prostředky s výjimkou instance služby Azure Redis Cache. Pokud je proveden pokus o nasazení Azure Redis Cache do virtuální sítě Resource Manageru k podsíti obsahující další prostředky, nasazení se nezdaří.
> 
> 

![Virtuální síť][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure si vyhrazuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítě jsou vyhrazené pro protokol shoda, společně s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení na pomocí IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Kromě IP adresy používané při infrastruktury virtuální sítě Azure každý Redis instance v podsíti používá dvě IP adresy na horizontální oddíl a jednu IP adresu další nástroje pro vyrovnávání zatížení. Mezipaměť neclusterované považuje tak, aby měl jeden horizontálního oddílu.
> 
> 

Po vytvoření mezipaměti konfiguraci sítě vnet můžete zobrazit kliknutím na **virtuální sítě** z **prostředků nabídky**.

![Virtuální síť][redis-cache-vnet-info]

Připojit k vaší instanci služby Azure Redis cache pomocí virtuální sítě, zadejte název hostitele vašeho mezipaměti v připojovacím řetězci, jak je znázorněno v následujícím příkladu:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>VNet Azure Redis Cache – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o škálování Azure Redis Cache.

* [Jaké jsou některé běžné chybné konfigurace problémy s Azure Redis Cache a virtuální sítě?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Jak můžete ověřit, že moje mezipaměti funguje ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Při pokusu o připojení k mé Redis cache ve virtuální síti, se zobrazuje chyba oznamující, že vzdálený certifikát je neplatný?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Můžete použít virtuální sítě s standardní nebo základní mezipaměti?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Proč vytvoření mezipaměti Redis selhání v některé podsítě a jiné ne?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Jaké jsou požadavky na místo adres podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Jaké jsou některé běžné chybné konfigurace problémy s Azure Redis Cache a virtuální sítě?
Pokud Azure Redis Cache je hostovaná ve virtuální síti, se použijí porty v následujících tabulkách. 

>[!IMPORTANT]
>Pokud porty v následujících tabulkách jsou zablokované, mezipaměti nemusí správně fungovat. Má minimálně jeden z těchto portů, zablokuje se Nejběžnější problém chybné konfigurace, při použití Azure Redis Cache ve virtuální síti.
> 
> 

- [Požadavky na odchozí port](#outbound-port-requirements)
- [Požadavky na portu pro příchozí spojení](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Požadavky na odchozí port

Existuj sedm požadavků odchozí port.

- Pokud požadovaný, všechny odchozí připojení k Internetu může být provedeny prostřednictvím klienta místní auditování zařízení.
- Tři porty směrovat provoz koncové body Azure údržby Azure Storage a Azure DNS.
- Zbývající rozsahy portů a pro interní komunikaci podsítě Redis. Pravidla NSG žádné podsítě jsou požadovány pro interní komunikaci podsítě Redis.

| Port(y) pro | Směr | Přenosový protokol | Účel | Místní IP | Vzdálené IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Odchozí |TCP |Redis závislosti na Azure Storage nebo infrastruktury veřejných KLÍČŮ (Internet) | (Redis podsíť) |* |
| 53 |Odchozí |TCP/UDP |Redis závislosti ve službě DNS (Internet nebo VNet) | (Redis podsíť) |* |
| 8443 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) | (Redis podsíť) |
| 10221-10231 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) | (Redis podsíť) |
| 20226 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |
| 13000-13999 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |
| 15000-15999 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |
| 6379-6380 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |


#### <a name="inbound-port-requirements"></a>Požadavky na portu pro příchozí spojení

Nejsou osm požadavky rozsah portu pro příchozí spojení. Příchozí požadavky do tohoto rozsahu jsou příchozí z jiné služby hostované ve stejné virtuální síti nebo interní komunikaci podsítě Redis.

| Port(y) pro | Směr | Přenosový protokol | Účel | Místní IP | Vzdálené IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Příchozí |TCP |Komunikaci klientů Redis, Vyrovnávání zatížení Azure | (Redis podsíť) | (Redis podsíť), virtuální sítě, Vyrovnávání zatížení Azure |
| 8443 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |
| 8500 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Redis podsíť) |Nástroj pro vyrovnávání zatížení Azure |
| 10221-10231 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť), nástroj pro vyrovnávání zatížení Azure |
| 13000-13999 |Příchozí |TCP |Komunikaci klientů Redis clustery Vyrovnávání zatížení Azure | (Redis podsíť) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 15000-15999 |Příchozí |TCP |Komunikaci klientů Redis clustery, Azure načíst vyrovnávání | (Redis podsíť) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 16001 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Redis podsíť) |Nástroj pro vyrovnávání zatížení Azure |
| 20226 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsíť) |(Redis podsíť) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Další požadavky na připojení sítě VNET

Existují požadavky na síťové připojení pro Azure Redis Cache, nemusí být splněny původně ve virtuální síti. Azure Redis Cache vyžaduje všechny následující položky fungovat správně při použití v rámci virtuální sítě.

* Odchozí síťové připojení k Azure Storage koncových bodů po celém světě. To zahrnuje koncové body umístěné ve stejné oblasti jako instanci služby Azure Redis Cache, stejně jako koncové body úložiště, které jsou umístěné v **jiných** oblastech Azure. Koncové body Azure úložiště vyřešit v rámci z následujících domén DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*a *file.core.windows.net*. 
* Odchozí síťové připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com*, a *crl.microsoft.com*. Toto připojení je potřeba k podpoře funkce SSL.
* Konfiguraci DNS pro virtuální síť musí být schopen překladu všechny koncové body a domény uvedený v dřívějších bodů. Zajištěním platný infrastruktura DNS je nakonfigurován a udržovat virtuální sítě můžete splnit tyto požadavky na DNS.
* Odchozí síťové připojení k vytvoření následujících Azure Monitoring koncových bodů, které vyřešit v rámci z následujících domén DNS: Severní prod2.prod2.metrics.nsatc.net shoebox2-black.shoebox2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net prod2.prod2.metrics.nsatc.net – východ, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak můžete ověřit, že moje mezipaměti funguje ve virtuální síti?

>[!IMPORTANT]
>Při připojování k instanci služby Azure Redis Cache, který je hostován ve virtuální síti, vaši klienti mezipaměti musí být ve stejné virtuální síti nebo ve virtuální síti s partnerský vztah virtuální síti, povolena. To zahrnuje všechny testovací aplikace nebo diagnostické nástroje příkaz ping. Skupiny zabezpečení sítě bez ohledu na to, kde je umístěn klientská aplikace, musí být nakonfigurované tak, aby síťový provoz klienta může připojit k instanci Redis.
>
>

Po nakonfigurování port požadavky popsané v předchozí části, můžete ověřit, že mezipaměti funguje tak, že provedete následující kroky.

- [Restartovat](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud všechny závislosti požadované mezipaměti, není dosažitelná (jak je uvedeno v [příchozí požadavky na porty](cache-how-to-premium-vnet.md#inbound-port-requirements) a [odchozí port požadavky](cache-how-to-premium-vnet.md#outbound-port-requirements)), do mezipaměti, nebude možné úspěšně znovu.
- Po restartování uzly mezipaměti (vykazované mezipaměti stav na portálu Azure), můžete provést následující testy:
  - příkaz ping koncový bod mezipaměti (pomocí portu 6380) z počítače, který je v rámci stejné virtuální síti jako do mezipaměti pomocí [tcping](https://www.elifulkerson.com/projects/tcping.php). Příklad:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud `tcping` nástroj hlásí, že port je otevřený, mezipaměti je k dispozici pro připojení z klientů ve virtuální síti.

  - Jiný způsob, jak otestovat je vytvoření testovacího mezipaměti klienta (které by mohly být jednoduchou konzolovou aplikaci pomocí StackExchange.Redis), se připojí k mezipaměti a přidá a načte některé položky z mezipaměti. Nainstalujte ukázkovou aplikaci klienta na virtuální počítač, který je ve stejné virtuální síti jako mezipaměť a spusťte jej k ověření připojení k mezipaměti.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Při pokusu o připojení k mé Redis cache ve virtuální síti, se zobrazuje chyba oznamující, že vzdálený certifikát je neplatný?

Při pokusu o připojení k mezipaměti Redis ve virtuální síti, zobrazí se chyba ověření certifikátu jako je tato:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být, že se připojujete k hostitele pomocí IP adresy. Doporučujeme použít název hostitele. Jinými slovy použijte tento příkaz:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Nepoužívejte IP adresu, která je podobná následující připojovací řetězec:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nelze přeložit název DNS, některé knihovny klienta zahrnují možnosti konfigurace, jako je `sslHost` poskytnutá klienta StackExchange.Redis. To umožňuje potlačit název hostitele použít k ověření certifikátu. Příklad:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Můžete použít virtuální sítě s standardní nebo základní mezipaměti?
Virtuální sítě můžete použít pouze u prémiových mezipamětí.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Proč vytvoření mezipaměti Redis selhání v některé podsítě a jiné ne?
Pokud nasazujete Azure Redis Cache do virtuální sítě Resource Manageru, mezipaměti musí být ve vyhrazené podsíť, která obsahuje jiný typ prostředku. Pokud je proveden pokus o nasazení Azure Redis Cache k podsíti virtuální sítě Resource Manageru obsahující další prostředky, nasazení se nezdaří. Před vytvořením nové mezipaměti Redis, je nutné odstranit existující prostředků v podsíti.

Více typů prostředků, můžete nasadit na klasické virtuální sítě, dokud máte dost IP adres, které jsou k dispozici.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na místo adres podsítě?
Azure si vyhrazuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítě jsou vyhrazené pro protokol shoda, společně s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení na pomocí IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adresy používané při infrastruktury virtuální sítě Azure každý Redis instance v podsíti používá dvě IP adresy na horizontální oddíl a jednu IP adresu další nástroje pro vyrovnávání zatížení. Mezipaměť neclusterované považuje tak, aby měl jeden horizontálního oddílu.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?
Pokud vaše mezipaměť je součástí virtuální sítě, jenom pro klienty ve virtuální síti přístup do mezipaměti. V tuto chvíli se v důsledku toho nepodporují následující funkce správy mezipaměti.

* Konzola redis – protože se spouští konzola Redis v prohlížeči místní, což je mimo síť VNET, se nemůže připojit ke své mezipaměti.


## <a name="use-expressroute-with-azure-redis-cache"></a>Pomocí ExpressRoute s Azure Redis Cache

Zákazníci mohou připojit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruhu jejich infrastruktury virtuální sítě, tak rozšířit jejich místní sítě do Azure. 

Ve výchozím nastavení, nově vytvořený okruh ExpressRoute neprovádí vynucené tunelování (inzerování výchozí trasy, 0.0.0.0/0) na virtuální síť. V důsledku toho odchozí připojení k Internetu je povoleno přímo z virtuální sítě a klientských aplikací se může připojit k jiné koncové body Azure, včetně Azure Redis Cache.

Ale obvyklé konfigurace zákazníka je použití vynuceného tunelování (Inzerovat výchozí trasu) který vynutí odchozí přenosy z Internetu do místo toku místní. Tento tok provozu dělí připojení s Azure Redis Cache, pokud je přenos odchozí potom blokovaný místně, tak, aby instanci služby Azure Redis Cache není schopna komunikovat s jeho závislé součásti.

Řešení je definovat jeden (nebo více) trasy definované uživatelem (udr) v podsíti, který obsahuje Azure Redis Cache. UDR definuje tras konkrétní podsítě, které bude dodržení místo výchozí trasu.

Pokud je to možné doporučujeme použít následující konfigurace:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelových propojení všechny odchozí přenosy na místě.
* UDR použije na podsíť obsahující Azure Redis Cache definuje 0.0.0.0/0 s pracovní postup pro provoz protokolu TCP/IP do veřejného Internetu; například pomocí nastavení typ dalšího přechodu "Internet".

Celkové požadavky tyto kroky je, že na úrovni podsítě UDR má přednost před ExpressRoute vynucené tunelování, čímž zajišťuje odchozí přístup k Internetu z Azure Redis Cache.

Připojení k instanci služby Azure Redis Cache z místní aplikace pomocí ExpressRoute není scénáři typické použití z důvodů výkonu (pro nejlepší výkon Azure Redis Cache klientů musí být ve stejné oblasti jako Azure Redis Cache).

>[!IMPORTANT] 
>Trasy definované v UDR **musí** být dost konkrétní, aby mají přednost před všechny trasy inzerované konfigurace ExpressRoute. Následující příklad používá rozsah adres široký 0.0.0.0/0 a jako takový se dá potenciálně omylem přepsat inzerování tras pomocí podrobnější rozsahy adres.

>[!WARNING]  
>Azure Redis Cache není podporován s konfigurací ExpressRoute, **nesprávně Inzerovat trasy z cesty veřejného partnerského vztahu k cestou soukromého partnerského vztahu mezi**. Konfigurace ExpressRoute, které mají veřejné partnerské vztahy nakonfigurované, obdrží inzerování trasy od společnosti Microsoft pro velké sady rozsahů adres Microsoft Azure IP. Pokud tyto rozsahy adres nesprávně ohlášené mezi na cestou soukromého partnerského vztahu, výsledkem je, že jsou všechny odchozí síťových paketů z podsítě instanci Azure Redis Cache nesprávně force tunelovým propojením zákazníka místní síťové infrastruktuře . Tento tok sítě dělí Azure Redis Cache. Řešení tohoto problému je zastavit směrování mezi – reklamu z cesty veřejného partnerského vztahu cestou soukromého partnerského vztahu.


Základní informace o trasy definované uživatelem je k dispozici v tomto [přehled](../virtual-network/virtual-networks-udr-overview.md).

Další informace o ExpressRoute najdete v tématu [technický přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další postup
Naučte se používat další funkce mezipaměti premium.

* [Úvod do Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

