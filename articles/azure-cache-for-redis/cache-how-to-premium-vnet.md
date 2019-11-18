---
title: Konfigurace Virtual Network pro mezipaměť Azure úrovně Premium pro Redis
description: Naučte se vytvářet a spravovat podporu Virtual Network pro Azure cache úrovně Premium pro instance Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: b2ddac9439183321691104d4eedccb0c971d19c9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129402"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Jak nakonfigurovat Virtual Network podporu pro Azure cache Premium pro Redis
Azure cache pro Redis má různé nabídky mezipaměti, které poskytují flexibilitu v výběru velikosti a funkcí mezipaměti, včetně funkcí úrovně Premium, jako je podpora clusteringu, trvalosti a virtuální sítě. Virtuální síť je privátní síť v cloudu. Když je u instance Azure cache for Redis nakonfigurovaná virtuální síť, není veřejně adresovatelná a je dostupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje, jak nakonfigurovat podporu virtuální sítě pro instanci Redis Premium Azure cache.

> [!NOTE]
> Mezipaměť Azure pro Redis podporuje klasické i Správce prostředků virtuální sítě.
> 
> 

Informace o dalších funkcích mezipaměti Premium najdete v tématu [Úvod do mezipaměti Azure pro Redis úrovně Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Proč VNet?
Nasazení [azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/) poskytuje rozšířené zabezpečení a izolaci pro mezipaměť Azure pro Redis a podsítě, zásady řízení přístupu a další funkce pro další omezení přístupu.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Podpora Virtual Network (VNet) je nakonfigurovaná v **novém okně Azure cache pro Redis** během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru cenové úrovně Premium můžete nakonfigurovat integraci virtuální sítě Redis tak, že vyberete virtuální síť, která se nachází ve stejném předplatném a umístění jako vaše mezipaměť. Pokud chcete použít novou virtuální síť, vytvořte ji jako první pomocí postupu v části [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo [vytvořte virtuální síť (classic) pomocí Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a pak se vraťte do **nového okna Azure cache pro Redis** a vytvořte a nakonfigurujte mezipaměť Premium.

Pokud chcete pro novou mezipaměť nakonfigurovat virtuální síť, klikněte na **Virtual Network** v **novém okně Azure cache pro Redis** a v rozevíracím seznamu vyberte požadovanou virtuální síť.

![Virtuální síť][redis-cache-vnet]

V rozevíracím seznamu **podsíť** vyberte požadovanou podsíť a zadejte požadovanou **statickou IP adresu**. Pokud používáte klasickou virtuální síť, pole **statické IP adresy** je volitelné a pokud není zadané, vybere se jedna z vybraných podsítí.

> [!IMPORTANT]
> Při nasazování mezipaměti Azure pro Redis do virtuální sítě Správce prostředků se mezipaměť musí nacházet ve vyhrazené podsíti, která neobsahuje žádné další prostředky s výjimkou mezipaměti Azure pro instance Redis. Pokud se provede pokus o nasazení mezipaměti Azure pro Redis do virtuální sítě Správce prostředků do podsítě, která obsahuje další prostředky, nasazení se nezdaří.
> 
> 

![Virtuální síť][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) .
> 
> Kromě IP adres, které používá infrastruktura virtuální sítě Azure, každá instance Redis v podsíti používá dvě IP adresy na horizontálních oddílů a jednu další IP adresu pro nástroj pro vyrovnávání zatížení. Mezipaměť, která není Clusterová, se považuje za jednu horizontálních oddílů.
> 
> 

Po vytvoření mezipaměti můžete kliknutím na **Virtual Network** v **nabídce prostředků**zobrazit konfiguraci virtuální sítě.

![Virtuální síť][redis-cache-vnet-info]

Pokud se chcete připojit ke službě Azure cache pro instanci Redis při použití virtuální sítě, zadejte název hostitele vaší mezipaměti do připojovacího řetězce, jak je znázorněno v následujícím příkladu:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Nejčastější dotazy k Azure cache pro virtuální sítě Redis
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se škálování Azure cache pro Redis.

* Jaké jsou některé běžné problémy s chybou konfigurace Azure cache pro Redis a virtuální sítě?
* [Jak ověřit, že mezipaměť funguje ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Proč se při pokusu o připojení k mezipaměti Azure pro Redis ve virtuální síti zobrazuje chyba s oznámením, že vzdálený certifikát je neplatný?
* [Můžu použít virtuální sítě se standardní nebo základní mezipamětí?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Proč v některých podsítích selže vytváření mezipaměti Azure pro Redis, ale ne jiné?
* [Jaké jsou požadavky na adresní prostor podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Jaké jsou některé běžné problémy s chybou konfigurace Azure cache pro Redis a virtuální sítě?
Když je Azure cache for Redis hostovaný ve virtuální síti, použijí se porty v následujících tabulkách. 

>[!IMPORTANT]
>Pokud jsou porty v následujících tabulkách blokované, mezipaměť nemusí správně fungovat. Pokud používáte službu Azure cache pro Redis ve virtuální síti, je jeden nebo více těchto portů v neblokovaném problému s konfigurací.
> 
> 

- [Požadavky na Odchozí porty](#outbound-port-requirements)
- [Požadavky na porty pro příchozí spojení](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Požadavky na Odchozí porty

K dispozici jsou devět požadavků na Odchozí porty.

- Všechna odchozí připojení k Internetu je možné provést prostřednictvím místního zařízení auditu klienta.
- Tři z portů směrují provoz do Azure Endpoints Servicing Azure Storage a Azure DNS.
- Zbývající rozsahy portů a interní komunikace podsítě Redis. Pro interní komunikaci mezi podsítěmi Redis se nevyžadují žádná NSG pravidla podsítě.

| Port (y) | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Odchozí |TCP |Redis závislosti na Azure Storage/PKI (Internet) | (Podsíť Redis) |* |
| 443 | Odchozí | TCP | Redis závislost na Azure Key Vault | (Podsíť Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Odchozí |TCP/UDP |Redis závislosti na DNS (Internet/VNet) | (Podsíť Redis) | 168.63.129.16 a 169.254.169.254 <sup>2</sup> a jakýkoli vlastní server DNS pro podsíť <sup>3</sup> |
| 8443 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 10221-10231 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 20226 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 13000-13999 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 15000-15999 |Odchozí |TCP |Interní komunikace pro Redis a geografickou replikaci | (Podsíť Redis) |(Podsíť Redis) (Geografická podsíť pro rovnocenné repliky) |
| 6379-6380 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> můžete použít značku služby AzureKeyVault s správce prostředkůmi skupinami zabezpečení sítě.

<sup>2</sup> tyto IP adresy vlastněné Microsoftem slouží k adresování HOSTITELSKÉHO virtuálního počítače, který slouží Azure DNS.

<sup>3</sup> není potřeba pro podsítě bez vlastního serveru DNS ani novějších mezipamětí Redis, které ignorují vlastní DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Požadavky na partnerský port geografické replikace

Pokud používáte mezi mezipamětí v Azure Virtual Networkch replikaci mezi mezipamětí, pamatujte na to, že doporučená konfigurace odblokuje porty 15000-15999 pro celou podsíť v příchozím i odchozím směru do mezipaměti, takže všechny součásti repliky v podsíti může vzájemně komunikovat přímo, a to i v případě budoucí geografické převzetí služeb při selhání.

#### <a name="inbound-port-requirements"></a>Požadavky na porty pro příchozí spojení

Existuje osm požadavků na rozsah příchozích portů. Příchozí požadavky v těchto rozsahech jsou buď příchozí z jiných služeb hostovaných ve stejné virtuální síti, nebo interní pro komunikaci podsítě Redis.

| Port (y) | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Příchozí |TCP |Komunikace klienta s Redis, Vyrovnávání zatížení Azure | (Podsíť Redis) | (Redis podsíť), Virtual Network Azure Load Balancer <sup>1</sup> |
| 8443 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 8500 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Podsíť Redis) |Azure Load Balancer |
| 10221-10231 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Redis podsíť), Azure Load Balancer |
| 13000-13999 |Příchozí |TCP |Komunikace klienta s Redis clustery, Vyrovnávání zatížení Azure | (Podsíť Redis) |Virtual Network Azure Load Balancer |
| 15000-15999 |Příchozí |TCP |Komunikace klientů s Redis clustery, vyrovnáváním zatížení Azure a geografickou replikací | (Podsíť Redis) |Virtual Network, Azure Load Balancer (geografická podsíť druhé repliky) |
| 16001 |Příchozí |TCP/UDP |Vyrovnávání zatížení Azure | (Podsíť Redis) |Azure Load Balancer |
| 20226 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> můžete použít značku služby ' AzureLoadBalancer ' (Správce prostředků) (nebo ' AZURE_LOADBALANCER ' pro klasický) pro vytváření pravidel NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Další požadavky na připojení k síti virtuální sítě

Existují požadavky na připojení k síti pro službu Azure cache pro Redis, které nemusí být zpočátku splněné ve virtuální síti. Azure cache pro Redis vyžaduje, aby při použití v rámci virtuální sítě správně fungovaly všechny následující položky.

* Odchozí připojení k síti Azure Storage koncovým bodům po celém světě. To zahrnuje koncové body umístěné ve stejné oblasti jako Azure cache for Redis instance a koncové body úložiště umístěné v **jiných** oblastech Azure. Azure Storage koncových bodů se vyhodnotí v následujících doménách DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *Queue.Core.Windows.NET*a *File.Core.Windows.NET*. 
* Odchozí připojení k síti pro *OCSP.msocsp.com*, *mscrl.Microsoft.com*a *CRL.Microsoft.com*. Toto připojení je potřeba k podpoře funkcí SSL.
* Konfigurace DNS pro virtuální síť musí umožňovat překlad všech koncových bodů a domén uvedených v předchozích bodech. Tyto požadavky DNS můžou být splněné tím, že zajistí konfiguraci a údržbu platné infrastruktury DNS pro virtuální síť.
* Odchozí síťové připojení k následujícím koncovým bodům monitorování Azure, které se řeší v následujících doménách DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak ověřit, že mezipaměť funguje ve virtuální síti?

>[!IMPORTANT]
>Když se připojujete ke službě Azure cache pro instanci Redis, která je hostovaná ve virtuální síti, musí být klienti mezipaměti ve stejné virtuální síti nebo ve virtuální síti s povoleným partnerským vztahem virtuálních sítí ve stejné oblasti Azure. Globální VNET Peering nejsou aktuálně podporovány. To zahrnuje všechny testovací aplikace nebo nástroje pro testování testů a diagnostiky. Bez ohledu na to, kde je klientská aplikace hostovaná, je nutné nakonfigurovat skupiny zabezpečení sítě tak, aby síťový provoz klienta měl přístup k instanci Redis.
>
>

Po nakonfigurování požadavků na porty, jak je popsáno v předchozí části, můžete ověřit, že mezipaměť funguje, provedením následujících kroků.

- [Restartujte](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud není dostupný žádný z požadovaných závislostí mezipaměti (jak je uvedeno v [požadavcích na příchozí porty](cache-how-to-premium-vnet.md#inbound-port-requirements) a [odchozí požadavky na porty](cache-how-to-premium-vnet.md#outbound-port-requirements)), mezipaměť nebude možné úspěšně restartovat.
- Po restartování uzlů mezipaměti (jak je uvedeno v Azure Portal stavu mezipaměti) můžete provádět následující testy:
  - Otestujte koncový bod mezipaměti (pomocí portu 6380) z počítače, který je ve stejné virtuální síti jako mezipaměť, pomocí [tcping](https://www.elifulkerson.com/projects/tcping.php). Příklad:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud nástroj `tcping` hlásí, že je port otevřený, je mezipaměť dostupná pro připojení od klientů ve virtuální síti.

  - Další možností testování je vytvořit klienta testovací mezipaměti (což může být jednoduchá Konzolová aplikace s použitím StackExchange. Redis), která se připojuje k mezipaměti a přidává a načítá některé položky z mezipaměti. Nainstalujte ukázkovou klientskou aplikaci do virtuálního počítače, který je ve stejné virtuální síti jako mezipaměť, a spusťte ho, abyste ověřili připojení k mezipaměti.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Proč se při pokusu o připojení k mezipaměti Azure pro Redis ve virtuální síti zobrazuje chyba s oznámením, že vzdálený certifikát je neplatný?

Při pokusu o připojení k mezipaměti Azure pro Redis ve virtuální síti se zobrazí chyba ověření certifikátu, například:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být to, že se k hostiteli připojujete prostřednictvím IP adresy. Doporučujeme použít název hostitele. Jinými slovy, použijte následující:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vyhněte se použití IP adresy, která je podobná následujícímu připojovacímu řetězci:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nemůžete přeložit název DNS, některé klientské knihovny zahrnují možnosti konfigurace, jako je `sslHost`, které poskytuje klient StackExchange. Redis. To vám umožní přepsat název hostitele, který se používá k ověření certifikátu. Příklad:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Můžu použít virtuální sítě se standardní nebo základní mezipamětí?
Virtuální sítě se dá použít jenom s mezipamětí úrovně Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Proč v některých podsítích selže vytváření mezipaměti Azure pro Redis, ale ne jiné?
Pokud nasazujete mezipaměť Azure pro Redis do virtuální sítě Správce prostředků, musí být mezipaměť ve vyhrazené podsíti, která neobsahuje žádný jiný typ prostředku. Pokud se provede pokus o nasazení mezipaměti Azure pro Redis do podsítě Správce prostředků virtuální sítě, která obsahuje další prostředky, nasazení se nezdaří. Aby bylo možné vytvořit novou mezipaměť Azure pro Redis, musíte odstranit stávající prostředky v podsíti.

Na klasickou virtuální síť můžete nasadit více typů prostředků, pokud máte k dispozici dostatek IP adres.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na adresní prostor podsítě?
Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) .

Kromě IP adres, které používá infrastruktura virtuální sítě Azure, každá instance Redis v podsíti používá dvě IP adresy na horizontálních oddílů a jednu další IP adresu pro nástroj pro vyrovnávání zatížení. Mezipaměť, která není Clusterová, se považuje za jednu horizontálních oddílů.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?
Když je mezipaměť součástí virtuální sítě, budou mít přístup k mezipaměti jenom klienti ve virtuální síti. V důsledku toho tyto funkce správy mezipaměti v tuto chvíli nefungují.

* Konzola Redis – protože konzola Redis běží v místním prohlížeči, který je mimo virtuální síť, nemůže se připojit ke svojí mezipaměti.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Použití ExpressRoute s Azure cache pro Redis

Zákazníci můžou připojit okruh [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ke své infrastruktuře virtuální sítě, a tak rozšířit svou místní síť do Azure. 

Ve výchozím nastavení nově vytvořený okruh ExpressRoute neprovádí vynucené tunelování (inzerování výchozí trasy 0.0.0.0/0) ve virtuální síti. V důsledku toho je odchozí připojení k Internetu povoleno přímo z virtuální sítě a klientské aplikace se mohou připojit k jiným koncovým bodům Azure, včetně mezipaměti Azure pro Redis.

Běžnou zákaznickou konfigurací ale je použití vynuceného tunelování (inzerování výchozí trasy), která vynucuje odchozí internetový provoz místo toho, aby se směrovala místně. Tento tok přenosů přeruší připojení ke službě Azure cache pro Redis, pokud je odchozí přenosy blokované místně, takže instance Azure cache for Redis nemůže komunikovat s jejími závislostmi.

Řešením je definovat jednu (nebo více) uživatelem definovaných tras (udr) v podsíti, která obsahuje mezipaměť Azure pro Redis. UDR definuje trasy specifické pro podsíť, které se budou respektovat místo výchozí trasy.

Pokud je to možné, doporučuje se použít následující konfiguraci:

* Konfigurace ExpressRoute inzeruje v síti 0.0.0.0/0 a ve výchozím nastavení vynutí tunelové propojení veškerého odchozího provozu v místním prostředí.
* UDR, který se použije pro podsíť obsahující mezipaměť Azure pro Redis, definuje 0.0.0.0/0 s pracovní trasou pro provoz TCP/IP na veřejný Internet. například nastavením typu dalšího segmentu směrování na "Internet".

Kombinovaný efekt těchto kroků je, že UDR úrovně podsítě má přednost před vynuceným tunelovým propojením ExpressRoute, čímž zajišťuje odchozí internetový přístup z mezipaměti Azure pro Redis.

Připojení k Azure cache pro instanci Redis z místní aplikace pomocí ExpressRoute není Typický scénář použití z důvodů výkonu (pro nejvyšší výkon Azure cache pro klienty Redis by mělo být ve stejné oblasti jako mezipaměť Azure pro Redis). .

>[!IMPORTANT] 
>Trasy definované v UDR **musí** být dostatečně specifické, aby měly přednost před všemi trasami inzerovanou konfigurací ExpressRoute. Následující příklad používá širokou škálu adres 0.0.0.0/0 a jako takový může být potenciálně omylem přepsán inzerováním směrování s použitím více konkrétních rozsahů adres.

>[!WARNING]  
>Mezipaměť Azure pro Redis se nepodporuje u konfigurací ExpressRoute, které **nesprávně přecházejí trasy z cesty veřejného partnerského vztahu na cestu privátního partnerského vztahu**. Konfigurace ExpressRoute s nakonfigurovaným veřejným partnerským vztahem, obdrží od Microsoftu inzerci tras od Microsoftu pro velkou sadu Microsoft Azure rozsahů IP adres. Pokud jsou tyto rozsahy adres nesprávně přeložené na cestě privátního partnerského vztahu, je výsledkem to, že všechny odchozí síťové pakety z mezipaměti Azure pro instanci instance Redis jsou nesprávně vynucené tunelování do místní sítě zákazníka. infrastrukturu. Tento tok sítě přeruší službu Azure cache pro Redis. Řešením tohoto problému je zastavit trasy mezi reklamními partnery z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu.


V tomto [přehledu](../virtual-network/virtual-networks-udr-overview.md)jsou k dispozici základní informace o trasách definovaných uživatelem.

Další informace o ExpressRoute najdete v [technickém přehledu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další kroky
Naučte se používat víc funkcí mezipaměti Premium.

* [Seznámení s mezipamětí Azure pro Redis úrovně Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
