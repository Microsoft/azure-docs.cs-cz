---
title: Konfigurace virtuální sítě Azure cache na úrovni Premium pro instanci Redis
description: Naučte se vytvářet a spravovat podporu virtuálních sítí pro Redis instanci Azure na úrovni Premium.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375268"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Konfigurace podpory virtuální sítě pro instanci Azure cache Premium pro Redis

Nasazení [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) poskytuje lepší zabezpečení a izolaci spolu s podsítěmi, zásadami řízení přístupu a dalšími funkcemi pro další omezení přístupu. Když je u instance Azure cache for Redis nakonfigurovaná virtuální síť, není veřejně adresovatelná a je dostupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě. Tento článek popisuje, jak nakonfigurovat podporu virtuální sítě pro instanci Redis Azure cache úrovně Premium.

> [!NOTE]
> Azure cache pro Redis podporuje model nasazení Classic i Azure Resource Manager virtuální sítě.
> 

## <a name="set-up-virtual-network-support"></a>Nastavení podpory virtuální sítě

Podpora virtuální sítě je nakonfigurovaná v **novém podokně mezipaměti Azure pro Redis** během vytváření mezipaměti.

1. Pokud chcete vytvořit mezipaměť úrovně Premium, přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **vytvořit prostředek**. Kromě vytváření mezipamětí v Azure Portal můžete také vytvořit pomocí šablon Správce prostředků, PowerShellu nebo rozhraní příkazového řádku Azure. Další informace o tom, jak vytvořit mezipaměť Azure pro instanci Redis, najdete v tématu [vytvoření mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Snímek obrazovky, který ukazuje vytvoření prostředku.":::
   
1. Na stránce **Nový** vyberte **databáze**. Pak vyberte **Azure cache pro Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Snímek obrazovky, který zobrazuje výběr mezipaměti Azure pro Redis.":::

1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť úrovně Premium.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude *\<DNS name> . Redis.cache.Windows.NET*. |
   | **Předplatné** | V rozevíracím seznamu vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis |
   | **Skupina prostředků** | V rozevíracím seznamu vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. |
   | **Umístění** | Z rozevíracího seznamu vyberte umístění. | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž jiných služeb, které budou používat vaši mezipaměť. |
   | **Typ mezipaměti** |V rozevíracím seznamu vyberte mezipaměť úrovně Premium pro konfiguraci funkcí úrovně Premium. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/). |  Cenová úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro danou mezipaměť. Další informace najdete v tématu [Přehled služby Azure cache pro Redis](cache-overview.md). |

1. Vyberte kartu **síť** nebo klikněte na tlačítko **sítě** v dolní části stránky.

1. Na kartě **sítě** vyberte jako metodu připojení možnost **virtuální sítě** . Pokud chcete použít novou virtuální síť, vytvořte ji jako první pomocí postupu v části [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) nebo [vytvoření virtuální sítě (classic) pomocí Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Pak se vraťte do **nového podokna Azure cache for Redis** a vytvořte a nakonfigurujte mezipaměť na úrovni Premium.

   > [!IMPORTANT]
   > Když nasadíte mezipaměť Azure pro Redis do virtuální sítě Správce prostředků, mezipaměť se musí nacházet ve vyhrazené podsíti, která neobsahuje žádné další prostředky s výjimkou mezipaměti Azure pro instance Redis. Pokud se pokusíte nasadit mezipaměť Azure pro instanci Redis do podsítě Správce prostředků virtuální sítě, která obsahuje další prostředky, nasazení se nezdaří.
   > 
   > 

   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtuální síť** | Z rozevíracího seznamu vyberte svou virtuální síť. | Vyberte virtuální síť, která se nachází ve stejném předplatném a umístění jako vaše mezipaměť. |
   | **Podsíť** | Z rozevíracího seznamu vyberte svou podsíť. | Rozsah adres podsítě by měl být v zápisu CIDR (například 192.168.1.0/24). Musí být obsažený v adresním prostoru virtuální sítě. |
   | **Statická IP adresa** | Volitelné Zadejte statickou IP adresu. | Pokud nezadáte statickou IP adresu, zvolí se IP adresa automaticky. |

   > [!IMPORTANT]
   > Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) .
   > 
   > Kromě IP adres, které používá infrastruktura virtuální sítě Azure, každá instance Azure cache for Redis v podsíti používá pro nástroj pro vyrovnávání zatížení dvě IP adresy na horizontálních oddílů a jednu další IP adresu. Neclusterovaná mezipaměť se považuje za jednu horizontálních oddílů.
   > 

