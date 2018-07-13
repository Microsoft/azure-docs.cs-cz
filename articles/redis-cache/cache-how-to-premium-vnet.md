---
title: Konfigurace virtuální sítě pro Azure Redis Cache ve verzi Premium | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat podpory služby Virtual Network pro vaše instance Azure Redis Cache na úrovni Premium
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671118"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Jak nakonfigurovat podpora služby Virtual Network pro mezipaměť Redis Azure Premium
Azure Redis Cache má jiný nabídek, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně novými funkcemi úrovně Premium jako je clustering, trvalé a podpory služby virtual network. Virtuální síť je privátní síť v cloudu. Pokud instance Azure Redis Cache je nakonfigurovaná pomocí virtuální sítě, není veřejně adresovatelný a je přístupný pouze z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje postup konfigurace podpory služby virtual network pro instance Azure Redis Cache premium.

> [!NOTE]
> Azure Redis Cache podporuje obě classic a virtuálních sítí správce prostředků.
> 
> 

Informace o dalších prémiových funkcí mezipaměti, naleznete v tématu [Úvod do služby Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Proč virtuální síť?
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) nasazení nabízí vyšší míru zabezpečení a izolace pro vaši Azure Redis Cache, stejně jako podsítě, zásady řízení přístupu a jiné funkce pro další omezení přístupu.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Podpora služby Virtual Network (VNet) je nakonfigurován na **novou mezipaměť Redis** okno během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru cenové úrovně premium můžete nakonfigurovat integrace Redis virtuální sítě tak, že vyberete virtuální síť, která je ve stejném předplatném a umístění jako mezipaměť. Použít novou virtuální síť, vytvořte ho nejprve podle postupu v [vytvořit virtuální síť pomocí webu Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo [vytvoření virtuální sítě (classic) pomocí webu Azure portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a pak se vraťte **Novou mezipaměť Redis** okno vytvořit a nakonfigurovat mezipaměť premium.

Ke konfiguraci virtuální sítě pro nové mezipaměti, klikněte na tlačítko **virtuální sítě** na **novou mezipaměť Redis** okna a vyberte požadovanou virtuální síť v rozevíracím seznamu.

![Virtuální síť][redis-cache-vnet]

Vyberte požadované podsítě z **podsítě** rozevíracího seznamu a zadejte požadovaný **statická IP adresa**. Pokud používáte klasickou virtuální síť **statickou IP adresu** pole je volitelné, a pokud není zadaný žádný, jeden je vybrán z vybrané podsítě.

> [!IMPORTANT]
> Při nasazování Azure Redis Cache k virtuální síti správce prostředků, do mezipaměti musí být ve vyhrazené podsíti, která neobsahuje žádné prostředky s výjimkou instancí služby Azure Redis Cache. Pokud je proveden pokus o nasazení Azure Redis Cache k virtuální síti správce prostředků k podsíti, která obsahuje další prostředky, nasazení se nezdaří.
> 
> 

![Virtuální síť][redis-cache-vnet-ip]

> [!IMPORTANT]
> Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Kromě IP adresy používané službou infrastruktury virtuální sítě Azure každý Redis instance v podsíti využívá dvě IP adresy za horizontální oddíl a jeden další IP adresy nástroje pro vyrovnávání zatížení. Mezipaměť neclusterovaný je považován za mít jeden horizontální oddíl.
> 
> 

Po vytvoření mezipaměti konfiguraci pro virtuální síť můžete zobrazit kliknutím **virtuální sítě** z **nabídce prostředků**.

![Virtuální síť][redis-cache-vnet-info]

Pro připojení k vaší instanci služby Azure Redis cache, při použití virtuální sítě, zadejte název hostitele vaší mezipaměti v připojovacím řetězci, jak je znázorněno v následujícím příkladu:

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

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache virtuální sítě – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o škálování Azure Redis Cache.

* [Jaké jsou některé běžné chybné konfigurace problémy s Azure Redis Cache a virtuálními sítěmi?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Jak ověřit, že mezipaměť funguje ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Při pokusu o připojení pro mezipaměť Redis ve virtuální síti, proč dochází k chybě oznamující, že vzdálený certifikát není platný?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Můžete použít virtuální sítě s mezipamětí standard nebo basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Proč vytvoření mezipaměti Redis selže v některých podsítě, ale ne pro jiné?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Jaké jsou požadavky na místo adres podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Jaké jsou některé běžné chybné konfigurace problémy s Azure Redis Cache a virtuálními sítěmi?
Když Azure Redis Cache je hostovaná ve virtuální síti, se používají porty v následujících tabulkách. 

>[!IMPORTANT]
>Pokud porty v následujících tabulkách jsou blokovány, mezipaměti nemusí fungovat správně. Jeden nebo více z těchto portů blokované je nejběžnějším problémem chybnou konfiguraci při použití Azure Redis Cache ve virtuální síti.
> 
> 

- [Požadavky na odchozích portů](#outbound-port-requirements)
- [Příchozí požadavky na porty](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Požadavky na odchozích portů

Existuj sedm požadavků odchozí port.

- Pokud požadovaný, všechny odchozí připojení k Internetu, lze provést prostřednictvím klienta v místním auditování zařízení.
- Tři porty směrování provozu do koncových bodů Azure údržby služby Azure Storage a Azure DNS.
- Zbývající rozsahy portů a pro interní komunikaci podsítě Redis. Žádná pravidla NSG podsítě jsou požadovány pro interní komunikaci podsítě Redis.

| Port(y) pro | Směr | Přenosový protokol | Účel | Místní IP | Vzdálená IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Odchozí |TCP |Redis závislosti na Azure Storage a infrastruktury veřejných KLÍČŮ (Internet) | (Redis podsítě) |* |
| 53 |Odchozí |TCP/UDP |Redis závislostí na DNS (Internet/virtuální sítě) | (Redis podsítě) |* |
| 8443 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) | (Redis podsítě) |
| 10221-10231 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) | (Redis podsítě) |
| 20226 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |
| 13000-13999 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |
| 15000-15999 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |
| 6379-6380 |Odchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |


#### <a name="inbound-port-requirements"></a>Příchozí požadavky na porty

Nejsou k dispozici osm požadavky rozsah portu pro příchozí spojení. Příchozí požadavky do tohoto rozsahu jsou příchozí z jiných služeb hostovaných ve stejné virtuální síti nebo interní podsítě komunikace Redis.

| Port(y) pro | Směr | Přenosový protokol | Účel | Místní IP | Vzdálená IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Příchozí |TCP |Komunikace klientů Redis, Vyrovnávání zatížení Azure | (Redis podsítě) | (Redis podsítě), virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 8443 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |
| 8500 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Redis podsítě) |Nástroj pro vyrovnávání zatížení Azure |
| 10221-10231 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě), nástroje pro vyrovnávání zatížení Azure |
| 13000-13999 |Příchozí |TCP |Komunikace klientů Redis clustery Vyrovnávání zatížení Azure | (Redis podsítě) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 15000-15999 |Příchozí |TCP |Komunikace klienta s redis cache, Azure načíst vyrovnávání | (Redis podsítě) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 16001 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Redis podsítě) |Nástroj pro vyrovnávání zatížení Azure |
| 20226 |Příchozí |TCP |Interní komunikaci pro Redis | (Redis podsítě) |(Redis podsítě) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Další požadavky síťového připojení virtuální sítě

