---
title: Konfigurace virtuální sítě – premium azure cache pro Redis
description: Přečtěte si, jak vytvořit a spravovat podporu virtuální sítě pro vaše instance Azure Cache pro redis úrovně Premium.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2821ee637b2562b5287dd3d59cf943b3dcb7ef97
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010881"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Jak nakonfigurovat podporu virtuálních sítí pro prémiovou mezipaměť Azure pro Redis
Azure Cache for Redis má různé nabídky mezipaměti, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně funkcí úrovně Premium, jako je clustering, trvalost a podpora virtuálních sítí. Virtuální síť je privátní síť v cloudu. Když je instance Azure Cache for Redis nakonfigurovaná pomocí virtuální sítě, není veřejně adresovatelná a je přístupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje, jak nakonfigurovat podporu virtuální sítě pro prémiovou instanci Azure Cache for Redis.

> [!NOTE]
> Azure Cache for Redis podporuje klasické virtuální sítě i virtuální sítě Správce prostředků.
> 
> 

Informace o dalších funkcích mezipaměti premium najdete [v tématu Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Proč virtuální síť?
[Nasazení virtuální sítě Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) poskytuje rozšířené zabezpečení a izolaci pro vaši Mezipaměť Azure pro Redis, stejně jako podsítě, zásady řízení přístupu a další funkce pro další omezení přístupu.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Podpora virtuální sítě (VNet) je nakonfigurovaná v okně **Nová mezipaměť Azure pro Redis** během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Jakmile vyberete prémiovou cenovou úroveň, můžete nakonfigurovat integraci virtuální sítě Redis výběrem virtuální sítě, která je ve stejném předplatném a umístění jako vaše mezipaměť. Pokud chcete použít novou virtuální síť, vytvořte ji nejprve podle kroků v [části Vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo Vytvoření virtuální sítě [(klasické) pomocí portálu Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a potom se vraťte do okna Nová azure cache for **Redis** k vytvoření a konfiguraci mezipaměti premium.

Pokud chcete nakonfigurovat virtuální síť pro novou mezipaměť, klikněte na **virtuální síť** v okně **Nová mezipaměť Azure pro Redis** a v rozevíracím seznamu vyberte požadovanou virtuální síť.

![Virtuální síť][redis-cache-vnet]

V rozevíracím seznamu **Podsíť** vyberte požadovanou podsíť.  V případě potřeby zadejte **statickou adresu IP**. Pole **Statická adresa IP** je volitelné a pokud není zadáno žádné, je z vybrané podsítě vybráno jedno.

> [!IMPORTANT]
> Při nasazování azure cache pro Redis na virtuální síť Správce prostředků, mezipaměti musí být ve vyhrazené podsíti, která neobsahuje žádné jiné prostředky s výjimkou Azure Cache pro redis instance. Pokud se pokusíte nasadit mezipaměť Azure pro Redis do virtuální sítě Správce prostředků do podsítě, která obsahuje další prostředky, nasazení se nezdaří.
> 
> 

![Virtuální síť][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure si rezervuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazeny pro shodu protokolu spolu s dalšími třemi adresami používanými pro služby Azure. Další informace naleznete v tématu [Existují nějaká omezení pro používání IP adres v těchto podsítích?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Kromě IP adres používaných infrastrukturou virtuální sítě Azure používá každá instance Redis v podsíti dvě IP adresy na střep a jednu další IP adresu pro vykladač zatížení. Neclusterovaná mezipaměť je považována za jednu úložnou skupinu.
> 
> 

Po vytvoření mezipaměti můžete zobrazit konfiguraci virtuální sítě klepnutím na **virtuální síť** z **nabídky Prostředek**.

![Virtuální síť][redis-cache-vnet-info]

Chcete-li se připojit k instanci Azure Cache for Redis při použití virtuální sítě, zadejte název hostitele mezipaměti v připojovacím řetězci, jak je znázorněno v následujícím příkladu:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Nejčastější dotazy k Azure Cache for Redis VNet
Následující seznam obsahuje odpovědi na běžně kladené otázky týkající se azure cache pro redis škálování.

* Jaké jsou některé běžné problémy s chybnou konfigurací s Azure Cache pro Redis a virtuální sítě?
* [Jak ověřit fungování mezipaměti ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Při pokusu o připojení k mé Azure Cache pro Redis ve virtuální síti, proč se zobrazuje chyba oznamující, že vzdálený certifikát je neplatný?
* [Můžu virtuální sítě používat se standardní nebo základní mezipamětí?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Proč vytvoření Azure Cache pro Redis nezdaří v některých podsítích, ale ne v jiných?
* [Jaké jsou požadavky na místo adres podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Jaké jsou některé běžné problémy s chybnou konfigurací s Azure Cache pro Redis a virtuální sítě?
Když je azure cache pro Redis hostované ve virtuální síti, používají se porty v následujících tabulkách. 

>[!IMPORTANT]
>Pokud jsou porty v následujících tabulkách blokovány, nemusí mezipaměť fungovat správně. S jeden nebo více z těchto portů blokovánje je nejčastější problém s chybnou konfigurací při použití Azure Cache pro Redis ve virtuální síti.
> 
> 

- [Požadavky na port pro odchozí spojení](#outbound-port-requirements)
- [Požadavky na port pro příchozí spojení](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Požadavky na port pro odchozí spojení

Existuje devět požadavků na odchozí port. Odchozí požadavky v těchto rozsahech jsou buď odchozí do jiných služeb nezbytných pro fungování mezipaměti nebo interní do podsítě Redis pro komunikaci internode. Pro geografickou replikaci existují další odchozí požadavky pro komunikaci mezi podsítěmi primární a sekundární mezipaměti.

| Port(y) | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Odchozí |TCP |Závislosti Redis u Azure Storage/PKI (Internet) | (Podsíť Redis) |* |
| 443 | Odchozí | TCP | Závislost Redis na Azure Key Vault | (Podsíť Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Odchozí |Protokol TCP/UDP |Závislosti redis u DNS (Internet/Virtuální síť) | (Podsíť Redis) | 168.63.129.16 a 169.254.169.254 <sup>2</sup> a libovolný vlastní server DNS pro podsíť <sup>3</sup> |
| 8443 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 10221-10231 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 20226 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 13000-13999 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 15000-15999 |Odchozí |TCP |Interní komunikace pro Redis a geografickou replikaci | (Podsíť Redis) |(Podsíť Redis) (Podsíť partnerské sítě geografické repliky) |
| 6379-6380 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> Značku služby AzureKeyVault můžete použít se skupinami zabezpečení sítě Resource Manager.

<sup>2</sup> Tyto IP adresy vlastněné společností Microsoft se používají k adresě hostitelského virtuálního počítače, který obsluhuje Azure DNS.

<sup>3</sup> Není potřeba pro podsítě bez vlastního serveru DNS nebo novější redis mezipaměti, které ignorují vlastní DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Požadavky na porty partnerských uzlů geografické replikace

Pokud používáte geografickou replikaci mezi mezipaměti ve virtuálních sítích Azure, vezměte prosím na vědomí, že doporučená konfigurace je odblokovat porty 15000-15999 pro celou podsíť v příchozích i odchozích směrech do obou mezipamětí, takže všechny součásti replik v podsíti mohou komunikovat přímo mezi sebou i v případě budoucího geografického převzetí služeb při selhání.

#### <a name="inbound-port-requirements"></a>Požadavky na port pro příchozí spojení

Existuje osm požadavků na rozsah příchozích portů. Příchozí požadavky v těchto rozsahech jsou příchozí z jiných služeb hostovaných ve stejné virtuální síti nebo interní komunikace podsítě Redis.

| Port(y) | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Příchozí |TCP |Komunikace s klientem redis, vyrovnávání zatížení Azure | (Podsíť Redis) | (podsíť Redis), virtuální síť, nástroj Azure Load Balancer <sup>1</sup> |
| 8443 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 8500 |Příchozí |Protokol TCP/UDP |Vyrovnávání zatížení Azure | (Podsíť Redis) |Azure Load Balancer |
| 10221-10231 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(podsíť Redis), Azure Load Balancer |
| 13000-13999 |Příchozí |TCP |Komunikace klienta s clustery Redis, vyrovnávání zatížení Azure | (Podsíť Redis) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure |
| 15000-15999 |Příchozí |TCP |Klientská komunikace s clustery Redis, vyrovnávání zatížení Azure a geografická replikace | (Podsíť Redis) |Virtuální síť, nástroj pro vyrovnávání zatížení Azure(podsíť partnerské sítě geografické repliky) |
| 16001 |Příchozí |Protokol TCP/UDP |Vyrovnávání zatížení Azure | (Podsíť Redis) |Azure Load Balancer |
| 20226 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> Pro vytváření pravidel skupiny zabezpečení sítě můžete použít výrobní číslo AzureLoadBalancer (Resource Manager) (nebo "AZURE_LOADBALANCER" pro klasickou).

#### <a name="additional-vnet-network-connectivity-requirements"></a>Další požadavky na připojení k síti virtuální sítě

Existují požadavky na připojení k síti pro Azure Cache pro Redis, které nemusí být zpočátku splněny ve virtuální síti. Azure Cache for Redis vyžaduje, aby všechny následující položky fungovaly správně, pokud jsou používány ve virtuální síti.

* Odchozí síťové připojení ke koncovým bodům Azure Storage po celém světě. To zahrnuje koncové body umístěné ve stejné oblasti jako instance Azure Cache for Redis, stejně jako koncové body úložiště umístěné v **jiných** oblastech Azure. Koncové body Azure Storage se řeší v následujících doménách DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*a *file.core.windows.net*. 
* Odchozí síťové připojení k *ocsp.msocsp.com*, *mscrl.microsoft.com*a *crl.microsoft.com*. Toto připojení je potřebné pro podporu funkcí TLS/SSL.
* Konfigurace DNS pro virtuální síť musí být schopná vyřešit všechny koncové body a domény uvedené v předchozích bodech. Tyto požadavky DNS lze splnit zajištěním platné infrastruktury DNS je nakonfigurován a udržován pro virtuální síť.
* Odchozí síťové připojení k následujícím koncovým bodům Azure Monitoring, které se řeší v následujících doménách DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak ověřit fungování mezipaměti ve virtuální síti?

>[!IMPORTANT]
>Při připojování k instanci Azure Cache for Redis, která je hostovaná ve virtuální síti, musí být klienti mezipaměti ve stejné virtuální síti nebo ve virtuální síti s partnerským vztahem virtuální sítě povoleným v rámci stejné oblasti Azure. Globální partnerský vztah virtuální sítě není aktuálně podporován. To zahrnuje všechny testovací aplikace nebo diagnostické nástroje ping. Bez ohledu na to, kde je klientská aplikace hostována, musí být skupiny zabezpečení sítě nakonfigurovány tak, aby síťový provoz klienta mohl dosáhnout instance Redis.
>
>

Jakmile jsou požadavky na port nakonfigurovány tak, jak je popsáno v předchozí části, můžete ověřit, zda mezipaměť funguje, provedením následujících kroků.

- [Restartujte](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud není možné dosáhnout všech požadovaných závislostí mezipaměti (jak je popsáno v [požadavcích na příchozí port](cache-how-to-premium-vnet.md#inbound-port-requirements) a [požadavky na odchozí port](cache-how-to-premium-vnet.md#outbound-port-requirements)), nebude možné úspěšně restartovat mezipaměť.
- Po restartování uzlů mezipaměti (jak je uvedeno stav mezipaměti na webu Azure Portal), můžete provést následující testy:
  - příkaz příkaz příkazem ping koncového bodu mezipaměti (pomocí portu 6380) z počítače, který je ve stejné virtuální síti jako cache, pomocí [tcping](https://www.elifulkerson.com/projects/tcping.php). Příklad:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud `tcping` nástroj hlásí, že port je otevřený, mezipaměť je k dispozici pro připojení od klientů ve virtuální síti.

  - Dalším způsobem, jak otestovat, je vytvořit testovací cache klienta (což by mohlo být jednoduché konzolové aplikace pomocí StackExchange.Redis), který se připojí k mezipaměti a přidá a načte některé položky z mezipaměti. Nainstalujte ukázkovou klientskou aplikaci do virtuálního počítače, který je ve stejné virtuální síti jako mezipaměť, a spusťte ji za účelem ověření připojení ke mezipaměti.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Při pokusu o připojení k mé Azure Cache pro Redis ve virtuální síti, proč se zobrazuje chyba oznamující, že vzdálený certifikát je neplatný?

Při pokusu o připojení k azure cache pro Redis ve virtuální síti se zobrazí chyba ověření certifikátu, jako je například tato:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být, že se připojujete k hostiteli pomocí adresy IP. Doporučujeme použít název hostitele. Jinými slovy, použijte následující:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Nepoužívejte adresu IP podobnou následujícímu připojovacímu řetězci:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nemůžete přeložit název DNS, některé klientské knihovny `sslHost` obsahují možnosti konfigurace, jako je to, které poskytuje klient StackExchange.Redis. To umožňuje přepsat název hostitele používaný pro ověření certifikátu. Příklad:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Můžu virtuální sítě používat se standardní nebo základní mezipamětí?
Virtuální sítě lze použít pouze s mezipamětí premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Proč vytvoření Azure Cache pro Redis nezdaří v některých podsítích, ale ne v jiných?
Pokud nasazujete mezipaměť Azure pro Redis do virtuální sítě Správce prostředků, musí být mezipaměť ve vyhrazené podsíti, která neobsahuje žádný jiný typ prostředku. Pokud se pokusíte nasadit mezipaměť Azure pro Redis do podsítě virtuální sítě Správce prostředků, která obsahuje další prostředky, nasazení se nezdaří. Před vytvořením nové mezipaměti Azure pro redis je nutné odstranit existující prostředky uvnitř podsítě.

Můžete nasadit více typů prostředků do klasické virtuální sítě tak dlouho, dokud máte dostatek IP adres k dispozici.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na místo adres podsítě?
Azure si rezervuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazeny pro shodu protokolu spolu s dalšími třemi adresami používanými pro služby Azure. Další informace naleznete v tématu [Existují nějaká omezení pro používání IP adres v těchto podsítích?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Kromě IP adres používaných infrastrukturou virtuální sítě Azure používá každá instance Redis v podsíti dvě IP adresy na střep a jednu další IP adresu pro vykladač zatížení. Neclusterovaná mezipaměť je považována za jednu úložnou skupinu.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungují všechny funkce mezipaměti při hostování mezipaměti ve virtuální síti?
Když je vaše mezipaměť součástí virtuální sítě, přístup k mezipaměti mají pouze klienti ve virtuální síti. V důsledku toho následující funkce správy mezipaměti nefungují v tomto okamžiku.

* Redis Console – Protože redis console běží v místním prohlížeči, který je mimo virtuální síť, nemůže se připojit k mezipaměti.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Použití ExpressRoute s mezipaměťAzure pro Redis

Zákazníci můžou připojit okruh [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ke své infrastruktuře virtuální sítě, a rozšířit tak svou místní síť do Azure. 

Ve výchozím nastavení nově vytvořený okruh ExpressRoute neprovádí vynucené tunelové propojení (reklama na výchozí trasu, 0.0.0.0/0) ve virtuální síti. V důsledku toho je odchozí připojení k Internetu povoleno přímo z virtuální sítě a klientské aplikace se mohou připojit k jiným koncovým bodům Azure, včetně Azure Cache for Redis.

Běžnou konfigurací zákazníka je však použití vynuceného tunelového propojení (inzerování výchozí trasy), které vynutí, aby odchozí internetový provoz místo toho tok místně. Tento tok provozu přeruší připojení s Azure Cache pro Redis, pokud odchozí provoz je pak blokován místně tak, aby instance Azure Cache for Redis není schopen komunikovat s jeho závislosti.

Řešením je definovat jednu (nebo více) uživatelem definovaných tras (UDR) v podsíti, která obsahuje mezipaměť Azure pro redis. UDR definuje trasy specifické pro podsíť, které budou dodrženy namísto výchozí trasy.

Pokud je to možné, doporučujeme použít následující konfiguraci:

* Konfigurace ExpressRoute inzeruje 0.0.0.0/0 a ve výchozím nastavení vynutit tunely všechny odchozí provoz v místním prostředí.
* UDR použité pro podsíť obsahující mezipaměť Azure pro Redis definuje 0.0.0.0/0 s pracovní trasou pro přenos tcp/IP na veřejný internet; například nastavením dalšího typu směrování na "Internet".

Kombinovaný efekt těchto kroků je, že úroveň podsítě UDR má přednost před expressroute vynucené tunelové propojení, čímž je zajištěn odchozí přístup k Internetu z mezipaměti Azure pro Redis.

Připojení k instanci Azure Cache for Redis z místní aplikace pomocí ExpressRoute není typický scénář použití z důvodu výkonu (pro nejlepší výkon Azure Cache pro klienty Redis by měl být ve stejné oblasti jako Azure Cache pro Redis).

>[!IMPORTANT] 
>Trasy definované v UDR **musí** být dostatečně specifické, aby měly přednost před všemi trasami inzerovanými konfigurací ExpressRoute. Následující příklad používá široký rozsah adres 0.0.0.0/0 a jako takový může být potenciálně náhodně přepsán a reklamy na trasu pomocí konkrétnější rozsahy adres.

>[!WARNING]  
>Azure Cache for Redis není podporována konfiguracemi ExpressRoute, které **nesprávně inzerují trasy z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu**. Konfigurace ExpressRoute, které mají nakonfigurované veřejný partnerský vztah, přijímají inzerování tras od Microsoftu pro rozsáhlou sadu rozsahů IP adres Microsoft Azure. Pokud jsou tyto rozsahy adres nesprávně inzerovány v cestě soukromého partnerského vztahu, výsledkem je, že všechny odchozí síťové pakety z podsítě instance Azure Cache pro redis jsou nesprávně vynuceně tunelovány do místní síťové infrastruktury zákazníka. Tento tok sítě přeruší mezipaměť Azure pro Redis. Řešením tohoto problému je zastavit křížové reklamní trasy z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu.


V tomto [přehledu](../virtual-network/virtual-networks-udr-overview.md)jsou k dispozici základní informace o uživatelem definovaných trasách .

Další informace o expressroute naleznete v [technickém přehledu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak používat více funkcí prémiové mezipaměti.

* [Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
