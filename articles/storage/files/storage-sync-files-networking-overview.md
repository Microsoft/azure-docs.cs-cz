---
title: Azure File Sync síťové požadavky | Microsoft Docs
description: Naučte se konfigurovat síť pro použití Azure File Sync k ukládání souborů do mezipaměti v místním prostředí.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629355"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync síťové požadavky
Ke sdílené složce Azure se můžete připojit dvěma způsoby:

- Přímý přístup ke sdílené složce přes protokoly SMB nebo REST. Tento vzor přístupu se primárně používá, když chcete eliminovat tolik místních serverů, kolik jich je možné.
- Vytvoření mezipaměti sdílené složky Azure na místním serveru (nebo na virtuálním počítači Azure) pomocí Azure File Sync a přístup k datům sdílené složky z místního serveru s vaším protokolem, který zvolíte (SMB, NFS, FTPS atd.) pro váš případ použití. Tento vzor přístupu je užitečný, protože kombinuje nejlépe místní výkon i cloudovou škálu a připojené služby bez serveru, jako je Azure Backup.

Tento článek se zaměřuje na to, jak nakonfigurovat sítě pro případ použití Azure File Sync k ukládání souborů do mezipaměti v místním prostředí, a ne přímo připojení sdílené složky Azure přes protokol SMB. Další informace o požadavcích na sítě pro nasazení souborů Azure najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](storage-files-networking-overview.md).

Konfigurace sítě pro Azure File Sync zahrnuje dva různé objekty Azure: službu synchronizace úložiště a účet úložiště Azure. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB. Služba synchronizace úložiště je konstrukce správy, která představuje registrované servery, což jsou souborové servery Windows s vytvořeným vztahem důvěryhodnosti s Azure File Sync a skupiny synchronizace, které definují topologii relace synchronizace. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Připojení souborového serveru Windows k Azure pomocí Azure File Sync 
Pokud chcete nastavit a používat soubory a Azure File Sync Azure pomocí místního souborového serveru Windows, nejsou za základní připojení k Internetu potřeba žádné speciální sítě pro Azure. Pokud chcete nasadit Azure File Sync, nainstalujte agenta Azure File Sync na souborový server Windows, který chcete synchronizovat s Azure. Agent Azure File Sync dosahuje synchronizace se sdílenou složkou Azure prostřednictvím dvou kanálů:

- Protokol souborů REST, což je protokol založený na protokolu HTTPS pro přístup ke sdílené složce Azure. Vzhledem k tomu, že protokol REST používá pro přenos dat standardní protokol HTTPS, musí být odchozí přístup pouze k portu 443. Azure File Sync nepoužívá protokol SMB k přenosu dat z místních serverů Windows do sdílené složky Azure.
- Protokol služby Azure File Sync Sync, což je protokol založený na protokolu HTTPS pro výměnu znalostí o synchronizaci, tj. informace o verzi souborů a složek ve vašem prostředí, mezi koncovými body ve vašem prostředí. Tento protokol se používá také k výměně metadat se soubory a složkami ve vašem prostředí, jako jsou například časová razítka a seznamy řízení přístupu (ACL). 

Vzhledem k tomu, že soubory Azure nabízí přímý přístup k protokolu SMB ve sdílených složkách Azure, zákazníci často chtějí, pokud potřebují nakonfigurovat speciální sítě pro připojení sdílených složek Azure s protokolem SMB, aby měl agent Azure File Sync přístup. To není nutné, ale také se nedoporučuje, ale kromě scénářů správců se nepovedlo kvůli nedostatku detekce rychlé změny u změn provedených přímo do sdílené složky Azure (změny se nedají zjistit déle než 24 hodin v závislosti na velikosti a počtu položek ve sdílené složce Azure). Pokud si přejete, aby se sdílená složka Azure používala přímo, tj. nepoužíváte Azure File Sync k ukládání do místní mezipaměti, můžete získat další informace o požadavcích na síť pro přímý přístup v článku [Přehled služby Azure Files Networking](storage-files-networking-overview.md).

I když Azure File Sync nevyžaduje žádnou zvláštní konfiguraci sítě, můžou někteří zákazníci chtít nakonfigurovat Pokročilá nastavení sítě, aby umožňovala následující scénáře:

- Spolupráce s konfigurací proxy server vaší organizace.
- Otevřete místní bránu firewall vaší organizace na soubory a Azure File Sync služby Azure.
- Tunelování souborů Azure a Azure File Sync přes ExpressRoute nebo připojení k síti VPN.

