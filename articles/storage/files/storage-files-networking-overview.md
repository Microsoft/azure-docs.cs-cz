---
title: Požadavky na síť pro Azure Files | Microsoft Docs
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141791"
---
# <a name="azure-files-networking-considerations"></a>Požadavky na síť pro Azure Files 
Ke sdílené složce Azure se můžete připojit dvěma způsoby:

- Přímý přístup ke sdílené složce přes protokoly SMB nebo REST. Tento vzor přístupu se primárně používá, když chcete eliminovat tolik místních serverů, kolik jich je možné.
- Vytvoření mezipaměti sdílené složky Azure na místním serveru pomocí Azure File Sync a přístup k datům sdílené složky z místního serveru s vaším protokolem volbou (SMB, NFS, FTPS atd.) pro váš případ použití. Tento vzor přístupu je užitečný, protože kombinuje nejlépe místní výkon i cloudovou škálu a připojené služby bez serveru, jako je Azure Backup.

Tento článek se zaměřuje na konfiguraci sítě pro případy, kdy váš případ použití volá pro přístup ke sdílené složce Azure přímo místo použití Azure File Sync. Další informace o požadavcích na používání sítě pro nasazení Azure File Sync najdete v tématu [konfigurace Azure File Sync proxy serveru a nastavení brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Nastavení účtu úložiště
Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB. Účty Azure Storage zpřístupňují dvě základní sady nastavení k zabezpečení sítě: šifrování v přenosech a branách firewall a virtuální sítě (virtuální sítě).

### <a name="encryption-in-transit"></a>Šifrování při přenosu
Ve výchozím nastavení mají všechny účty úložiště Azure povolený šifrování při přenosu. To znamená, že když připojíte sdílenou složku přes protokol SMB nebo k ní přistupujete prostřednictvím protokolu REST (například prostřednictvím Azure Portal, PowerShellu nebo rozhraní Azure SDK), budou soubory Azure umožňovat připojení jenom v případě, že se jedná o protokol SMB 3.0 + s šifrováním nebo HTTPS. Klienti, kteří nepodporují protokol SMB 3,0 nebo klienti, kteří podporují protokol SMB 3,0, ale nemají šifrování protokolu SMB, nebudou moci připojit sdílenou složku Azure, pokud je zapnuté šifrování při přenosu. Další informace o tom, které operační systémy podporují protokol SMB 3,0 se šifrováním, najdete v naší podrobné dokumentaci pro [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md). Všechny aktuální verze PowerShellu, CLI a sad SDK podporují protokol HTTPS.  

Šifrování můžete zakázat při přenosu pro účet služby Azure Storage. Když je šifrování zakázané, budou soubory Azure taky umožňovat protokol SMB 2,1, SMB 3,0 bez šifrování a nešifrované volání rozhraní REST API přes HTTP. Hlavním důvodem Zakázání šifrování při přenosu je podpora starší verze aplikace, kterou je třeba spustit ve starším operačním systému, jako je Windows Server 2008 R2 nebo starší distribuce systému Linux. Soubory Azure v rámci stejné oblasti Azure jako sdílená složka Azure povolují jenom připojení SMB 2,1. Klient SMB 2,1 mimo oblast Azure sdílené složky Azure, například v místním prostředí nebo v jiné oblasti Azure, nebude mít přístup ke sdílené složce souborů.

Další informace o šifrování v přenosu najdete v tématu [vyžadování zabezpečeného přenosu ve službě Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Brány firewall a virtuální sítě 
Brána firewall je zásada sítě, jejíž požadavky mají povolený přístup ke sdíleným složkám Azure a dalším prostředkům úložiště ve vašem účtu úložiště. Když se vytvoří účet úložiště s výchozím nastavením sítě, není omezený na určitou síť, a proto je přístupný z Internetu. To neznamená, že kdokoli na internetu může získat přístup k datům uloženým ve sdílených složkách Azure hostovaných ve vašem účtu úložiště, ale místo toho bude účet úložiště přijímat autorizované požadavky ze všech sítí. Požadavky mohou být autorizovány pomocí klíče účtu úložiště, tokenu sdíleného přístupového podpisu (SAS) (pouze REST) nebo s objektem zabezpečení uživatele služby Active Directory. 

Zásady brány firewall pro účet úložiště se dají použít k omezení přístupu na určité IP adresy nebo rozsahy nebo na virtuální síť. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti do virtuální sítě. 

[Virtuální síť nebo virtuální](../../virtual-network/virtual-networks-overview.md)síť se podobá tradiční síti, kterou provozujete ve vlastním datovém centru. Umožňuje vytvořit zabezpečený komunikační kanál pro prostředky Azure, jako jsou sdílené složky Azure, virtuální počítače, databáze SQL atd., aby se vzájemně komunikovaly. Jako je účet Azure Storage nebo virtuální počítač Azure, virtuální síť je prostředek Azure, který je nasazený ve skupině prostředků. V případě další konfigurace sítě může být Azure virtuální sítě také připojen k vašim místním sítím.

Když se prostředky, jako je třeba virtuální počítač Azure, přidají do virtuální sítě, bude virtuální síťové rozhraní (NIC) připojené k virtuálnímu počítači omezené na konkrétní virtuální síť. To je možné, protože virtuální počítače Azure jsou virtualizované počítače, které mají samozřejmě síťové adaptéry. Virtuální počítače se nabízejí jako součást služby Azure typu infrastruktura jako služba nebo IaaS, produkt seznamu. Vzhledem k tomu, že sdílené složky Azure jsou sdílené složky bez serveru, neobsahují síťové rozhraní, které byste mohli přidat do virtuální sítě. V takovém případě se soubory Azure nabízejí jako součást produktu Azure typu platforma jako služba nebo PaaS produkt seznamu. Aby bylo možné povolit účet úložiště jako součást virtuální sítě, Azure podporuje koncept pro služby PaaS s názvem koncové body služby. Koncový bod služby umožňuje, aby služby PaaS byly součástí virtuální sítě. Další informace o koncových bodech služby najdete v tématu [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md).

Účet úložiště se dá přidat do jedné nebo víc virtuálních sítí. Další informace o tom, jak přidat účet úložiště do virtuální sítě nebo nakonfigurovat další nastavení brány firewall, najdete v tématu [Konfigurace bran firewall a virtuálních sítí Azure Storage](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Sítě Azure
Ve výchozím nastavení jsou služby Azure, včetně souborů Azure, dostupné přes Internet. Vzhledem k tomu, že ve výchozím nastavení je přenos dat do vašeho účtu úložiště šifrovaný (a připojení SMB 2,1 se nikdy nepovoluje mimo oblast Azure), není nic nezabezpečené o přístupu ke sdíleným složkám Azure přes Internet. Na základě zásad vaší organizace nebo jedinečných regulativních předpisů můžete vyžadovat přísnější komunikaci s Azure, a proto Azure nabízí několik způsobů, jak omezit provoz mimo Azure do souborů Azure. Při přístupu ke sdílené složce Azure můžete lépe zabezpečit své sítě pomocí následujících nabídek služeb:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brána VPN je konkrétní typ brány virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a alternativním umístěním (jako je místní) přes Internet. Azure VPN Gateway je prostředek Azure, který se dá nasadit ve skupině prostředků na straně účtu úložiště nebo jiných prostředků Azure. Brány VPN zpřístupňují dva různé typy připojení:
    - Připojení brány [VPN typu Point-to-Site (P2S)](../../vpn-gateway/point-to-site-about.md) , což jsou připojení VPN mezi Azure a jednotlivými klienty. Toto řešení je primárně užitečné pro zařízení, která nejsou součástí místní sítě vaší organizace, jako jsou například dojíždění, kteří chtějí mít možnost připojit svou sdílenou složku Azure z domova, z kavárny nebo hotelu na cestách. Pokud chcete použít připojení VPN P2S se soubory Azure, bude nutné nakonfigurovat připojení VPN P2S pro každého klienta, který se chce připojit. 
    - Síť [VPN typu Site-to-Site (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), která je připojení VPN mezi Azure a sítí vaší organizace. Připojení VPN S2S umožňuje nakonfigurovat připojení VPN jednou, pro server VPN nebo zařízení hostované v síti vaší organizace, a ne pro každé klientské zařízení, které potřebuje mít přístup ke sdílené složce Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), která umožňuje vytvořit definovanou trasu mezi Azure a místní sítí, která neprojde internetem. Vzhledem k tomu, že ExpressRoute poskytuje vyhrazenou cestu mezi místním datacentrem a Azure, může být ExpressRoute užitečné v případě, že se výkon sítě zvažuje. ExpressRoute je také dobrou volbou v případě, že požadavky nebo předpisy vaší organizace vyžadují deterministické cesty k prostředkům v cloudu.

## <a name="securing-access-from-on-premises"></a>Zabezpečení přístupu z místního prostředí 
Při migraci souborů pro obecné účely (například dokumentů Office, souborů PDF, dokumentů CAD atd.) do souborů Azure budou uživatelé obvykle i nadále potřebovat přístup ke svým souborům z místních zařízení, jako jsou pracovní stanice, přenosné počítače a tablety. Hlavním aspektem pro sdílenou složku pro obecné účely je způsob, jakým můžou místní uživatelé bezpečně přistupovat ke sdíleným složkám souborů přes Internet nebo WAN.

Nejjednodušší způsob, jak získat přístup ke sdílené složce Azure z místního prostředí, je otevřít místní síť pro port 445, port, který používá protokol SMB, a připojit cestu UNC, kterou poskytuje Azure Portal. To nevyžaduje žádné zvláštní síťové požadavky. Mnoho zákazníků se zdráhají na otevření portu 445 kvůli zastaralým bezpečnostním pokynům týkajícím se protokolu SMB 1,0, což Microsoft nebere v úvahu, že jde o bezpečný protokol pro Internet. Soubory Azure neimplementují protokol SMB 1,0. 

Protokol SMB 3,0 byl navržen s explicitním požadavkem na bezpečné internetové sdílené soubory. Proto při použití protokolu SMB 3.0 + z perspektivy počítačové sítě není otevření portu 445 jiné než otevření portu 443, který se používá pro připojení HTTPS. Místo blokování portu 445 zabráníte nezabezpečenému provozu SMB 1,0, Microsoft doporučuje následující kroky:

> [!Important]  
> I když se rozhodnete opustit port 445 uzavřený do odchozího provozu, společnost Microsoft stále doporučuje pomocí těchto kroků odebrat z vašeho prostředí protokol SMB 1,0.

1. Zajistěte, aby byl na zařízeních vaší organizace odebrán nebo zakázán protokol SMB 1,0. Všechny aktuálně podporované verze systému Windows a Windows Server podporují odebrání nebo zakázání protokolu SMB 1,0 a počínaje verzí Windows 10 verze 1709 není ve výchozím nastavení ve Windows nainstalovaná služba SMB 1,0. Další informace o tom, jak zakázat SMB 1,0, najdete na stránkách pro konkrétní operační systém:
    - [Zabezpečení Windows/Windows serveru](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpečení systému Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Zajistěte, aby žádné produkty v rámci vaší organizace nevyžadovaly SMB 1,0 a odebraly ty, které mají. Udržujeme [SMB1 produkt](https://aka.ms/stillneedssmb1), který obsahuje všechny první a třetí výrobce známé společnosti Microsoft, aby vyžadovaly protokol SMB 1,0. 
1. Volitelné Pomocí brány firewall jiného výrobce s místní sítí vaší organizace zabráníte přenosům přes protokol SMB 1,0.

Pokud vaše organizace vyžaduje, aby byl port 445 zablokovaný na základě zásad nebo nařízení, můžete k tunelování provozu přes port 443 použít Azure VPN Gateway nebo ExpressRoute. Další informace o konkrétních krocích pro jejich nasazení najdete v naší konkrétní části stránky:
- [Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md)

Vaše organizace může mít další požadavek, aby odchozí provoz z vaší místní lokality měl postupovat podle deterministické cesty k prostředkům v cloudu. Pokud ano, ExpressRoute je schopen splnit tento požadavek.

## <a name="securing-access-from-cloud-resources"></a>Zabezpečení přístupu z cloudových prostředků
Obecně platí, že když je místní aplikace převolána a přesunuta do cloudu, aplikace a data aplikace se přesunou současně. To znamená, že primární aspekt migrace typu výtah a Shift zamkne přístup ke sdílené složce Azure na konkrétní virtuální počítače nebo služby Azure, které vyžadují přístup ke sdílené složce, aby fungovala. 

Možná budete chtít použít virtuální sítě k omezení, které virtuální počítače nebo jiné prostředky Azure můžou vytvářet síťová připojení (připojení SMB nebo REST API volání do sdílené složky Azure). Sdílenou složku Azure vždycky doporučujeme umístit do virtuální sítě, pokud povolíte nešifrovaný provoz do svého účtu úložiště. Jinak bez ohledu na to, jestli používáte virtuální sítě, je rozhodnutí, které by mělo být založené na vašich obchodních požadavcích a zásadách organizace.

Hlavním důvodem pro povolení nešifrovaného provozu do sdílené složky Azure je podpora systému Windows Server 2008 R2, Windows 7 nebo jiného staršího operačního systému pro přístup ke sdílené složce Azure pomocí protokolu SMB 2,1 (nebo SMB 3,0 bez šifrování pro některé distribuce systému Linux). Nedoporučujeme používat SMB 2,1 ani SMB 3,0 bez šifrování v operačních systémech, které podporují protokol SMB 3.0 + s šifrováním.

## <a name="see-also"></a>Další informace najdete v tématech
- [Přehled služby Azure Files](storage-files-introduction.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)