---
title: Důležité informace o vytváření sítí Azure Files | Dokumenty společnosti Microsoft
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067153"
---
# <a name="azure-files-networking-considerations"></a>Důležité informace o vytváření sítí Azure Files 
Ke sdílené složce Azure se můžete připojit dvěma způsoby:

- Přístup ke sdílené složce přímo prostřednictvím protokolů SMB nebo FileREST. Tento přístup vzor se používá především při odstranění co nejvíce místních serverů, jak je to možné.
- Vytvoření mezipaměti sdílené složky Azure na místním serveru pomocí Azure File Sync a přístup k datům sdílené složky z místního serveru s protokolem podle vašeho výběru (SMB, NFS, FTPS atd.) pro váš případ použití. Tento přístupový vzor je praktický, protože kombinuje to nejlepší z místního výkonu a cloudového škálování a připojitelných služeb bez serveru, jako je azure backup.

Tento článek se zaměřuje na to, jak nakonfigurovat sítě pro případ použití volá pro přístup ke sdílené složce Azure přímo než pomocí Azure File Sync. Další informace o aspektech sítě pro nasazení Synchronizace souborů Azure najdete v [tématu konfigurace nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

Konfigurace sítě pro sdílené složky Azure se provádí na účtu úložiště Azure. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek, stejně jako další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty. Účty úložiště zveřejňují několik nastavení, která pomáhají zabezpečit síťový přístup ke sdíleným položkám souborů: koncové body sítě, nastavení brány firewall účtu úložiště a šifrování při přenosu. 

Doporučujeme si [přečíst plánování pro nasazení souborů Azure](storage-files-planning.md) před přečtením tohoto koncepčního průvodce.

## <a name="accessing-your-azure-file-shares"></a>Přístup ke sdíleným položkům azure souborů
Když nasadíte sdílenou složku Azure v rámci účtu úložiště, vaše sdílená složka je okamžitě přístupná prostřednictvím veřejného koncového bodu účtu úložiště. To znamená, že ověřené požadavky, jako jsou požadavky autorizované přihlašovací identitou uživatele, mohou bezpečně pocházet z Azure nebo mimo něj. 

V mnoha zákaznických prostředích se nezdaří počáteční připojení sdílené složky Azure na vaší místní pracovní stanici, i když připojení z virtuálních počítačů Azure jsou úspěšná. Důvodem je to, že mnoho organizací a poskytovatelů internetových služeb (ISP) blokuje port, který smb používá ke komunikaci, port 445. Tento postup pochází z pokynů k zabezpečení starších a zastaralé verze protokolu SMB. Přestože SMB 3.0 je internet-bezpečný protokol, starší verze SMB, zejména SMB 1.0 nejsou. Sdílené složky Azure mohou být přístupné jenom prostřednictvím protokolu SMB 3.0 a protokolu FileREST (což je také protokol bezpečného připojení k internetu) prostřednictvím veřejného koncového bodu.

Vzhledem k tomu, že nejjednodušší způsob, jak získat přístup ke sdílené složce Azure z místního prostředí, je otevřít místní síť na port u portu 445, společnost Microsoft doporučuje následující kroky k odebrání smb 1.0 z vašeho prostředí:

1. Ujistěte se, že smb 1.0 je odebrána nebo zakázána na zařízeních vaší organizace. Všechny aktuálně podporované verze systému Windows a Windows Server podporují odebrání nebo zakázání protokolu SMB 1.0 a počínaje systémem Windows 10 verze 1709 není ve výchozím nastavení v systému Windows nainstalován systém SMB 1.0. Další informace o zakázání protokolu SMB 1.0 najdete na našich stránkách specifických pro operační režim:
    - [Zabezpečení Windows a Windows Serveru](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpečení Linuxu](storage-how-to-use-files-linux.md#securing-linux)
2. Ujistěte se, že žádné produkty ve vaší organizaci nevyžadují smb 1.0 a odeberte ty, které to dělají. Udržujeme [službu SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), která obsahuje všechny první produkty a produkty třetích stran, o nichž je společnosti Microsoft známo, že vyžadují smb 1.0. 
3. (Nepovinné) Pomocí brány firewall jiného výrobce v místní síti vaší organizace zabráníte přenosům ve smb 1.0 v opuštění hranice vaší organizace.

Pokud vaše organizace vyžaduje, aby byl port 445 blokován podle zásad nebo nařízení, nebo vaše organizace vyžaduje, aby provoz v Azure sledoval deterministickou cestu, můžete použít Azure VPN Gateway nebo ExpressRoute k tunelovému provozu do sdílených složek Azure.

> [!Important]  
> I v případě, že se rozhodnete použít alternativní metodu pro přístup ke sdíleným položkám souborů Azure, Microsoft stále doporučuje odebrat SMB 1.0 z vašeho prostředí.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelové propojení přenosů přes virtuální privátní síť nebo ExpressRoute
Když vytvoříte síťový tunel mezi místní sítí a Azure, budete partnerské sítě s jedním nebo více virtuálních sítí v Azure. [Virtuální síť](../../virtual-network/virtual-networks-overview.md)nebo virtuální síť se podobá tradiční síti, kterou byste provozovali místně. Stejně jako účet úložiště Azure nebo virtuální počítač Azure, virtuální síť je prostředek Azure, který se nasadí ve skupině prostředků. 

Azure Files podporuje následující mechanismy tunelového propojení provozu mezi místními pracovními stanicemi a servery a Azure:

- [Brána Azure VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brána VPN je specifický typ brány virtuální sítě, který se používá k odesílání šifrovaného provozu mezi virtuální sítí Azure a alternativním umístěním (například místním) přes internet. Brána Azure VPN je prostředek Azure, který se dá nasadit ve skupině prostředků společně s účtem úložiště nebo jinými prostředky Azure. Brány VPN zveřejňují dva různé typy připojení:
    - Připojení brány VPN z [bodu na místo (P2S),](../../vpn-gateway/point-to-site-about.md) což jsou připojení VPN mezi Azure a jednotlivým klientem. Toto řešení je užitečné především pro zařízení, která nejsou součástí místní sítě vaší organizace, jako jsou pracovníci pracující na dálku, kteří chtějí mít možnost připojit sdílenou složku Azure z domova, kavárnu nebo hotel na cestách. Chcete-li použít připojení P2S VPN se soubory Azure, připojení P2S VPN bude muset být nakonfigurováno pro každého klienta, který se chce připojit. Informace o zjednodušení nasazení připojení Vpn P2S najdete [v tématu Konfigurace sítě VPN z bodu na webu (P2S)](storage-files-configure-p2s-vpn-windows.md) v systému Windows pro použití se soubory Azure a Konfigurace sítě VPN [point-to-site (P2S) v Linuxu pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md).
    - [VPN site-to-site (S2S),](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)což jsou připojení VPN mezi Azure a sítí vaší organizace. Připojení Vpn S2S umožňuje jednou nakonfigurovat připojení VPN pro server VPN nebo zařízení hostované v síti vaší organizace, nikoli pro každé klientské zařízení, které potřebuje přístup ke sdílené složce Azure. Informace o zjednodušení nasazení připojení S2S VPN najdete [v tématu Konfigurace sítě VPN site-to-site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), který umožňuje vytvořit definovanou trasu mezi Azure a místní sítí, která neprochází internetem. Vzhledem k tomu, že ExpressRoute poskytuje vyhrazenou cestu mezi místním datovým centrem a Azure, expressroute může být užitečné, když výkon sítě je důležité. ExpressRoute je také dobrou volbou, když zásady nebo regulační požadavky vaší organizace vyžadují deterministickou cestu k vašim prostředkům v cloudu.

Bez ohledu na to, jakou metodu tunelového propojení používáte pro přístup ke sdíleným položkám souborů Azure, potřebujete mechanismus, který zajistí, že provoz na vašem účtu úložiště půjde přes tunel, nikoli přes běžné připojení k internetu. Technicky je možné směrovat do veřejného koncového bodu účtu úložiště, ale to vyžaduje pevné kódování všech IP adres pro clustery úložiště Azure v oblasti, protože účty úložiště mohou být kdykoli přesunuty mezi clustery úložiště. To také vyžaduje neustálé aktualizace mapování IP adres, protože nové clustery jsou přidávány po celou dobu.

Místo pevného kódování IP adresy vašich účtů úložiště do pravidel směrování VPN doporučujeme používat privátní koncové body, které poskytují vašemu účtu úložiště IP adresu z adresního prostoru virtuální sítě Azure. Vzhledem k tomu, že vytvoření tunelového propojení do Azure vytváří partnerský vztah mezi místní sítí a jednou nebo více virtuální chůzí, to umožňuje správné směrování trvalým způsobem.

### <a name="private-endpoints"></a>Soukromé koncové body
Kromě výchozího veřejného koncového bodu pro účet úložiště poskytuje Soubory Azure možnost mít jeden nebo více privátní koncové body. Privátní koncový bod je koncový bod, který je přístupný jenom v rámci virtuální sítě Azure. Když vytvoříte soukromý koncový bod pro váš účet úložiště, váš účet úložiště získá privátní IP adresu z adresního prostoru vaší virtuální sítě, podobně jako jak místní souborový server nebo zařízení NAS obdrží IP adresu v rámci vyhrazené adresy místní sítě. 

Individuální privátní koncový bod je přidružený k podsíti konkrétní virtuální sítě Azure. Účet úložiště může mít privátní koncové body ve více než jedné virtuální síti.

Použití privátní koncové body s Azure Files umožňuje:
- Bezpečně se připojujte ke sdíleným spodám souborů Azure z místních sítí pomocí připojení VPN nebo ExpressRoute s privátním partnerského vztahu.
- Zabezpečte sdílené složky Azure konfigurací brány firewall účtu úložiště tak, aby blokovala všechna připojení ve veřejném koncovém bodě. Ve výchozím nastavení vytvoření privátníkoncový koncový bod neblokuje připojení k veřejnému koncovému bodu.
- Zvyšte zabezpečení virtuální sítě tím, že vám umožní blokovat exfiltraci dat z virtuální sítě (a partnerských hranic).

Pokud chcete vytvořit soukromý koncový bod, [přečtěte si část Konfigurace privátních koncových bodů pro soubory Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Soukromé koncové body a DNS
Když vytvoříte soukromý koncový bod, ve výchozím nastavení také vytvoříme (nebo `privatelink` aktualizujeme stávající) soukromou zónu DNS odpovídající subdoméně. Přísně vzato, vytvoření privátní zóny DNS není nutné používat soukromý koncový bod pro váš účet úložiště, ale je vysoce doporučeno obecně a explicitně vyžadováno při připojování sdílené složky Azure s uživatelským objektem služby Active Directory nebo při přístupu k uživateli služby Active Directory z rozhraní API FileREST.

> [!Note]  
> Tento článek používá příponu DNS účtu úložiště `core.windows.net`pro oblasti Azure Public . Tento komentář se vztahuje také na cloudy Azure Sovereign, jako je cloud Azure US Government a cloud Azure China – stačí nahradit příslušné přípony pro vaše prostředí. 

Ve vaší privátní zóně `storageaccount.privatelink.file.core.windows.net` DNS vytvoříme záznam A a záznam CNAME pro běžný `storageaccount.file.core.windows.net`název účtu úložiště, který následuje podle vzoru . Vzhledem k tomu, že vaše privátní zóna DNS Azure je připojena `Resolve-DnsName` k virtuální síti obsahující privátní `nslookup` koncový bod, můžete sledovat konfiguraci DNS při volání rutiny z Prostředí PowerShell v virtuálním počítači Azure (střídavě ve Windows a Linuxu):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

V tomto příkladu `storageaccount.file.core.windows.net` účet úložiště překládá na privátní IP `192.168.0.4`adresu privátní koncový bod, který se stane být .

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

Pokud spustíte stejný příkaz z místního prostředí, uvidíte, že stejný název účtu úložiště se překládá na veřejnou IP adresu účtu úložiště. `storageaccount.file.core.windows.net` je záznam CNAME `storageaccount.privatelink.file.core.windows.net`pro , což je zase záznam CNAME pro cluster úložiště Azure hostující účet úložiště:

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

To odráží skutečnost, že účet úložiště může vystavit veřejný koncový bod a jeden nebo více soukromých koncových bodů. Chcete-li zajistit, aby se název účtu úložiště překládal na privátní IP adresu privátního koncového bodu, je nutné změnit konfiguraci na místních serverech DNS. Toho lze dosáhnout několika způsoby:

- Úprava souboru hosts na `storageaccount.file.core.windows.net` vašich klientů, aby se vyřešit na požadovanou privátní koncový bod privátní IP adresu. To se důrazně nedoporučuje pro produkční prostředí, protože budete muset provést tyto změny pro každého klienta, který chce připojit sdílené složky Azure a změny účtu úložiště nebo soukromého koncového bodu nebudou automaticky zpracovány.
- Vytvoření záznamu A `storageaccount.file.core.windows.net` na místních serverech DNS. To má tu výhodu, že klienti ve vašem místním prostředí budou moci automaticky vyřešit účet úložiště bez nutnosti konfigurace každého klienta, ale toto řešení je podobně křehké jako úprava souboru hosts, protože změny nejsou Odráží. Přestože toto řešení je křehký, může být nejlepší volbou pro některá prostředí.
- Přepošlete zónu `core.windows.net` z místních serverů DNS do privátní zóny DNS Azure. Privátního hostitele DNS Azure lze`168.63.129.16`dosáhnout prostřednictvím speciální IP adresy ( ), která je přístupná jenom uvnitř virtuálních sítí, které jsou propojené se zónou Azure privátní DNS. Chcete-li toto omezení vyřešit, můžete spustit další `core.windows.net` servery DNS v rámci vaší virtuální sítě, které budou předávat do zóny Azure privátní DNS. Pro zjednodušení tohoto nastavení jsme poskytli rutiny prostředí PowerShell, které automaticky nasadí servery DNS ve vaší virtuální síti Azure a nakonfigurují je podle potřeby. Informace o tom, jak nastavit předávání DNS, najdete [v tématu Konfigurace DNS pomocí souborů Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Nastavení brány firewall účtu úložiště
Brána firewall je zásada sítě, která řídí, které požadavky mají povolen přístup k veřejnému koncovému bodu pro účet úložiště. Pomocí brány firewall účtu úložiště můžete omezit přístup k veřejnému koncovému bodu účtu úložiště na určité IP adresy nebo rozsahy nebo na virtuální síť. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti do jedné nebo více virtuálních sítí. 

Existují dva přístupy k omezení přístupu k účtu úložiště na virtuální síť:
- Vytvořte jeden nebo více soukromých koncových bodů pro účet úložiště a omezit veškerý přístup k veřejnému koncovému bodu. Tím zajistíte, že pouze provoz pocházející z v rámci požadovaných virtuálních sítí přístup ke sdíleným položkám souborů Azure v rámci účtu úložiště.
- Omezte veřejný koncový bod na jednu nebo více virtuálních sítí. To funguje pomocí funkce virtuální sítě nazývané *koncové body služby*. Když omezíte provoz na účet úložiště prostřednictvím koncového bodu služby, stále přistupujete k účtu úložiště prostřednictvím veřejné IP adresy.

Další informace o konfiguraci brány firewall účtu úložiště najdete v [tématu Konfigurace bran firewall úložiště Azure a virtuálních sítí](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Šifrování během přenosu
Ve výchozím nastavení mají všechny účty úložiště Azure povolené šifrování při přenosu. To znamená, že když připojíte sdílenou složku přes SMB nebo k ní přistupujete přes protokol FileREST (například prostřednictvím portálu Azure, PowerShellu/CLI nebo sad Azure SDK), soubory Azure povolí připojení jenom v případě, že se vytvoří s SMB 3.0+ se šifrováním nebo HTTPS. Klienti, kteří nepodporují SMB 3.0 nebo klienti, kteří podporují šifrování SMB 3.0, ale ne SMB, nebudou moci připojit sdílenou složku Azure, pokud je povoleno šifrování při přenosu. Další informace o tom, které operační systémy podporují smb 3.0 s šifrováním, naleznete v naší podrobné dokumentaci pro [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md). Všechny aktuální verze powershellových, cli a sad SDK podporují protokol HTTPS.  

Šifrování můžete zakázat při přenosu pro účet úložiště Azure. Pokud je šifrování zakázáno, soubory Azure také povolí SMB 2.1, SMB 3.0 bez šifrování a un-encrypted FileREST API volání přes HTTP. Primárním důvodem pro zakázání šifrování při přenosu je podpora starší aplikace, která musí být spuštěna ve starším operačním systému, jako je například windows server 2008 R2 nebo starší distribuce Linuxu. Soubory Azure povolují jenom připojení SMB 2.1 ve stejné oblasti Azure jako sdílená složka Azure; Klient SMB 2.1 mimo oblast Azure sdílené složky Azure, jako je například místní nebo v jiné oblasti Azure, nebude mít přístup ke sdílené složce.

Další informace o šifrování při přenosu najdete v [tématu vyžadování zabezpečeného přenosu v úložišti Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Viz také
- [Přehled služby Azure Files](storage-files-introduction.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)