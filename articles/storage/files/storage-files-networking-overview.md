---
title: Požadavky na síť pro Azure Files | Microsoft Docs
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7164c3dd5c98544f3cb2944cb33cfd0e9703e36d
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563331"
---
# <a name="azure-files-networking-considerations"></a>Požadavky na síť pro Azure Files 
Ke sdílené složce Azure se můžete připojit dvěma způsoby:

- Přímý přístup ke sdílené složce přes protokol SMB (Server Message Block), systém souborů NFS (Network File System) (Preview) nebo protokoly REST. Tento vzor přístupu se primárně používá, když chcete eliminovat tolik místních serverů, kolik jich je možné.
- Vytvoření mezipaměti sdílené složky Azure na místním serveru (nebo na virtuálním počítači Azure) pomocí Azure File Sync a přístup k datům sdílené složky z místního serveru s vaším protokolem, který zvolíte (SMB, NFS, FTPS atd.) pro váš případ použití. Tento vzor přístupu je užitečný, protože kombinuje nejlépe místní výkon i cloudovou škálu a připojené služby bez serveru, jako je Azure Backup.

Tento článek se zaměřuje na konfiguraci sítě pro případy, kdy váš případ použití volá pro přístup ke sdílené složce Azure přímo místo použití Azure File Sync. Další informace o požadavcích na používání sítě pro nasazení Azure File Sync najdete v tématu [informace o Azure File Syncch sítích](storage-sync-files-networking-overview.md).

Konfigurace sítě pro sdílené složky Azure se provádí v účtu úložiště Azure. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB. Účty úložiště zpřístupňují více nastavení, která vám pomůžou zabezpečit síťový přístup ke sdíleným složkám: koncové body sítě, nastavení brány firewall účtu úložiště a šifrování při přenosu. 

Před čtením tohoto koncepčního Průvodce doporučujeme, abyste načetli [plánování pro nasazení souborů Azure](storage-files-planning.md) .

## <a name="accessing-your-azure-file-shares"></a>Přístup ke sdíleným složkám Azure
Když nasadíte sdílenou složku Azure v rámci účtu úložiště, vaše sdílená složka je hned dostupná přes Veřejný koncový bod účtu úložiště. To znamená, že ověřené požadavky, jako jsou požadavky autorizované identitou přihlašování uživatele, můžou pocházet z interního nebo mimo Azure. 

V mnoha zákaznických prostředích se počáteční připojení sdílené složky Azure na místní pracovní stanici nezdaří, i když připojení z virtuálních počítačů Azure bude úspěšné. Důvodem je to, že mnoho organizací a poskytovatelů internetových služeb (ISP) blokuje port, který protokol SMB používá ke komunikaci, port 445. Sdílené složky NFS nemají tento problém. Tento postup vychází z bezpečnostních pokynů pro starší verze a zastaralé verze protokolu SMB. I když je SMB 3,0 protokolem bezpečným pro Internet, starší verze SMB, zejména SMB 1,0, nejsou. Ke sdíleným složkám Azure se dá externě přistupovat prostřednictvím protokolu SMB 3,0 a protokolu REST (což je také internetový protokol) prostřednictvím veřejného koncového bodu.

Vzhledem k tomu, že nejjednodušší způsob, jak získat přístup ke sdílené složce Azure SMB z místního prostředí, je otevřít místní síť pro port 445, Microsoft doporučuje následující kroky, aby z vašeho prostředí odebrala protokol SMB 1,0:

