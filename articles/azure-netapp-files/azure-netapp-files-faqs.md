---
title: Nejčastější dotazy k souborům Azure NetApp | Dokumenty společnosti Microsoft
description: Odpovědi na nejčastější dotazy týkající se souborů Azure NetApp.
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
ms.date: 03/25/2020
ms.author: b-juche
ms.openlocfilehash: 79c23c49cbf3c869b41e5a2dbfc6ec0aaa93e4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258171"
---
# <a name="faqs-about-azure-netapp-files"></a>Nejčastější dotazy k souborům Azure NetApp

Tento článek odpovídá na nejčastější dotazy týkající se souborů Azure NetApp. 

## <a name="networking-faqs"></a>Časté otázky k vytváření sítí

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Přecvákla datová cesta nfs přes Internet?  

Ne. Datová cesta nfs neprochází přes Internet. Azure NetApp Files je nativní služba Azure, která se nasadí do virtuální sítě Azure (VNet), kde je služba dostupná. Soubory Azure NetApp používá delegovanou podsíť a zřaží síťové rozhraní přímo ve virtuální síti. 

Podrobnosti najdete [v tématu Pokyny pro plánování sítě Souborů Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Můžu připojit virtuální síť, kterou jsem už vytvořil, ke službě Azure NetApp Files?

Ano, virtuální sítě, které jste vytvořili ke službě. 

Podrobnosti najdete [v tématu Pokyny pro plánování sítě Souborů Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Můžu připojit svazek nfs souborů Azure NetApp pomocí názvu DNS FQDN?

Ano, můžete, pokud vytvoříte požadované položky DNS. Azure NetApp Files poskytuje IP adresu služby pro zřízený svazek. 

> [!NOTE] 
> Soubory Azure NetApp můžete nasadit další IP adresy pro službu podle potřeby.  Může být nutné pravidelně aktualizovat položky DNS.

## <a name="security-faqs"></a>Nejčastější dotazy k zabezpečení

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Může být síťový provoz mezi virtuálním počítačem Azure a úložištěm šifrován?

Datový provoz (přenosy z nfsv3, NFSv4.1 nebo SMBv3 klienta na svazky Azure NetApp Files) není šifrovaný. Provoz z virtuálního počítače Azure (spuštěný klient nfs nebo SMB) do souborů Azure NetApp je však stejně zabezpečený jako jakýkoli jiný provoz Azure-VM-To-VM. Tento provoz je místní do sítě datového centra Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Může být úložiště zašifrováno v klidu?

Všechny svazky souborů Azure NetApp jsou šifrované pomocí standardu FIPS 140-2. Všechny klíče jsou spravované službou Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Jak jsou šifrovací klíče spravovány? 

Správa klíčů pro soubory Azure NetApp je zpracována službou. Pro každý svazek je generován jedinečný šifrovací klíč XTS-AES-256. Hierarchie šifrovacího klíče se používá k šifrování a ochraně všech klíčů svazku. Tyto šifrovací klíče nejsou nikdy zobrazeny nebo hlášeny v nešifrovaném formátu. Šifrovací klíče jsou odstraněny okamžitě při odstranění svazku.

Podpora pro uživatelem spravované klíče (Přineste si vlastní klíče) pomocí vyhrazeného modulu hardwarového zabezpečení Azure je k dispozici na řízeném základě v oblasti USA – východ, USA – západ2 a USA – jižní střed.  Můžete požádat **anffeedback@microsoft.com**o přístup na adrese . Jakmile bude kapacita k dispozici, budou žádosti schváleny.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Můžu nakonfigurovat pravidla zásad exportu systému souborů NFS tak, aby řídila přístup k cíli připojení služby Azure NetApp Files?


Ano, můžete nakonfigurovat až pět pravidel v jedné zásadě exportu služby NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Podporuje soubory Azure NetApp skupiny zabezpečení sítě?

Ne, v současné době nelze použít skupiny zabezpečení sítě na delegovanou podsíť souborů Azure NetApp nebo na síťová rozhraní vytvořená službou.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Můžu používat Azure IAM se soubory Azure NetApp?

Ano, Azure NetApp Files podporuje funkce RBAC s Azure IAM.

## <a name="performance-faqs"></a>Časté otázky k výkonu

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co mám udělat pro optimalizaci nebo optimalizaci výkonu souborů Azure NetApp?

Podle požadavků na výkon můžete provést následující akce: 
- Ujistěte se, že virtuální počítač je velikost idve.
- Povolte zrychlené vytváření sítí pro virtuální hod.
- Vyberte požadovanou úroveň a velikost služby pro fond kapacity.
- Vytvořte svazek s požadovanou velikostí kvóty pro kapacitu a výkon.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak převádím úrovně služeb azure netappových souborů na základě propustností na viposlužby?

Mb/s můžete převést na vstupně-to-vstupně-tos pomocí následujícího vzorce:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Jak změním úroveň služeb svazku?

Změna úrovně služeb svazku není aktuálně podporována.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak můžu sledovat výkon souborů Azure NetApp?

Soubory Azure NetApp poskytují metriky výkonu svazku. Azure Monitor můžete taky použít k monitorování metrik využití pro soubory Azure NetApp.  Seznam metrik výkonu pro soubory Azure NetApp najdete v [tématu Metriky pro soubory Azure NetApp.](azure-netapp-files-metrics.md)

## <a name="nfs-faqs"></a>Nejčastější dotazy k NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chci mít svazek připojený automaticky při spuštění nebo restartování virtuálního počítače Azure.  Jak nakonfiguruji hostitele pro trvalé svazky služby Souborů NFS?

Chcete-li, aby se svazek systému souborů NFS `/etc/fstab` automaticky připojoval při spuštění nebo restartování virtuálního počítače, přidejte položku do souboru na hostiteli. 

Podrobnosti najdete [v tématu Připojení nebo odpojte svazek pro virtuální počítače se systémem Windows nebo Linux.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Proč příkaz DF v klientovi systému souborů NFS nezobrazuje velikost zřízeného svazku?

Velikost svazku uvedená v DF je maximální velikost svazku Soubory Azure NetApp může růst. Velikost svazku Soubory Azure NetApp v příkazu DF neodráží kvótu nebo velikost svazku.  Velikost svazku nebo kvóty souborů Azure NetApp můžete získat prostřednictvím portálu Azure nebo rozhraní API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jakou verzi systému souborů NFS soubory Azure NetApp podporují?

Soubory Azure NetApp podporují nfsv3 a NFSv4.1. [Svazek](azure-netapp-files-create-volumes.md) můžete vytvořit buď pomocí verze systému nfs. 

### <a name="how-do-i-enable-root-squashing"></a>Jak povolím squash eroot?

Root squashing není v současné době podporován.

## <a name="smb-faqs"></a>Nejčastější dotazy k SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Je pro přístup s rozhraním SMB vyžadováno připojení služby Active Directory? 

Ano, před nasazením svazku SMB je nutné vytvořit připojení služby Active Directory. Zadané řadiče domény musí být přístupné delegované podsíti souborů Azure NetApp pro úspěšné připojení.  Podrobnosti najdete [v tématu Vytvoření svazku SMB.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 

### <a name="how-many-active-directory-connections-are-supported"></a>Kolik připojení služby Active Directory je podporováno?

Soubory Azure NetApp nepodporuje více připojení služby Active Directory (AD) v jedné *oblasti*, a to i v případě, že připojení AD jsou v různých účtech NetApp. Můžete však mít více připojení služby AD v jednom *předplatném*, pokud jsou připojení služby AD v různých oblastech. Pokud potřebujete více připojení služby AD v jedné oblasti, můžete k tomu použít samostatná předplatná. 

Připojení AD je konfigurováno pro účet NetApp. Připojení AD je viditelné pouze prostřednictvím účtu NetApp, ve který je vytvořen.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Podporuje azure netapp ové soubory Azure Active Directory? 

Podporovány jsou [služby Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) a Služba [Active Directory Domain Services (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) S azure netapp soubory můžete použít existující řadiče domény služby Active Directory. Řadiče domény se můžou v Azure napěchovat jako virtuální počítače nebo místně prostřednictvím ExpressRoute nebo S2S VPN. Soubory Azure NetApp v tuto chvíli nepodporují připojení služby AD pro [službu Azure Active Directory.](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)

Pokud používáte soubory Azure NetApp se službou Azure Active `OU=AADDC Computers` Directory Domain Services, cesta organizační jednotky je při konfiguraci služby Active Directory pro váš účet NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jaké verze služby Windows Server Active Directory jsou podporovány?

Soubory Azure NetApp podporují verze služby Active Directory Domain Services pro Windows Server 2008r2SP1-2019.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Proč dostupné místo v klientovi SMB nezobrazuje zřízenou velikost?

Velikost svazku vykázaná klientem SMB je maximální velikost svazku souborů Azure NetApp, na kterou se může zvětšit. Velikost svazku Souborů Azure NetApp, jak je znázorněno na klientovi SMB, neodráží kvótu nebo velikost svazku. Velikost svazku nebo kvóty souborů Azure NetApp můžete získat prostřednictvím portálu Azure nebo rozhraní API.
<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Časté otázky k řízení kapacity

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak můžu monitorovat využití fondu kapacit a objemu souborů Azure NetApp? 

Soubory Azure NetApp poskytuje objem kapacity a metriky využití svazku. Azure Monitor můžete taky použít ke sledování využití souborů Azure NetApp. Podrobnosti najdete [v tématu Metriky pro soubory Azure NetApp.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Můžu spravovat soubory Azure NetApp prostřednictvím Průzkumníka úložiště Azure?

Ne. Azure NetApp Soubory není podporována Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Jak zjistím, zda se adresář blíží velikosti limitu?

Pomocí příkazu `stat` od klienta můžete zjistit, zda se adresář blíží maximálnímu limitu velikosti (320 MB).

Pro adresář 320 MB je počet bloků 655360, přičemž každá velikost bloku je 512 bajtů.  (To znamená 320x1024x1024/512.)  

Příklady:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Časté otázky k migraci a ochraně dat

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak můžu migrovat data do souborů Azure NetApp?
Soubory Azure NetApp poskytují svazky systému souborů NFS a SMB.  K migraci dat do služby můžete použít libovolný nástroj pro kopírování souborů. 

NetApp nabízí řešení založené na SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Toto řešení umožňuje replikovat data systému souborů NFS nebo SMB do exportů souborů nfs azure netapp nebo sdílených složek SMB. 

Ke kopírování dat můžete také použít širokou škálu bezplatných nástrojů. Pro systém souborů NFS můžete ke kopírování a synchronizaci zdrojových dat do svazku souborů Azure NetApp použít nástroje pro úlohy, jako je [rsync.](https://rsync.samba.org/examples.html) Pro SMB můžete použít [úlohy robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje mohou také replikovat oprávnění k souborům nebo složkám. 

Požadavky na migraci dat z místního prostředí do souborů Azure NetApp jsou následující: 

- Ujistěte se, že soubory Azure NetApp jsou dostupné v cílové oblasti Azure.
- Ověřte připojení k síti mezi zdrojem a adresou IP cílového svazku soubory Azure NetApp. Přenos dat mezi místní mise a službou Azure NetApp Files je podporován přes ExpressRoute.
- Vytvořte cílový svazek souborů Azure NetApp.
- Přeneste zdrojová data do cílového svazku pomocí upřednostňovaného nástroje pro kopírování souborů.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak vytvořím kopii svazku souborů Azure NetApp v jiné oblasti Azure?
    
Soubory Azure NetApp poskytují svazky systému souborů NFS a SMB.  Libovolný nástroj pro kopírování souborů lze použít k replikaci dat mezi oblastmi Azure. 

NetApp nabízí řešení založené na SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Toto řešení umožňuje replikovat data systému souborů NFS nebo SMB do exportů souborů nfs azure netapp nebo sdílených složek SMB. 

Ke kopírování dat můžete také použít širokou škálu bezplatných nástrojů. Pro systém souborů NFS můžete ke kopírování a synchronizaci zdrojových dat do svazku souborů Azure NetApp použít nástroje pro úlohy, jako je [rsync.](https://rsync.samba.org/examples.html) Pro SMB můžete použít [úlohy robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje mohou také replikovat oprávnění k souborům nebo složkám. 

Požadavky na replikaci svazku souborů Azure NetApp do jiné oblasti Azure jsou následující: 
- Ujistěte se, že soubory Azure NetApp jsou dostupné v cílové oblasti Azure.
- Ověřte připojení k síti mezi virtuálními sítěmi v každé oblasti. V současné době globální partnerský vztah mezi virtuálními sítěmi není podporován.  Připojení mezi virtuálními sítěmi můžete navázat propojením s okruhem ExpressRoute nebo pomocí připojení S2S VPN. 
- Vytvořte cílový svazek souborů Azure NetApp.
- Přeneste zdrojová data do cílového svazku pomocí upřednostňovaného nástroje pro kopírování souborů.

### <a name="is-migration-with-azure-data-box-supported"></a>Je migrace s Azure Data Box emituje?

Ne. Azure Data Box momentálně nepodporuje soubory Azure NetApp. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Je migrace se službou Azure Import/Export podporovaná?

Ne. Služba Azure Import/Export momentálně nepodporuje soubory Azure NetApp.

## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy k webu Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Nejčastější dotazy k virtuální síti Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Časté otázky týkající se výkonu SMB pro soubory Azure NetApp](azure-netapp-files-smb-performance.md)
