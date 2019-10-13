---
title: Nejčastější dotazy týkající se Azure NetApp Files | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: eefa54806d9f5ec9ef3a0c02e4abbaf6b4bf22e2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298482"
---
# <a name="faqs-about-azure-netapp-files"></a>Nejčastější dotazy týkající se Azure NetApp Files

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure NetApp Files. 

## <a name="networking-faqs"></a>Nejčastější dotazy k síti

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Přechází se přes Internet cestu k datům NFS?  

Ne. Cesta k datům NFS nepřekračuje Internet. Azure NetApp Files je nativní služba Azure, která je nasazená do Azure Virtual Network (VNet), kde je služba dostupná. Azure NetApp Files používá delegovanou podsíť a zřizuje síťové rozhraní přímo ve virtuální síti. 

Podrobnosti najdete v tématu [pokyny pro Azure NetApp Files plánování sítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Můžu připojit virtuální síť, kterou už jsem vytvořili ve službě Azure NetApp Files?

Ano, virtuální sítě, které jste vytvořili ve službě, můžete propojit. 

Podrobnosti najdete v tématu [pokyny pro Azure NetApp Files plánování sítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Je možné připojit ke svazku NFS Azure NetApp Files pomocí názvu FQDN DNS?

Ano, pokud vytvoříte požadované položky DNS, můžete. Azure NetApp Files poskytuje IP adresu služby pro zřízený svazek. 

> [!NOTE] 
> Azure NetApp Files může v případě potřeby nasadit další IP adresy pro službu.  Je možné, že bude nutné pravidelně aktualizovat položky DNS.

## <a name="security-faqs"></a>Nejčastější dotazy týkající se zabezpečení

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Je možné šifrovat síťový provoz mezi virtuálním počítačem Azure a úložištěm?

Přenosy dat (přenosy z klienta NFSv3, NFSv 4.1 nebo SMBv3 do Azure NetApp Files) nejsou šifrované. Provoz z virtuálního počítače Azure (s operačním systémem souborů NFS nebo klienta SMB) ale Azure NetApp Files je tak bezpečný jako jakýkoli jiný provoz Azure-VM-to-VM. Tento provoz je místní pro síť Azure Data Center. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Je možné úložiště v klidovém stavu zašifrovat?

Všechny Azure NetApp Files svazky se šifrují pomocí standardu FIPS 140-2. Všechny klíče spravuje služba Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Jak se spravují šifrovací klíče? 

Správa klíčů pro Azure NetApp Files je zpracovávána službou. Pro každý svazek se vygeneruje jedinečný datový šifrovací klíč XTS-AES-256. K šifrování a ochraně všech klíčů svazků se používá hierarchie šifrovacího klíče. Tyto šifrovací klíče se nikdy nezobrazují ani nehlásí v nešifrovaném formátu. Šifrovací klíče se odstraní hned po odstranění svazku.

V současné době nejsou podporované uživatelsky spravované klíče (Přineste si vlastní klíče).

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Můžu nakonfigurovat pravidla zásad exportu pro systém souborů NFS pro řízení přístupu k cíli připojení služby Azure NetApp Files?


Ano, v jedné zásadě exportu systému souborů NFS můžete nakonfigurovat až pět pravidel.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Podporuje Azure NetApp Files skupiny zabezpečení sítě?

Ne, v současné době nemůžete použít skupiny zabezpečení sítě na delegovanou podsíť Azure NetApp Files ani na síťová rozhraní vytvořená službou.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Můžu použít Azure IAM s Azure NetApp Files?

Ano, Azure NetApp Files podporuje funkce RBAC s Azure IAM.

## <a name="performance-faqs"></a>Nejčastější dotazy týkající se výkonu

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co mám udělat pro optimalizaci nebo ladění výkonu Azure NetApp Files?

Na základě požadavků na výkon můžete provést následující akce: 
- Ujistěte se, že je virtuální počítač správně určený.
- Povolte pro virtuální počítač akcelerované síťové služby.
- Vyberte požadovanou úroveň služby a velikost pro fond kapacit.
- Vytvořte svazek s požadovanou velikostí kvóty pro kapacitu a výkon.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Návody převést úrovně služeb na základě propustnosti Azure NetApp Files na IOPS?

MB/s můžete převést na IOPS pomocí následujícího vzorce:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Návody změnit úroveň služby svazku?

Změna úrovně služby svazku se momentálně nepodporuje.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Návody monitorovat výkon Azure NetApp Files?

Azure NetApp Files poskytuje metriky výkonu svazku. K monitorování metrik využití pro Azure NetApp Files můžete použít taky Azure Monitor.  Seznam metrik výkonu pro Azure NetApp Files najdete v tématu [metriky pro Azure NetApp Files](azure-netapp-files-metrics.md) .

## <a name="nfs-faqs"></a>Nejčastější dotazy k systému souborů NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chci automaticky připojen svazek, když se spustí nebo restartuje virtuální počítač Azure.  Návody nakonfigurovat můj hostitel pro trvalé svazky NFS?

Pokud se pro svazek NFS automaticky připojí při spuštění nebo restartování virtuálního počítače, přidejte položku do souboru `/etc/fstab` na hostiteli. 

Příklad: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    V okně vlastností svazku se našla IP adresa Azure NetApp Filesho svazku.
- $FILEPATH  
    Cesta exportu Azure NetApp Filesho svazku
- $MOUNTPOINT  
    Adresář vytvořený na hostiteli systému Linux, který slouží k připojení exportu NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Proč příkaz DF na klientovi NFS nezobrazuje zřízený velikost svazku?

Velikost svazku nahlášená v DF je maximální velikost, na kterou může Azure NetApp Files svazek růst. Velikost Azure NetApp Filesho svazku v příkazu DF neodráží kvótu ani velikost svazku.  Můžete získat velikost Azure NetApp Filesho svazku nebo kvótu prostřednictvím Azure Portal nebo rozhraní API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jakou verzi systému souborů NFS podporuje Azure NetApp Files?

Azure NetApp Files podporuje NFSv3 a NFSv 4.1. Svazek můžete vytvořit pomocí buď verze systému souborů NFS. 

> [!IMPORTANT] 
> Přístup k funkci NFSv 4.1 vyžaduje přidávání do seznamu povolených.  Pokud chcete požádat o přidávání do seznamu povolených žádostí, odešlete žádost o <anffeedback@microsoft.com>. 


### <a name="how-do-i-enable-root-squashing"></a>Návody povolit root vtěsnání?

Kořenová vtěsnání se v tuto chvíli nepodporuje.

## <a name="smb-faqs"></a>Nejčastější dotazy k protokolu SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Vyžaduje se pro přístup přes protokol SMB připojení ke službě Active Directory? 

Ano, před nasazením svazku SMB musíte vytvořit připojení ke službě Active Directory. Zadané řadiče domény musí být přístupné delegované podsíti Azure NetApp Files pro úspěšné připojení.  Podrobnosti najdete v tématu [vytvoření svazku SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Kolik připojení služby Active Directory je podporováno?

Azure NetApp Files aktuálně podporuje jedno připojení Active Directory pro každé předplatné. Připojení ke službě Active Directory je také specifické pro jeden účet NetApp. nesdílí se mezi účty. 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Podporuje Azure NetApp Files Azure Active Directory? 

Jsou podporovány jak [služby Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) , tak [Active Directory Domain Services (služba AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Pomocí Azure NetApp Files můžete použít existující řadiče domény služby Active Directory. Řadiče domény se můžou nacházet v Azure jako virtuální počítače nebo místně prostřednictvím sítě VPN ExpressRoute nebo S2S. Azure NetApp Files v tuto chvíli nepodporuje službu AD JOIN pro [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

Pokud používáte Azure NetApp Files s Azure Active Directory Domain Services, cesta k organizační jednotce je `OU=AADDC Computers`, když konfigurujete službu Active Directory pro svůj účet NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jaké verze Windows serveru Active Directory se podporují?

Azure NetApp Files podporuje verze Active Directory Domain Services Windows Server 2008r2SP1-2019.

## <a name="capacity-management-faqs"></a>Nejčastější dotazy ke správě kapacity

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Návody monitorování využití fondu kapacity a objemu Azure NetApp Files? 

Azure NetApp Files zajišťuje fond kapacit a metriky využití svazků. K monitorování využití Azure NetApp Files můžete použít taky Azure Monitor. Podrobnosti najdete v tématu [metriky pro Azure NetApp Files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Můžu Azure NetApp Files přes Průzkumník služby Azure Storage spravovat?

Ne. Průzkumník služby Azure Storage nepodporuje Azure NetApp Files.

## <a name="data-migration-and-protection-faqs"></a>Nejčastější dotazy k migraci a ochraně dat

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Návody migrovat data do Azure NetApp Files?
Azure NetApp Files poskytuje svazky NFS a SMB.  K migraci dat do služby můžete použít libovolný nástroj pro kopírování na základě souborů. 

NetApp nabízí řešení pro [cloudové synchronizace NetApp](https://cloud.netapp.com/cloud-sync-service), které je založené na SaaS.  Řešení umožňuje replikovat data systému souborů NFS nebo SMB do Azure NetApp Files soubory NFS exporty nebo sdílené složky SMB. 

K kopírování dat můžete také využít nejrůznější bezplatné nástroje. Pro systém souborů NFS můžete pomocí nástrojů pro úlohy, jako je například [rsync](https://rsync.samba.org/examples.html) , kopírovat a synchronizovat zdrojová data do Azure NetApp Filesho svazku. V případě protokolu SMB můžete použít úlohy nástroje [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje můžou taky replikovat oprávnění k souborům nebo složkám. 

Požadavky na migraci dat z místního prostředí do Azure NetApp Files jsou následující: 

- Ujistěte se, že je Azure NetApp Files k dispozici v cílové oblasti Azure.
- Ověřte připojení k síti mezi zdrojovým a Azure NetApp Files IP adresou cílového svazku. Přenos dat mezi místním prostředím a službou Azure NetApp Files se podporuje přes ExpressRoute.
- Vytvořte cílový svazek Azure NetApp Files.
- Přeneste zdrojová data do cílového svazku pomocí vašeho preferovaného nástroje pro kopírování souborů.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Návody vytvořit kopii Azure NetApp Files svazku v jiné oblasti Azure?
    
Azure NetApp Files poskytuje svazky NFS a SMB.  K replikaci dat mezi oblastmi Azure se dá použít jakýkoli nástroj pro kopírování založené na souborech. 

NetApp nabízí řešení založené na SaaS a [cloudové synchronizaci NetApp](https://cloud.netapp.com/cloud-sync-service).  Řešení umožňuje replikovat data systému souborů NFS nebo SMB do Azure NetApp Files soubory NFS exporty nebo sdílené složky SMB. 

K kopírování dat můžete také využít nejrůznější bezplatné nástroje. Pro systém souborů NFS můžete pomocí nástrojů pro úlohy, jako je například [rsync](https://rsync.samba.org/examples.html) , kopírovat a synchronizovat zdrojová data do Azure NetApp Filesho svazku. V případě protokolu SMB můžete použít úlohy nástroje [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje můžou taky replikovat oprávnění k souborům nebo složkám. 

Požadavky na replikaci Azure NetApp Filesho svazku do jiné oblasti Azure jsou následující: 
- Ujistěte se, že je Azure NetApp Files k dispozici v cílové oblasti Azure.
- Ověřte připojení k síti mezi virtuální sítě v každé oblasti. Globální partnerský vztah mezi virtuální sítě se v současné době nepodporuje.  Připojení mezi virtuální sítě můžete vytvořit propojením s okruhem ExpressRoute nebo pomocí připojení VPN S2S. 
- Vytvořte cílový svazek Azure NetApp Files.
- Přeneste zdrojová data do cílového svazku pomocí vašeho preferovaného nástroje pro kopírování souborů.

### <a name="is-migration-with-azure-data-box-supported"></a>Je migrace s Azure Data Box podporována?

Ne. Azure Data Box nepodporuje aktuálně Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Je migrace pomocí služby Azure import/export podporovaná?

Ne. Služba Azure import/export nepodporuje Azure NetApp Files aktuálně.

## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy k Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Nejčastější dotazy k Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