1. Zajistěte, aby byl na zařízeních vaší organizace odebrán nebo zakázán protokol SMB 1,0. Všechny aktuálně podporované verze systému Windows a Windows Server podporují odebrání nebo zakázání protokolu SMB 1,0 a počínaje verzí Windows 10 verze 1709 není ve výchozím nastavení ve Windows nainstalovaná služba SMB 1,0. Další informace o tom, jak zakázat SMB 1,0, najdete na stránkách pro konkrétní operační systém:
    - [Zabezpečení Windows a Windows Serveru](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpečení systému Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Zajistěte, aby žádné produkty v rámci vaší organizace nevyžadovaly SMB 1,0 a odebraly ty, které mají. Udržujeme [SMB1 produkt](https://aka.ms/stillneedssmb1), který obsahuje všechny první a třetí výrobce známé společnosti Microsoft, aby vyžadovaly protokol SMB 1,0. 
1. Volitelné Použijte bránu firewall od jiného výrobce s místní sítí vaší organizace, abyste zabránili provozu protokolu SMB 1,0 opustit hranici vaší organizace.

Pokud vaše organizace vyžaduje, aby byl port 445 zablokovaný na základě zásad nebo nařízení, nebo pokud vaše organizace vyžaduje, aby se v Azure použila deterministické cesta, můžete k tunelování provozu do sdílených složek Azure použít Azure VPN Gateway nebo ExpressRoute. Sdílené složky NFS nevyžadují žádný z těchto požadavků, protože nepotřebují port 445.

> [!Important]  
> I když se rozhodnete použít pro přístup ke sdíleným složkám Azure alternativní metodu, společnost Microsoft stále doporučuje odebrat z vašeho prostředí protokol SMB 1,0.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelování přenosů přes virtuální privátní síť nebo ExpressRoute
Když vytváříte síťové tunelové propojení mezi vaší místní sítí a Azure, vytváříte partnerský vztah k místní síti s jednou nebo více virtuálními sítěmi v Azure. [Virtuální síť nebo virtuální](../../virtual-network/virtual-networks-overview.md)síť se podobá tradiční síti, kterou provozujete místně. Jako je účet Azure Storage nebo virtuální počítač Azure, virtuální síť je prostředek Azure, který je nasazený ve skupině prostředků. 

Soubory Azure podporují pro tunelování provozu mezi místními pracovními stanicemi a servery a sdílenými složkami souborů Azure SMB/NFS tyto mechanismy:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brána VPN je konkrétní typ brány virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a alternativním umístěním (jako je místní) přes Internet. Azure VPN Gateway je prostředek Azure, který se dá nasadit ve skupině prostředků na straně účtu úložiště nebo jiných prostředků Azure. Brány VPN zpřístupňují dva různé typy připojení:
    - Připojení brány [VPN typu Point-to-Site (P2S)](../../vpn-gateway/point-to-site-about.md) , což jsou připojení VPN mezi Azure a jednotlivými klienty. Toto řešení je primárně užitečné pro zařízení, která nejsou součástí místní sítě vaší organizace, jako jsou například dojíždění, kteří chtějí mít možnost připojit svou sdílenou složku Azure z domova, z kavárny nebo hotelu na cestách. Pokud chcete použít připojení VPN P2S se soubory Azure, bude nutné nakonfigurovat připojení VPN P2S pro každého klienta, který se chce připojit. Pokud chcete zjednodušit nasazení připojení k síti VPN P2S, přečtěte si téma [Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md) a [Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md).
    - Síť [VPN typu Site-to-Site (S2S)](../../vpn-gateway/design.md#s2smulti), která je připojení VPN mezi Azure a sítí vaší organizace. Připojení VPN S2S umožňuje nakonfigurovat připojení VPN jednou, pro server VPN nebo zařízení hostované v síti vaší organizace, a ne pro každé klientské zařízení, které potřebuje mít přístup ke sdílené složce Azure. Pokud chcete zjednodušit nasazení připojení S2S VPN, přečtěte si téma [Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), která umožňuje vytvořit definovanou trasu mezi Azure a místní sítí, která neprojde internetem. Vzhledem k tomu, že ExpressRoute poskytuje vyhrazenou cestu mezi místním datacentrem a Azure, může být ExpressRoute užitečné v případě, že se výkon sítě zvažuje. ExpressRoute je také dobrou volbou v případě, že požadavky nebo předpisy vaší organizace vyžadují deterministické cesty k prostředkům v cloudu.

Bez ohledu na to, kterou metodu tunelování používáte pro přístup ke sdíleným složkám Azure, potřebujete mechanismus, který zajistí, aby přenos dat do vašeho účtu úložiště procházel tunelovým propojením, a ne s běžným připojením k Internetu. Je technicky možné směrovat na veřejný koncový bod účtu úložiště, ale to vyžaduje, aby byly všechny IP adresy pro clustery Azure Storage v určité oblasti pevně zakódovaných, protože účty úložiště můžete kdykoli přesouvat mezi clustery úložiště. To také vyžaduje nepřetržitou aktualizaci mapování IP adres, protože nové clustery se přidávají kdykoli.

Místo hardwarového zakódování IP adres účtů úložiště do pravidel směrování sítě VPN doporučujeme použít soukromé koncové body, které přidávají účtu úložiště IP adresu z adresního prostoru virtuální sítě Azure. Vzhledem k tomu, že vytvoření tunelu do Azure vytvoří partnerský vztah mezi vaší místní sítí a jednou nebo více virtuálními sítěmi, umožní vám to trvalý způsob směrování.

### <a name="private-endpoints"></a>Soukromé koncové body
Kromě výchozího veřejného koncového bodu pro účet úložiště poskytuje Azure Files možnost mít jeden nebo více privátních koncových bodů. Soukromý koncový bod je koncový bod, který je přístupný jenom v rámci virtuální sítě Azure. Když vytvoříte privátní koncový bod pro svůj účet úložiště, váš účet úložiště načte privátní IP adresu z adresního prostoru virtuální sítě, podobně jako místní souborový server nebo zařízení NAS obdrží IP adresu v rámci vyhrazeného adresního prostoru místní sítě. 

Individuální privátní koncový bod je přidružený ke konkrétní podsíti Azure Virtual Network. Účet úložiště může mít privátní koncové body ve více než jedné virtuální síti.

Použití privátních koncových bodů se soubory Azure vám umožní:
- Připojte se bezpečně ke sdíleným složkám Azure z místních sítí pomocí připojení VPN nebo ExpressRoute s privátním partnerským vztahem.
- Zabezpečte sdílené složky Azure tak, že nakonfigurujete bránu firewall účtu úložiště pro blokování všech připojení ve veřejném koncovém bodu. Při vytváření privátního koncového bodu se ve výchozím nastavení neblokuje připojení k veřejnému koncovému bodu.
- Zvyšte zabezpečení virtuální sítě tím, že povolíte blokování exfiltrace dat z virtuální sítě (a hranic partnerských vztahů).

Pokud chcete vytvořit privátní koncový bod, přečtěte si téma [Konfigurace privátních koncových bodů pro soubory Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privátní koncové body a DNS
Při vytváření privátního koncového bodu se ve výchozím nastavení vytvoří také privátní zóna DNS (nebo aktualizace stávající), která odpovídá `privatelink` subdoméně. Výhradně řečeno, vytvoření privátní zóny DNS není vyžadováno pro použití privátního koncového bodu pro váš účet úložiště, ale při připojování sdílené složky Azure s objektem zabezpečení služby Active Directory nebo přístupu z rozhraní REST API se důrazně doporučuje obecně a explicitně vyžadovat.

> [!Note]  
> Tento článek používá příponu DNS účtu úložiště pro veřejné oblasti Azure, `core.windows.net` . Tento komentář platí také pro cloudy Azure, jako je Cloud pro státní správu USA a Azure Čína, stačí nahradit příslušné přípony vašeho prostředí. 

V privátní zóně DNS vytvoříme záznam `storageaccount.privatelink.file.core.windows.net` a a záznam CNAME pro běžný název účtu úložiště, který následuje za vzorem `storageaccount.file.core.windows.net` . Vzhledem k tomu, že vaše privátní zóna DNS Azure je připojená k virtuální síti obsahující soukromý koncový bod, můžete sledovat konfiguraci DNS při volání `Resolve-DnsName` rutiny z PowerShellu ve virtuálním počítači Azure (střídavě `nslookup` v systému Windows a Linux):

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

To odráží skutečnost, že účet úložiště může vystavit veřejný koncový bod a jeden nebo několik privátních koncových bodů. Aby se zajistilo, že se název účtu úložiště přeloží na privátní IP adresu privátního koncového bodu, musíte změnit konfiguraci na místních serverech DNS. To lze provést několika způsoby:

- Změna souboru hostitelů ve vašich klientských počítačích tak, aby se `storageaccount.file.core.windows.net` přeložila na privátní IP adresu požadovaného privátního koncového bodu. U produkčních prostředí se důrazně nedoporučuje, protože je potřeba provést tyto změny každému klientovi, který chce připojit sdílené složky Azure a změnit účet úložiště, nebo privátní koncový bod nebude automaticky zpracován.
- Vytvoření záznamu A pro na `storageaccount.file.core.windows.net` místních serverech DNS. To má výhodu, že klienti v místním prostředí budou moci automaticky přeložit účet úložiště, aniž by museli konfigurovat každého klienta, ale toto řešení je podobně poměrně křehký na úpravu souboru hostitelů, protože se neprojeví změny. I když je toto řešení poměrně křehký, může být pro některá prostředí nejlepší volbou.
- Dodejte `core.windows.net` zónu z místních serverů DNS do privátní zóny DNS Azure. K privátnímu hostiteli DNS Azure se dá získat přístup prostřednictvím speciální IP adresy ( `168.63.129.16` ), která je dostupná jenom ve virtuálních sítích, které jsou propojené s privátní zónou DNS Azure. Chcete-li toto omezení vyřešit, můžete spustit další servery DNS v rámci virtuální sítě, které budou předány `core.windows.net` do privátní zóny DNS Azure. Pro zjednodušení tohoto nastavení jsme zadali rutiny PowerShellu, které budou automaticky nasazovat servery DNS ve vaší virtuální síti Azure a nakonfigurovat je podle potřeby. Informace o tom, jak nastavit předávání DNS, najdete v tématu [Konfigurace DNS se soubory Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Nastavení brány firewall účtu úložiště
Brána firewall je zásada sítě, která řídí, které požadavky mají povolený přístup k veřejnému koncovému bodu pro účet úložiště. Pomocí brány firewall účtu úložiště můžete omezit přístup k veřejnému koncovému bodu účtu úložiště na určité IP adresy nebo rozsahy nebo na virtuální síť. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti na jednu nebo více virtuálních sítí. 

Existují dva způsoby, jak omezit přístup k účtu úložiště na virtuální síť:
- Vytvořte jeden nebo několik privátních koncových bodů pro účet úložiště a omezte veškerý přístup k veřejnému koncovému bodu. Tím se zajistí, že budou mít přístup ke sdíleným složkám Azure v rámci účtu úložiště jenom přenosy pocházející z požadovaných virtuálních sítí.
- Omezte veřejný koncový bod na jednu nebo více virtuálních sítí. To funguje pomocí funkce virtuální sítě s názvem *koncové body služby*. Když omezíte provoz na účet úložiště prostřednictvím koncového bodu služby, stále přistupujete k účtu úložiště prostřednictvím veřejné IP adresy.

> [!NOTE]
> Sdílené složky NFS nemají přístup k veřejnému koncovému bodu účtu úložiště přes veřejnou IP adresu, můžou k veřejnému koncovému bodu účtu úložiště přistupovat jenom pomocí virtuálních sítí. Sdílené složky systému souborů NFS můžou k účtu úložiště přistupovat taky pomocí privátních koncových bodů.

Další informace o tom, jak nakonfigurovat bránu firewall účtu úložiště, najdete v tématu [Konfigurace bran firewall služby Azure Storage a virtuálních sítí](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Šifrování během přenosu

> [!IMPORTANT]
> Tato část popisuje šifrování v podrobnostech o přenosu pro sdílené složky SMB. Podrobnosti o šifrování při přenosu s sdílenými složkami NFS najdete v tématu [zabezpečení](storage-files-compare-protocols.md#security).

Ve výchozím nastavení mají všechny účty úložiště Azure povolený šifrování při přenosu. To znamená, že když připojíte sdílenou složku přes protokol SMB nebo k ní přistupujete prostřednictvím protokolu REST (například prostřednictvím Azure Portal, PowerShellu nebo rozhraní Azure SDK), budou soubory Azure umožňovat připojení jenom v případě, že se jedná o protokol SMB 3.0 + s šifrováním nebo HTTPS. Klienti, kteří nepodporují protokol SMB 3,0 nebo klienti, kteří podporují protokol SMB 3,0, ale nemají šifrování protokolu SMB, nebudou moci připojit sdílenou složku Azure, pokud je zapnuté šifrování při přenosu. Další informace o tom, které operační systémy podporují protokol SMB 3,0 se šifrováním, najdete v naší podrobné dokumentaci pro [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md). Všechny aktuální verze PowerShellu, CLI a sad SDK podporují protokol HTTPS.  

Šifrování můžete zakázat při přenosu pro účet služby Azure Storage. Když je šifrování zakázané, budou soubory Azure taky umožňovat protokol SMB 2,1, SMB 3,0 bez šifrování a nešifrované volání rozhraní REST API přes HTTP. Hlavním důvodem Zakázání šifrování při přenosu je podpora starší verze aplikace, kterou je třeba spustit ve starším operačním systému, jako je Windows Server 2008 R2 nebo starší distribuce systému Linux. Soubory Azure v rámci stejné oblasti Azure jako sdílená složka Azure povolují jenom připojení SMB 2,1. Klient SMB 2,1 mimo oblast Azure sdílené složky Azure, například v místním prostředí nebo v jiné oblasti Azure, nebude mít přístup ke sdílené složce souborů.

Další informace o šifrování v přenosu najdete v tématu [vyžadování zabezpečeného přenosu ve službě Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Viz také
- [Přehled služby Azure Files](storage-files-introduction.md)
- [Plánování nasazení Azure Files](storage-files-planning.md)