Existují požadavky síťového připojení pro Azure Redis Cache, která nemusí být splněny zpočátku ve virtuální síti. Azure Redis Cache vyžaduje následující položky fungovat správně při použití v rámci virtuální sítě.

* Odchozí síťové připojení ke koncovým bodům služby Azure Storage po celém světě. Jedná se o koncových bodech, které jsou umístěné ve stejné oblasti jako instanci služby Azure Redis Cache, stejně jako koncové body úložiště nachází v **jiných** oblastí Azure. Koncové body služby Azure Storage vyřešit podle následujících domén DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*a *file.core.windows.net*. 
* Odchozího síťového připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com*, a *crl.microsoft.com*. Toto připojení je potřeba k podpoře funkce SSL.
* Konfigurace DNS pro virtuální síť musí být schopné řeší všechny koncové body a domén, které jsou uvedené v předchozích bodů. Mohou být splněny tyto požadavky na DNS tím, že zajišťuje platný infrastruktury služby DNS je konfiguraci a údržbu pro virtuální síť.
* Odchozího síťového připojení na následující Azure Monitoring koncové body, které se přeloží podle následujících domén DNS: Severní prod2.prod2.metrics.nsatc.net shoebox2-black.shoebox2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net – východ – prod2.prod2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak ověřit, že mezipaměť funguje ve virtuální síti?