### <a name="configuring-proxy-servers"></a>Konfigurace proxy serverů
Mnoho organizací používá proxy server jako prostředník mezi prostředky v místní síti a prostředky mimo jejich síť, jako je například v Azure. Proxy servery jsou užitečné pro mnoho aplikací, jako je izolace sítě a zabezpečení, a monitorování a protokolování. Azure File Sync může plně spolupracovat s proxy server, ale musíte ručně nakonfigurovat nastavení koncových bodů proxy serveru pro vaše prostředí pomocí Azure File Sync. To se musí udělat přes PowerShell pomocí rutin Azure File Sync serveru. 

Další informace o tom, jak nakonfigurovat Azure File Sync pomocí proxy server, najdete v tématu [konfigurace Azure File Sync s proxy server](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurace bran firewall a značek služeb
Souborové servery můžete izolovat z většiny internetových umístění pro účely zabezpečení. Pokud chcete ve svém prostředí použít Azure File Sync, musíte bránu firewall upravit tak, aby se otevřela v rámci výběru služeb Azure. Můžete to provést tak, že načtěte rozsahy IP adres pro služby, které požadujete prostřednictvím mechanismu nazývaného [značky služby](../../virtual-network/service-tags-overview.md).

Azure File Sync vyžaduje rozsahy IP adres pro následující služby, které identifikovaly značky služeb:

| Služba | Popis | Značka služby |
|---------|-------------|-------------|
| Synchronizace souborů Azure | Služba Azure File Sync, jak je reprezentovaná objektem služby synchronizace úložiště, zodpovídá za základní aktivitu synchronizace dat mezi sdílenou složkou Azure a souborovým serverem Windows. | `StorageSyncService` |
| Soubory Azure | Všechna data synchronizovaná prostřednictvím Azure File Sync se ukládají ve sdílené složce Azure. Soubory změněné na souborových serverech Windows se replikují do sdílené složky Azure a soubory vrstvené na místním souborovém serveru se snadno stáhnou, když si ji uživatel požádá. | `Storage` |
| Azure Resource Manager | Azure Resource Manager je rozhraní pro správu Azure. Všechna volání správy, včetně úloh registrace serveru Azure File Sync a probíhající synchronizace serveru, probíhají prostřednictvím Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory nebo Azure AD obsahuje uživatelské objekty, které jsou potřebné k autorizaci registrace serveru vůči službě synchronizace úložiště, a instanční objekty vyžadované pro Azure File Sync oprávnění pro přístup k vašim cloudovým prostředkům. | `AzureActiveDirectory` |

Pokud používáte Azure File Sync v rámci Azure, a to i v případě, že se jedná o jinou oblast, můžete k povolení provozu do této služby použít název značky služby přímo ve skupině zabezpečení sítě. Další informace o tom, jak to provést, najdete v tématu [skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md). 

Pokud používáte Azure File Sync v místním prostředí, můžete k získání specifických rozsahů IP adres pro seznam povolených bran firewall použít rozhraní API tag služby. Existují dvě metody pro získání těchto informací:

- Aktuální seznam rozsahů IP adres pro všechny služby Azure, které podporují značky služby, se každý týden zveřejňuje na webu služby Stažení softwaru ve formě dokumentu JSON. Každý cloud Azure má svůj vlastní dokument JSON s rozsahy IP adres, které jsou relevantní pro tento Cloud:
    - [Veřejné Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure pro vládu USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Německo)](https://www.microsoft.com/download/details.aspx?id=57064)
- Rozhraní API zjišťování značek služby (Preview) umožňuje programové načtení aktuálního seznamu značek služeb. Ve verzi Preview může rozhraní API zjišťování značek služby vracet informace, které jsou méně aktuální než informace vrácené z dokumentů JSON publikovaných na webu Microsoft Download Center. Plochu rozhraní API můžete použít na základě preference automatizace:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Další informace o tom, jak používat rozhraní API značek služby k načtení adres vašich služeb, najdete v tématu [povolení Azure File Sync IP adres v seznamu povolených](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelování přenosů přes virtuální privátní síť nebo ExpressRoute
Některé organizace vyžadují komunikaci s Azure a přejít přes síťové tunelové propojení, jako je virtuální privátní privátní síť (VPN) nebo ExpressRoute, pro další vrstvu zabezpečení nebo pro zajištění komunikace s Azure za použití deterministické trasy. 

Když vytváříte síťové tunelové propojení mezi vaší místní sítí a Azure, vytváříte partnerský vztah k místní síti s jednou nebo více virtuálními sítěmi v Azure. [Virtuální síť nebo virtuální](../../virtual-network/virtual-networks-overview.md)síť se podobá tradiční síti, kterou provozujete místně. Jako je účet Azure Storage nebo virtuální počítač Azure, virtuální síť je prostředek Azure, který je nasazený ve skupině prostředků. 

Azure Files a Synchronizace souborů podporují následující mechanismy pro tunelování provozu mezi vašimi místními servery a Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brána VPN je konkrétní typ brány virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a alternativním umístěním (jako je místní) přes Internet. Azure VPN Gateway je prostředek Azure, který se dá nasadit ve skupině prostředků na straně účtu úložiště nebo jiných prostředků Azure. Vzhledem k tomu, že Azure File Sync má být použit s místním souborovým serverem Windows, obvykle používáte [síť VPN S2S (site-to-site)](../../vpn-gateway/design.md#s2smulti), ale je technicky možné použít [síť VPN typu Point-to-Site (P2S)](../../vpn-gateway/point-to-site-about.md). 

    Připojení VPN typu Site-to-Site (S2S) spojují vaši virtuální síť Azure a místní síť vaší organizace. Připojení VPN S2S umožňuje nakonfigurovat připojení VPN jednou, pro server VPN nebo zařízení hostované v síti vaší organizace, a ne pro každé klientské zařízení, které potřebuje mít přístup ke sdílené složce Azure. Pokud chcete zjednodušit nasazení připojení S2S VPN, přečtěte si téma [Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), která umožňuje vytvořit definovanou trasu mezi Azure a místní sítí, která neprojde internetem. Vzhledem k tomu, že ExpressRoute poskytuje vyhrazenou cestu mezi místním datacentrem a Azure, může být ExpressRoute užitečné v případě, že se výkon sítě zvažuje. ExpressRoute je také dobrou volbou v případě, že požadavky nebo předpisy vaší organizace vyžadují deterministické cesty k prostředkům v cloudu.

### <a name="private-endpoints"></a>Soukromé koncové body
Kromě výchozích veřejných koncových bodů a Synchronizace souborů poskytují účet úložiště a službu synchronizace úložiště, soubory Azure a Synchronizace souborů poskytují možnost mít jeden nebo více privátních koncových bodů na prostředek. Když vytvoříte privátní koncový bod pro prostředek Azure, získá privátní IP adresu z adresního prostoru virtuální sítě, podobně jako místní souborový server Windows má IP adresu v rámci vyhrazeného adresního prostoru vaší místní sítě. 

> [!Important]  
> Aby bylo možné používat privátní koncové body v prostředku služby synchronizace úložiště, je nutné použít agenta Azure File Sync verze 10,1 nebo vyšší. Verze agenta starší než 10,1 nepodporují privátní koncové body ve službě synchronizace úložiště. Všechny předchozí verze agenta podporují soukromé koncové body v prostředku účtu úložiště.

Individuální privátní koncový bod je přidružený ke konkrétní podsíti Azure Virtual Network. Účty úložiště a služby synchronizace úložiště můžou mít privátní koncové body ve více než jedné virtuální síti.

Použití privátních koncových bodů umožňuje:
- Připojte se bezpečně k prostředkům Azure z místních sítí pomocí připojení VPN nebo ExpressRoute s privátním partnerským vztahem.
- Zabezpečte svoje prostředky Azure tím, že zakážete veřejné koncové body pro soubory Azure a Synchronizace souborů. Při vytváření privátního koncového bodu se ve výchozím nastavení neblokuje připojení k veřejnému koncovému bodu.
- Zvyšte zabezpečení virtuální sítě tím, že povolíte blokování exfiltrace dat z virtuální sítě (a hranic partnerských vztahů).

Pokud chcete vytvořit privátní koncový bod, přečtěte si téma [Konfigurace privátních koncových bodů pro Azure File Sync](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privátní koncové body a DNS
Při vytváření privátního koncového bodu se ve výchozím nastavení vytvoří také privátní zóna DNS (nebo aktualizace stávající), která odpovídá `privatelink` subdoméně. Pro oblasti veřejného cloudu jsou tyto zóny DNS `privatelink.file.core.windows.net` pro soubory Azure a `privatelink.afs.azure.net` pro Azure File Sync.

> [!Note]  
> Tento článek používá příponu DNS účtu úložiště pro veřejné oblasti Azure, `core.windows.net` . Tento komentář platí také pro cloudy Azure, jako je Cloud pro státní správu USA a Azure Čína, stačí nahradit příslušné přípony vašeho prostředí. 

Při vytváření privátních koncových bodů pro účet úložiště a službu synchronizace úložiště vytvoříme pro ně záznamy v příslušných privátních zónách DNS. Také aktualizujeme veřejnou položku DNS tak, aby názvy běžných plně kvalifikovaných domén byly CNAME pro příslušný název privatelink. To umožňuje plně kvalifikované názvy domény ukazovat na IP adresy privátního koncového bodu, když je žadatel uvnitř virtuální sítě a odkazuje na IP adresy veřejného koncového bodu, když je žadatel mimo virtuální síť. 

Pro soubory Azure každý privátní koncový bod má jeden plně kvalifikovaný název domény, a to za vzorem `storageaccount.privatelink.file.core.windows.net` , který se mapuje na jednu privátní IP adresu privátního koncového bodu. U Azure File Sync každý privátní koncový bod má čtyři plně kvalifikované názvy domény pro čtyři různé koncové body, které Azure File Sync zveřejňuje: Management, Sync (primární), Sync (sekundární) a monitoring. Plně kvalifikované názvy domén pro tyto koncové body se obvykle řídí názvem služby synchronizace úložiště, pokud název neobsahuje jiné znaky než ASCII. Pokud je například název vaší služby synchronizace úložiště `mysyncservice` v oblasti západní USA 2, ekvivalentní koncové body by byly `mysyncservicemanagement.westus2.afs.azure.net` ,, `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` a `mysyncservicemonitoring.westus2.afs.azure.net` . Každý privátní koncový bod pro službu synchronizace úložiště bude obsahovat 4 jedinečné IP adresy. 

Vzhledem k tomu, že vaše privátní zóna DNS Azure je připojená k virtuální síti obsahující soukromý koncový bod, můžete sledovat konfiguraci DNS při volání `Resolve-DnsName` rutiny z PowerShellu ve virtuálním počítači Azure (střídavě `nslookup` v systému Windows a Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

V tomto příkladu se účet úložiště `storageaccount.file.core.windows.net` přeloží na soukromou IP adresu privátního koncového bodu, který se stane `192.168.0.4` .

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Pokud spustíte stejný příkaz z místního prostředí, uvidíte, že stejný název účtu úložiště se přeloží na veřejnou IP adresu účtu úložiště. `storageaccount.file.core.windows.net` je záznam CNAME pro `storageaccount.privatelink.file.core.windows.net` , který je zase záznam CNAME pro cluster úložiště Azure hostující účet úložiště:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

To odráží skutečnost, že soubory Azure a Azure File Sync můžou vystavovat oba veřejné koncové body a jeden nebo více privátních koncových bodů na prostředek. Aby se zajistilo, že plně kvalifikované názvy domén pro vaše prostředky překládají na privátních IP adresách privátních koncových bodů, musíte změnit konfiguraci na místních serverech DNS. To lze provést několika způsoby:

- Změnou souboru hostitelů v klientech zajistíte, aby plně kvalifikované názvy domén účtů úložiště a služeb synchronizace úložiště byly přeloženy na požadované privátní IP adresy. Toto nastavení se u produkčních prostředí důrazně nedoporučuje, protože tyto změny budete potřebovat udělat u každého klienta, který potřebuje přístup k vašim soukromým koncovým bodům. Změny vašich privátních koncových bodů a prostředků (odstranění, úpravy atd.) nebudou automaticky zpracovány.
- Vytváření zón DNS na místních serverech pro `privatelink.file.core.windows.net` a `privatelink.afs.azure.net` se záznamy pro prostředky Azure. To má výhodu, že klienti v místním prostředí budou moci automaticky přeložit prostředky Azure, aniž by museli konfigurovat každého klienta, ale toto řešení je podobně poměrně křehký na úpravu souboru hostitelů, protože změny se neprojeví. I když je toto řešení poměrně křehký, může být pro některá prostředí nejlepší volbou.
- Předejte `core.windows.net` `afs.azure.net` zóny a z vašich místních serverů DNS do privátní zóny DNS Azure. K privátnímu hostiteli DNS Azure se dá získat přístup prostřednictvím speciální IP adresy ( `168.63.129.16` ), která je dostupná jenom ve virtuálních sítích, které jsou propojené s privátní zónou DNS Azure. V případě tohoto omezení můžete spustit další servery DNS v rámci virtuální sítě, které budou předány `core.windows.net` a `afs.azure.net` do ekvivalentních privátních zón DNS Azure. Pro zjednodušení tohoto nastavení jsme zadali rutiny PowerShellu, které budou automaticky nasazovat servery DNS ve vaší virtuální síti Azure a nakonfigurovat je podle potřeby. Informace o tom, jak nastavit předávání DNS, najdete v tématu [Konfigurace DNS se soubory Azure](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Šifrování při přenosu
Připojení od agenta Azure File Sync k vaší sdílené složce Azure nebo službě synchronizace úložiště jsou vždycky zašifrovaná. I když účty úložiště Azure mají nastavení, které by se mělo zakázat při přenosu dat do služby Azure Files (a dalších služeb úložiště Azure, které jsou spravované mimo účet úložiště), zakázání tohoto nastavení nebude mít vliv na šifrování Azure File Sync při komunikaci se soubory Azure. Ve výchozím nastavení mají všechny účty úložiště Azure povolený šifrování při přenosu. 

Další informace o šifrování v přenosu najdete v tématu [vyžadování zabezpečeného přenosu ve službě Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Viz také
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)