1. Vyberte kartu **Další: Upřesnit** nebo v dolní části stránky vyberte tlačítko **Další: Upřesnit** .

1. Na kartě **Upřesnit** u instance mezipaměti úrovně Premium nakonfigurujte nastavení pro port bez TLS, clusteringu a trvalost dat.

1. Vyberte kartu **Další: značky** , nebo vyberte tlačítko **Další: značky** v dolní části stránky.

1. Volitelně můžete na kartě **značky** zadat název a hodnotu, pokud chcete prostředek zařadit do kategorií.

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na kartu **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Po zobrazení zprávy se zobrazeným zeleným **ověřením** vyberte **vytvořit**.

Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití. Po vytvoření mezipaměti můžete zobrazit konfiguraci pro virtuální síť tak, že v nabídce **prostředek** vyberete **Virtual Network** .

![Virtuální síť][redis-cache-vnet-info]

Pokud se chcete připojit ke službě Azure cache pro instanci Redis při použití virtuální sítě, zadejte název hostitele vaší mezipaměti do připojovacího řetězce, jak je znázorněno v následujícím příkladu:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Nejčastější dotazy k mezipaměti Azure pro virtuální sítě Redis

Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se škálování Azure cache pro Redis.

* Jaké jsou některé běžné problémy s chybou konfigurace Azure cache pro Redis a virtuální sítě?
* [Jak ověřit, že mezipaměť funguje ve virtuální síti?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Proč se při pokusu o připojení ke službě Azure cache pro instanci Redis ve virtuální síti zobrazí chyba s oznámením, že vzdálený certifikát je neplatný?
* [Můžu používat virtuální sítě se standardní nebo základní mezipamětí?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Proč v některých podsítích selže vytváření mezipaměti Azure pro instanci Redis, ale ne jiné?
* [Jaké jsou požadavky na adresní prostor podsítě?](#what-are-the-subnet-address-space-requirements)
* [Fungují všechny funkce mezipaměti, když je mezipaměť hostována ve virtuální síti?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Jaké jsou některé běžné problémy s chybou konfigurace Azure cache pro Redis a virtuální sítě?

Když je Azure cache for Redis hostovaný ve virtuální síti, použijí se porty v následujících tabulkách.

>[!IMPORTANT]
>Pokud jsou porty v následujících tabulkách blokované, mezipaměť nemusí správně fungovat. Pokud používáte službu Azure cache pro Redis ve virtuální síti, je jeden nebo více těchto portů zablokovaných.
> 

- [Požadavky na port pro odchozí spojení](#outbound-port-requirements)
- [Požadavky na port pro příchozí spojení](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Požadavky na port pro odchozí spojení

K dispozici jsou devět požadavků na Odchozí porty. Odchozí požadavky v těchto oblastech jsou buď odchozí, do dalších služeb, které jsou nezbytné pro fungování mezipaměti, nebo interní pro Redis podsíť pro komunikaci mezi uzly. Pro geografickou replikaci existují další odchozí požadavky pro komunikaci mezi podsítěmi primární mezipaměti a mezipaměti repliky.

| Porty | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Odchozí |TCP |Redis závislosti na Azure Storage/PKI (Internet) | (Podsíť Redis) |* <sup>4</sup> |
| 443 | Odchozí | TCP | Redis závislost na Azure Key Vault a Azure Monitor | (Podsíť Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Odchozí |TCP/UDP |Redis závislosti na DNS (Internet/virtuální síť) | (Podsíť Redis) | 168.63.129.16 a 169.254.169.254 <sup>2</sup> a jakýkoli vlastní server DNS pro podsíť <sup>3</sup> |
| 8443 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 10221-10231 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) | (Podsíť Redis) |
| 20226 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 13000-13999 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 15000-15999 |Odchozí |TCP |Interní komunikace pro Redis a geografickou replikaci | (Podsíť Redis) |(Podsíť Redis) (Geografická podsíť pro rovnocenné repliky) |
| 6379-6380 |Odchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> můžete použít značky služby AzureKeyVault a AzureMonitor s správce prostředkůmi skupinami zabezpečení sítě (skupin zabezpečení sítě).

<sup>2</sup> tyto IP adresy vlastněné Microsoftem se používají k adresování HOSTITELSKÉHO virtuálního počítače, který obsluhuje Azure DNS.

<sup>3</sup> tyto informace nejsou potřeba pro podsítě bez vlastního serveru DNS ani novějších mezipamětí Redis, které ignorují vlastní DNS.

<sup>4</sup> Další informace najdete v tématu [Další požadavky na připojení k virtuální síti](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Požadavky na porty partnerských uzlů geografické replikace

Pokud používáte geografickou replikaci mezi mezipamětí v Azure Virtual Networks, odblokujte porty 15000-15999 pro celou podsíť v směrech příchozích *i* odchozích přenosů do mezipamětí. V této konfiguraci můžou všechny součásti repliky v podsíti přímo komunikovat, i když dojde k budoucí geografické převzetí služeb při selhání.

#### <a name="inbound-port-requirements"></a>Požadavky na port pro příchozí spojení

Existuje osm požadavků na rozsah příchozích portů. Příchozí požadavky v těchto rozsahech jsou buď příchozí z jiných služeb hostovaných ve stejné virtuální síti, nebo interní pro komunikaci podsítě Redis.

| Porty | Směr | Transportní protokol | Účel | Místní IP adresa | Vzdálená IP adresa |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Příchozí |TCP |Komunikace klienta s Redis, Vyrovnávání zatížení Azure | (Podsíť Redis) | (Redis podsíť), (podsíť klienta), AzureLoadBalancer <sup>1</sup> |
| 8443 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |
| 8500 |Příchozí |TCP/UDP |Vyrovnávání zatížení v Azure | (Podsíť Redis) | AzureLoadBalancer |
| 10221-10231 |Příchozí |TCP |Komunikace klientů s Redis clustery, interní komunikací pro Redis | (Podsíť Redis) |(Redis podsíť), AzureLoadBalancer, (klientská podsíť) |
| 13000-13999 |Příchozí |TCP |Komunikace klienta s Redis clustery, Vyrovnávání zatížení Azure | (Podsíť Redis) | (Redis podsíť), (podsíť klienta), AzureLoadBalancer |
| 15000-15999 |Příchozí |TCP |Komunikace klientů s Redis clustery, vyrovnáváním zatížení Azure a geografickou replikací | (Podsíť Redis) | (Redis podsíť), (podsíť klienta), AzureLoadBalancer (geografická podsíť druhé repliky) |
| 16001 |Příchozí |TCP/UDP |Vyrovnávání zatížení v Azure | (Podsíť Redis) | AzureLoadBalancer |
| 20226 |Příchozí |TCP |Interní komunikace pro Redis | (Podsíť Redis) |(Podsíť Redis) |

<sup>1</sup> můžete použít značku služby AzureLoadBalancer pro Správce prostředků nebo AZURE_LOADBALANCER pro model nasazení Classic pro vytváření pravidel NSG.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Další požadavky na připojení k virtuální síti

Pro službu Azure cache pro Redis existují požadavky na připojení k síti, které nemusí být zpočátku splněné ve virtuální síti. Azure cache pro Redis vyžaduje, aby při použití v rámci virtuální sítě správně fungovaly všechny následující položky:

* Odchozí připojení k síti Azure Storage koncovým bodům po celém světě. Zahrnuty jsou koncové body umístěné ve stejné oblasti jako Azure cache pro instance Redis a koncové body úložiště umístěné v *jiných* oblastech Azure. Azure Storage koncových bodů se vyhodnotí v následujících doménách DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *Queue.Core.Windows.NET* a *File.Core.Windows.NET*.
* Odchozí připojení k síti pro *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* a *CRL.Microsoft.com*. Toto připojení je potřeba k podpoře funkcí TLS/SSL.
* Konfigurace DNS pro virtuální síť musí umožňovat překlad všech koncových bodů a domén uvedených v předchozích bodech. Tyto požadavky DNS můžou být splněné tím, že zajistí konfiguraci a údržbu platné infrastruktury DNS pro virtuální síť.
* Odchozí síťové připojení k následujícím koncovým bodům Azure Monitor, které se řeší v následujících doménách DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.NET*, *North-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Black.azglobal.Metrics.nsatc.NET*, *shoebox2-Red.shoebox2.Metrics.nsatc.NET*, *East-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Red.azglobal.Metrics.nsatc.NET*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-Red.prod.microsoftmetrics.com* a *shoebox3-Black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Jak ověřit, že mezipaměť funguje ve virtuální síti?

>[!IMPORTANT]
>Když se připojíte k mezipaměti Azure pro instanci Redis, která je hostovaná ve virtuální síti, musí být klienti mezipaměti ve stejné virtuální síti nebo ve virtuální síti s povoleným partnerským vztahem virtuální sítě, který je povolený ve stejné oblasti Azure. Globální partnerské vztahy virtuálních sítí se aktuálně nepodporují. Tento požadavek se vztahuje na všechny testovací aplikace nebo nástroje pro testování testů a diagnostiku. Bez ohledu na to, kde je klientská aplikace hostovaná, skupin zabezpečení sítě nebo jiné síťové vrstvy musí být nakonfigurované tak, aby síťový provoz klienta měl přístup k instanci Azure cache pro Redis.
>

Po nakonfigurování požadavků na porty, jak je popsáno v předchozí části, můžete ověřit, že vaše mezipaměť funguje, pomocí následujících kroků:

- [Restartujte](cache-administration.md#reboot) všechny uzly mezipaměti. Pokud není dostupný žádný z požadovaných závislostí mezipaměti, jak je popsáno v části [požadavky na příchozí porty](cache-how-to-premium-vnet.md#inbound-port-requirements) a [požadavky na Odchozí porty](cache-how-to-premium-vnet.md#outbound-port-requirements), mezipaměť nebude možné úspěšně restartovat.
- Po restartování uzlů mezipaměti, jak je uvedeno v Azure Portal stav mezipaměti, můžete provést následující testy:
  - Otestujte koncový bod mezipaměti pomocí portu 6380 z počítače, který je ve stejné virtuální síti jako mezipaměť, pomocí [tcping](https://www.elifulkerson.com/projects/tcping.php). Například:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Pokud `tcping` Nástroj hlásí, že je port otevřený, je mezipaměť dostupná pro připojení od klientů ve virtuální síti.

  - Další možností testování je vytvořit klienta testovací mezipaměti (což může být jednoduchá Konzolová aplikace s použitím StackExchange. Redis), která se připojuje k mezipaměti a přidává a načítá některé položky z mezipaměti. Nainstalujte ukázkovou klientskou aplikaci do virtuálního počítače, který je ve stejné virtuální síti jako mezipaměť. Pak ji spusťte, chcete-li ověřit připojení k mezipaměti.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Proč se při pokusu o připojení ke službě Azure cache pro instanci Redis ve virtuální síti zobrazí chyba s oznámením, že vzdálený certifikát je neplatný?

Když se pokusíte připojit k instanci služby Azure cache pro Redis ve virtuální síti, zobrazí se chyba ověření certifikátu, jako je tato:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Příčinou může být to, že se připojujete k hostiteli prostřednictvím IP adresy. Doporučujeme použít název hostitele. Jinými slovy, použijte následující řetězec:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vyhněte se použití IP adresy, která je podobná následujícímu připojovacímu řetězci:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Pokud nemůžete přeložit název DNS, některé klientské knihovny obsahují možnosti konfigurace `sslHost` , například, které poskytuje klient stackexchange. Redis. Tato možnost umožňuje přepsat název hostitele, který se používá k ověření certifikátu. Například:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Můžu používat virtuální sítě se standardní nebo základní mezipamětí?

Virtuální sítě se dají používat jenom s mezipamětí úrovně Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Proč v některých podsítích selže vytváření mezipaměti Azure pro instanci Redis, ale ne jiné?

Pokud nasazujete mezipaměť Azure pro instanci Redis do virtuální sítě, musí být mezipaměť ve vyhrazené podsíti, která neobsahuje žádný jiný typ prostředku. Pokud se provede pokus o nasazení mezipaměti Azure cache pro instanci Redis do podsítě Správce prostředků virtuální sítě, která obsahuje další prostředky, například instance Azure Application Gateway a odchozí překlad adres (NAT), nasazení se obvykle nezdaří. Než budete moct vytvořit novou službu Azure cache pro instanci Redis, musíte odstranit stávající prostředky jiných typů.

Je také nutné mít v podsíti k dispozici dostatek IP adres.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jaké jsou požadavky na adresní prostor podsítě?

Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) .

Kromě IP adres, které používá infrastruktura virtuální sítě Azure, každá instance Azure cache for Redis v podsíti používá dvě IP adresy na cluster horizontálních oddílů a navíc další IP adresy pro další repliky, pokud existují. Pro nástroj pro vyrovnávání zatížení se používá jedna další IP adresa. Neclusterovaná mezipaměť se považuje za jednu horizontálních oddílů.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Fungují všechny funkce mezipaměti, když je mezipaměť hostována ve virtuální síti?

Pokud je mezipaměť součástí virtuální sítě, budou mít přístup k mezipaměti pouze klienti ve virtuální síti. V důsledku toho tyto funkce správy mezipaměti v tuto chvíli nefungují:

* **Konzola Redis**: vzhledem k tomu, že se konzola Redis spouští v místním prohlížeči, který je obvykle na vývojářském počítači, který není připojený k virtuální síti, se nemůže připojit ke svojí mezipaměti.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Použití ExpressRoute s Azure cache pro Redis

Zákazníci můžou připojit okruh [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) ke své infrastruktuře virtuální sítě. Tímto způsobem rozšiřuje místní síť do Azure.

Ve výchozím nastavení nově vytvořený okruh ExpressRoute neprovádí vynucené tunelování (inzerování výchozí trasy 0.0.0.0/0) ve virtuální síti. V důsledku toho je odchozí připojení k Internetu povoleno přímo z virtuální sítě. Klientské aplikace se můžou připojit k jiným koncovým bodům Azure, což zahrnuje mezipaměť Azure pro instanci Redis.

Běžnou zákaznickou konfigurací je použití vynuceného tunelového propojení (inzerování výchozí trasy), které vynucuje odchozí internetový provoz místo toho, aby se směroval místně. Tento tok přenosů přeruší připojení ke službě Azure cache pro Redis, pokud je odchozí přenosy blokované místně, takže instance Azure cache for Redis nemůže komunikovat s jejími závislostmi.

Řešením je definovat jednu nebo více uživatelsky definovaných tras (udr) v podsíti, která obsahuje službu Azure cache for Redis instance. UDR definuje trasy specifické pro podsíť, které se budou respektovat místo výchozí trasy.

Pokud je to možné, použijte následující konfiguraci:

* Konfigurace ExpressRoute inzeruje v síti 0.0.0.0/0 a ve výchozím nastavení vynutí tunelové propojení veškerý odchozí provoz v místním prostředí.
* UDR, který se použije pro podsíť, která obsahuje instanci Azure cache for Redis, definuje 0.0.0.0/0 s pracovní trasou pro provoz TCP/IP na veřejný Internet. Například nastaví typ dalšího segmentu směrování na *Internet*.

Kombinovaný efekt těchto kroků je, že UDR na úrovni podsítě má přednost před vynuceným tunelovým propojením ExpressRoute, které zajišťuje odchozí internetový přístup z mezipaměti Azure pro instanci Redis.

Připojení k Azure cache for Redis instance z místní aplikace pomocí ExpressRoute není Typický scénář použití z důvodu výkonu. Nejlepšího výkonu dosáhnete, když Azure cache pro klienty Redis musí být ve stejné oblasti jako Azure cache pro instanci Redis.

>[!IMPORTANT]
>Trasy definované v UDR *musí* být dostatečně specifické, aby měly přednost před všemi trasami inzerovanou konfigurací ExpressRoute. Následující příklad používá širokou škálu adres 0.0.0.0/0 a jako takový může být potenciálně omylem přepsán reklamními inzeráty, které používají více konkrétních rozsahů adres.

>[!WARNING]
>Mezipaměť Azure pro Redis se nepodporuje u konfigurací ExpressRoute, které *nesprávně přecházejí trasy z cesty veřejného partnerského vztahu na cestu privátního partnerského vztahu*. Konfigurace ExpressRoute s nakonfigurovaným veřejným partnerským vztahem obdrží od Microsoftu inzerci tras pro velkou sadu Microsoft Azure rozsahů IP adres. Pokud jsou tyto rozsahy adres nesprávně vzájemně inzerovány na cestě privátního partnerského vztahu, je výsledkem to, že všechny odchozí síťové pakety z mezipaměti Azure pro instanci instance Redis jsou nesprávně vynucené tunelování do místní síťové infrastruktury zákazníka. Tento tok sítě přeruší službu Azure cache pro Redis. Řešením tohoto problému je zastavit trasy mezi reklamními partnery z cesty veřejného partnerského vztahu k cestě soukromého partnerského vztahu.

Základní informace o udr jsou k dispozici ve [směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

Další informace o ExpressRoute najdete v [technickém přehledu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