>[!IMPORTANT]
>Při připojování k instanci služby Azure Redis Cache, která je hostována ve virtuální síti, vaši klienti mezipaměti musí být ve stejné virtuální síti nebo ve virtuální síti s povoleným partnerským vztahem virtuální sítě. To zahrnuje všechny testovací aplikace nebo diagnostické nástroje příkaz ping. Bez ohledu na to, kde se hostuje klientské aplikace skupiny zabezpečení sítě musí být nakonfigurovaný tak, že je povolený provoz sítě klienta k dosažení Redis instance.
>
>

Jakmile požadavky na porty jsou nastavená způsobem popsaným v předchozí části, můžete ověřit, že mezipaměť pracuje podle následujících kroků.

- [Restartovat](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud všechny závislosti požadované mezipaměti není dostupný (jak je uvedeno v [příchozí požadavky na porty](cache-how-to-premium-vnet.md#inbound-port-requirements) a [požadavky na odchozích portů](cache-how-to-premium-vnet.md#outbound-port-requirements)), mezipaměť nebude možné úspěšně spustit.
- Po restartování uzly mezipaměti (jak je hlásí stav mezipaměti na webu Azure Portal), můžete provést následující testy:
  - Odešlete zprávu ping koncový bod mezipaměti (pomocí portu 6380) z počítače, který je v rámci stejné virtuální síti jako mezipaměť, pomocí [použít příkaz tcping](https://www.elifulkerson.com/projects/tcping.php). Příklad:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud `tcping` nástroj hlásí, že je otevřený port, mezipaměť je dostupná pro připojení z klientů ve virtuální síti.

  - Jiný způsob, jak otestovat je vytvoření testovacího klienta mezipaměti (které by mohly být jednoduchou konzolovou aplikaci v jazyce StackExchange.Redis), který se připojí k mezipaměti a přidá a načte některé položky z mezipaměti. Nainstalujte ukázkovou aplikaci klienta na virtuální počítač, který je ve stejné virtuální síti jako mezipaměť a spustíme ji k ověření připojení k mezipaměti.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Při pokusu o připojení pro mezipaměť Redis ve virtuální síti, proč dochází k chybě oznamující, že vzdálený certifikát není platný?

Při pokusu o připojení k mezipaměti redis Cache ve virtuální síti, se zobrazí chyba ověření certifikátu takovou situaci:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být, že se připojujete k hostiteli podle IP adresy. Doporučujeme použít název hostitele. Jinými slovy použijte následující:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Nepoužívejte IP adresu, podobně jako následující připojovací řetězec:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nelze přeložit název DNS, některé klientské knihovny zahrnují možnosti konfigurace, jako je `sslHost` poskytnutá klient StackExchange.Redis. To umožňuje změnit název hostitele pro ověření certifikátu. Příklad:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Můžete použít virtuální sítě s mezipamětí standard nebo basic?
Virtuálními sítěmi jde použít jenom u prémiových mezipamětí.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Proč vytvoření mezipaměti Redis selže v některých podsítě, ale ne pro jiné?
Pokud provádíte nasazení Azure Redis Cache k virtuální síti správce prostředků, do mezipaměti musí být ve vyhrazené podsíti, která obsahuje žádný jiný typ prostředku. Pokud je proveden pokus o nasazení Azure Redis Cache do podsítě virtuální sítě Resource Manageru, která obsahuje další prostředky, nasazení se nezdaří. Než budete moct vytvořit nové mezipaměti Redis, musíte odstranit stávající prostředky v podsíti.

Více typů prostředků můžete nasadit do klasické virtuální sítě, za předpokladu, máte k dispozici dostatek IP adres.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na místo adres podsítě?
Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adresy používané službou infrastruktury virtuální sítě Azure každý Redis instance v podsíti využívá dvě IP adresy za horizontální oddíl a jeden další IP adresy nástroje pro vyrovnávání zatížení. Mezipaměť neclusterovaný je považován za mít jeden horizontální oddíl.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?
Pokud vaše mezipaměť je součástí virtuální sítě, pouze klienti ve virtuální síti mají přístup k mezipaměti. V důsledku toho následující funkce správy mezipaměti nefungují v tuto chvíli.

* Konzola redis – protože se spouští konzola Redis v prohlížeči místní, což je mimo virtuální síť, se nemůže připojit k vaší mezipaměti.


## <a name="use-expressroute-with-azure-redis-cache"></a>Pomocí ExpressRoute s Azure Redis Cache

Zákazníci mohou připojit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruh ke své virtuální síťové infrastruktury, tak rozšířit jejich místní síť do Azure. 

Ve výchozím nastavení, nově vytvořený okruh ExpressRoute neprovádí vynucené tunelování (inzerování výchozí trasy 0.0.0.0/0) ve virtuální síti. V důsledku toho je povolen odchozí připojení k Internetu přímo z virtuální sítě a klientské aplikace se může připojit k jiné koncové body Azure, včetně Azure Redis Cache.

Ale zákazníků běžnou konfigurací je použití vynuceného tunelování (Inzerovat výchozí cestu) která vynutí odchozí internetový provoz místo toho tok místní. Tento tok provozu přeruší připojení k Azure Redis Cache, pokud je odchozí provoz pak zablokuje v místním tak, aby instance Azure Redis Cache není schopna komunikovat se jeho závislosti.

Toto řešení je definování (nejméně) trasy definované uživatelem (udr) na podsíť, která obsahuje Azure Redis Cache. Trasu UDR definuje konkrétní podsítě tras, které bude použito místo výchozí trasu.

Pokud je to možné doporučuje se použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelů všechny odchozí provoz do místní.
* Uživatelem definovaná TRASA použitá na podsíť obsahující Azure Redis Cache definuje 0.0.0.0/0 pracovního postupu pro přenosy protokolu TCP/IP do veřejného Internetu. například nastavením dalšímu typu přechodu "Internet".

Celkové požadavky z těchto kroků je, že na úrovni podsítě uživatelem definovaná TRASA má přednost před ExpressRoute vynucené tunelování, zajistila odchozí internetový přístup z Azure Redis Cache.

Připojení k instanci služby Azure Redis Cache z aplikace v místním přes ExpressRoute není typickému využití z důvodů výkonu (pro zajištění nejlepšího výkonu Azure Redis Cache klienti by měl být ve stejné oblasti jako Azure Redis Cache).

>[!IMPORTANT] 
>Trasy definované v trase UDR **musí** být dost konkrétní, aby přednost před všemi trasami inzerovanými konfigurací ExpressRoute. Následující příklad používá široký rozsah adres 0.0.0.0/0 a proto může nechtěně dojít k jeho podle inzerování tras pomocí konkrétnější rozsahy adres.

>[!WARNING]  
>Azure Redis Cache není podporované v konfiguracích ExpressRoute, který **nesprávně inzerování tras z cesty veřejného partnerského vztahu do cestou soukromého partnerského vztahu mezi**. Konfigurace ExpressRoute s nakonfigurovanými, veřejnými partnerskými uzly přijímají inzerci tras od Microsoftu pro velkou sadu rozsahů adres IP adres Microsoft Azure. Pokud tyto rozsahy adres nesprávně křížová inzerce na cestou soukromého partnerského vztahu, výsledkem je, že všechny odchozí síťové pakety z podsítě instance Azure Redis Cache jsou správně vynucuje tunelové propojení pro zákazníka místní síťové infrastruktury . Tento tok sítí dělí Azure Redis Cache. Řešení tohoto problému je ukončit křížovou inzerci tras z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu.


Základní informace o trasách definovaných uživatelem je k dispozici v tomto [přehled](../virtual-network/virtual-networks-udr-overview.md).

Další informace o ExpressRoute najdete v tématu [technický přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další postup
Další informace o použití další prémiových funkcí mezipaměti.

* [Úvod do služby Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

