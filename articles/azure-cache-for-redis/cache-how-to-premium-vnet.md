---
title: Konfigurace virtuální sítě pro Azure úrovně Premium pro Redis Cache | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat podpory služby Virtual Network vaší úrovně Premium mezipaměti Azure pro instance Redis
services: azure-cache-for-redis
documentationcenter: ''
author: yegu-ms
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: 399c6ef1d14137708ef37d4265517d41432e2dd2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106485"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Jak nakonfigurovat podpora služby Virtual Network pro mezipaměť Azure Premium pro Redis
Mezipaměti Redis Azure má různé mezipaměti nabídek, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně novými funkcemi úrovně Premium jako je clustering, trvalé a podpory služby virtual network. Virtuální síť je privátní síť v cloudu. Když Azure pro instanci Redis Cache má nakonfigurovanou virtuální síť, není veřejně adresovatelný a je přístupný pouze z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje postup konfigurace podpory služby virtual network pro Azure Cache úrovně premium pro instanci Redis.

> [!NOTE]
> Mezipaměti Redis Azure podporuje obě classic a virtuálních sítí správce prostředků.
> 
> 

Informace o dalších prémiových funkcí mezipaměti, naleznete v tématu [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Proč virtuální síť?
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) nasazení nabízí vyšší míru zabezpečení a izolaci pro mezipaměť Azure Redis, stejně jako podsítě, zásady řízení přístupu a jiné funkce pro další omezení přístupu.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Podpora služby Virtual Network (VNet) je nakonfigurován na **nové mezipaměti Redis Azure** okno během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru cenové úrovně premium můžete nakonfigurovat integrace Redis virtuální sítě tak, že vyberete virtuální síť, která je ve stejném předplatném a umístění jako mezipaměť. Použít novou virtuální síť, vytvořte ho nejprve podle postupu v [vytvořit virtuální síť pomocí webu Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo [vytvoření virtuální sítě (classic) pomocí webu Azure portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a pak se vraťte **Nové mezipaměti Redis Azure** okno vytvořit a nakonfigurovat mezipaměť premium.

Ke konfiguraci virtuální sítě pro nové mezipaměti, klikněte na tlačítko **virtuální sítě** na **nové mezipaměti Redis Azure** okna a vyberte požadovanou virtuální síť v rozevíracím seznamu.

![Virtuální síť][redis-cache-vnet]

Vyberte požadované podsítě z **podsítě** rozevíracího seznamu a zadejte požadovaný **statická IP adresa**. Pokud používáte klasickou virtuální síť **statickou IP adresu** pole je volitelné, a pokud není zadaný žádný, jeden je vybrán z vybrané podsítě.

> [!IMPORTANT]
> Při použití mezipaměti Azure Redis k virtuální síti správce prostředků, do mezipaměti musí být ve vyhrazené podsíti, která neobsahuje žádné další prostředky s výjimkou mezipaměti Azure pro instance Redis. Pokud je proveden pokus o nasazení Azure Cache pro Redis k virtuální síti správce prostředků k podsíti, která obsahuje další prostředky, nasazení se nezdaří.
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

Pro připojení k mezipaměti Azure pro instanci Redis při používání virtuální sítě, zadejte název hostitele vaší mezipaměti v připojovacím řetězci, jak je znázorněno v následujícím příkladu:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Mezipaměť Azure pro virtuální síť Redis – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o ukládání do mezipaměti Azure Redis škálování.

* Jaké jsou některé běžné chybné konfigurace problémy s mezipamětí Azure Redis a virtuální sítě?
* [Jak ověřit, že mezipaměť funguje ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Při pokusu o připojení pro mezipaměť Azure redis Cache ve virtuální síti, proč se zobrazuje chyba oznamující, že vzdálený certifikát není platná?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Můžete použít virtuální sítě s mezipamětí standard nebo basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Proč vytvoření Azure Cache pro Redis selže v některých podsítě, ale ne pro jiné?
* [Jaké jsou požadavky na místo adres podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Jaké jsou některé běžné chybné konfigurace problémy s mezipamětí Azure Redis a virtuální sítě?
Pokud Azure mezipaměti Redis je hostovaný ve virtuální síti, se používají porty v následujících tabulkách. 

>[!IMPORTANT]
>Pokud porty v následujících tabulkách jsou blokovány, mezipaměti nemusí fungovat správně. Jeden nebo více z těchto portů blokované je nejběžnějším problémem chybnou konfiguraci při použití mezipaměti Azure pro Redis ve virtuální síti.
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

Existují požadavky síťového připojení pro Azure Cache pro Redis, který nemusí být splněny zpočátku ve virtuální síti. Azure mezipaměti Redis vyžaduje následující položky fungovat správně při použití v rámci virtuální sítě.

* Odchozí síťové připojení ke koncovým bodům služby Azure Storage po celém světě. Jedná se o koncové body, které jsou umístěné ve stejné oblasti jako ukládání do mezipaměti Azure pro instanci Redis, stejně jako koncové body úložiště nachází v **jiných** oblastí Azure. Koncové body služby Azure Storage vyřešit podle následujících domén DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*a *file.core.windows.net*. 
* Odchozího síťového připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com*, a *crl.microsoft.com*. Toto připojení je potřeba k podpoře funkce SSL.
* Konfigurace DNS pro virtuální síť musí být schopné řeší všechny koncové body a domén, které jsou uvedené v předchozích bodů. Mohou být splněny tyto požadavky na DNS tím, že zajišťuje platný infrastruktury služby DNS je konfiguraci a údržbu pro virtuální síť.
* Odchozího síťového připojení na následující Azure Monitoring koncové body, které se přeloží podle následujících domén DNS: Severní prod2.prod2.metrics.nsatc.net shoebox2-black.shoebox2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net – východ – prod2.prod2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak ověřit, že mezipaměť funguje ve virtuální síti?

>[!IMPORTANT]
>Při připojování k Azure Cache pro instanci Redis, která je hostována ve virtuální síti, vaši klienti mezipaměti musí být ve stejné virtuální síti nebo ve virtuální síti s povoleným partnerským vztahem virtuální sítě. To zahrnuje všechny testovací aplikace nebo diagnostické nástroje příkaz ping. Bez ohledu na to, kde se hostuje klientské aplikace skupiny zabezpečení sítě musí být nakonfigurovaný tak, že je povolený provoz sítě klienta k dosažení Redis instance.
>
>

Jakmile požadavky na porty jsou nastavená způsobem popsaným v předchozí části, můžete ověřit, že mezipaměť pracuje podle následujících kroků.

- [Restartovat](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud všechny závislosti požadované mezipaměti není dostupný (jak je uvedeno v [příchozí požadavky na porty](cache-how-to-premium-vnet.md#inbound-port-requirements) a [požadavky na odchozích portů](cache-how-to-premium-vnet.md#outbound-port-requirements)), mezipaměť nebude možné úspěšně spustit.
- Po restartování uzly mezipaměti (jak je hlásí stav mezipaměti na webu Azure Portal), můžete provést následující testy:
  - Odešlete zprávu ping koncový bod mezipaměti (pomocí portu 6380) z počítače, který je v rámci stejné virtuální síti jako mezipaměť, pomocí [použít příkaz tcping](https://www.elifulkerson.com/projects/tcping.php). Příklad:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud `tcping` nástroj hlásí, že je otevřený port, mezipaměť je dostupná pro připojení z klientů ve virtuální síti.

  - Jiný způsob, jak otestovat je vytvoření testovacího klienta mezipaměti (které by mohly být jednoduchou konzolovou aplikaci v jazyce StackExchange.Redis), který se připojí k mezipaměti a přidá a načte některé položky z mezipaměti. Nainstalujte ukázkovou aplikaci klienta na virtuální počítač, který je ve stejné virtuální síti jako mezipaměť a spustíme ji k ověření připojení k mezipaměti.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Při pokusu o připojení pro mezipaměť Azure redis Cache ve virtuální síti, proč se zobrazuje chyba oznamující, že vzdálený certifikát není platná?

Při pokusu o připojení k Azure Cache pro Redis ve virtuální síti, se zobrazí chyba ověření certifikátu takovou situaci:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být, že se připojujete k hostiteli podle IP adresy. Doporučujeme použít název hostitele. Jinými slovy použijte následující:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Nepoužívejte IP adresu, podobně jako následující připojovací řetězec:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nelze přeložit název DNS, některé klientské knihovny zahrnují možnosti konfigurace, jako je `sslHost` poskytnutá klient StackExchange.Redis. To umožňuje změnit název hostitele pro ověření certifikátu. Příklad:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Můžete použít virtuální sítě s mezipamětí standard nebo basic?
Virtuálními sítěmi jde použít jenom u prémiových mezipamětí.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Proč vytvoření Azure Cache pro Redis selže v některých podsítě, ale ne pro jiné?
Pokud provádíte nasazení Azure Cache pro Redis k virtuální síti správce prostředků, do mezipaměti musí být ve vyhrazené podsíti, která obsahuje žádný jiný typ prostředku. Pokud je proveden pokus o nasazení Azure Cache pro Redis k podsíti virtuální sítě Resource Manageru, která obsahuje další prostředky, nasazení se nezdaří. Než budete moct vytvořit novou mezipaměť Azure pro Redis, musíte odstranit stávající prostředky v podsíti.

Více typů prostředků můžete nasadit do klasické virtuální sítě, za předpokladu, máte k dispozici dostatek IP adres.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na místo adres podsítě?
Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adresy používané službou infrastruktury virtuální sítě Azure každý Redis instance v podsíti využívá dvě IP adresy za horizontální oddíl a jeden další IP adresy nástroje pro vyrovnávání zatížení. Mezipaměť neclusterovaný je považován za mít jeden horizontální oddíl.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?
Pokud vaše mezipaměť je součástí virtuální sítě, pouze klienti ve virtuální síti mají přístup k mezipaměti. V důsledku toho následující funkce správy mezipaměti nefungují v tuto chvíli.

* Konzola redis – protože se spouští konzola Redis v prohlížeči místní, což je mimo virtuální síť, se nemůže připojit k vaší mezipaměti.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Pomocí ExpressRoute s mezipaměti Azure redis Cache

Zákazníci mohou připojit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruh ke své virtuální síťové infrastruktury, tak rozšířit jejich místní síť do Azure. 

Ve výchozím nastavení, nově vytvořený okruh ExpressRoute neprovádí vynucené tunelování (inzerování výchozí trasy 0.0.0.0/0) ve virtuální síti. V důsledku toho je povolen odchozí připojení k Internetu přímo z virtuální sítě a klientské aplikace se může připojit k jiné koncové body Azure, včetně Azure mezipaměti Redis.

Ale zákazníků běžnou konfigurací je použití vynuceného tunelování (Inzerovat výchozí cestu) která vynutí odchozí internetový provoz místo toho tok místní. Tento tok provozu pro Redis přestane fungovat připojení k mezipaměti Azure, pokud je odchozí provoz pak zablokuje v místním tak, že ukládání do mezipaměti Azure pro instanci Redis není schopen komunikovat s jeho závislosti.

Toto řešení je definování (nejméně) trasy definované uživatelem (udr) na podsíť, která obsahuje ukládání do mezipaměti Azure Redis. Trasu UDR definuje konkrétní podsítě tras, které bude použito místo výchozí trasu.

Pokud je to možné doporučuje se použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynucené tunelů všechny odchozí provoz do místní.
* Uživatelem definovaná TRASA použitá na podsíť obsahující ukládání do mezipaměti Azure Redis definuje 0.0.0.0/0 pracovního postupu pro přenosy protokolu TCP/IP do veřejného Internetu. například nastavením dalšímu typu přechodu "Internet".

Celkové požadavky z těchto kroků je, že úrovni podsítě uživatelem definovaná TRASA má přednost před ExpressRoute vynucené tunelování, čímž zajišťuje odchozí internetový přístup z mezipaměti Azure pro Redis.

Připojení k Azure pro instanci Redis Cache z místní aplikaci pomocí ExpressRoute není typickému využití z důvodů výkonu (pro zajištění nejlepšího výkonu mezipaměti Azure Redis klientů musí být ve stejné oblasti jako mezipaměť Azure Redis) .

>[!IMPORTANT] 
>Trasy definované v trase UDR **musí** být dost konkrétní, aby přednost před všemi trasami inzerovanými konfigurací ExpressRoute. Následující příklad používá široký rozsah adres 0.0.0.0/0 a proto může nechtěně dojít k jeho podle inzerování tras pomocí konkrétnější rozsahy adres.

>[!WARNING]  
>Mezipaměť Azure pro Redis není podporované v konfiguracích ExpressRoute, který **nesprávně inzerování tras z cesty veřejného partnerského vztahu do cestou soukromého partnerského vztahu mezi**. Konfigurace ExpressRoute s nakonfigurovanými, veřejnými partnerskými uzly přijímají inzerci tras od Microsoftu pro velkou sadu rozsahů adres IP adres Microsoft Azure. Pokud tyto rozsahy adres nesprávně křížová inzerce na cestou soukromého partnerského vztahu, výsledkem je, že všechny odchozí síťové pakety z mezipaměti Azure pro instanci Redis podsítě jsou správně vynucuje tunelové propojení pro zákazníka v místní síti infrastruktura. Tento tok sítí konců mezipaměti Azure Redis. Řešení tohoto problému je ukončit křížovou inzerci tras z cesty veřejného partnerského vztahu pro cestou soukromého partnerského vztahu.


Základní informace o trasách definovaných uživatelem je k dispozici v tomto [přehled](../virtual-network/virtual-networks-udr-overview.md).

Další informace o ExpressRoute najdete v tématu [technický přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další postup
Další informace o použití další prémiových funkcí mezipaměti.

* [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

