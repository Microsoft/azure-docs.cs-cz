---
title: Nejčastější dotazy týkající se Azure NetApp Files | Microsoft Docs
description: Přečtěte si nejčastější dotazy týkající se Azure NetApp Files, jako jsou například síťové služby, zabezpečení, výkon, správa kapacity a migrace a ochrana dat.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2a64e595f0ea07510f416be56a54a3c74294b95d
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653617"
---
# <a name="faqs-about-azure-netapp-files"></a>Nejčastější dotazy týkající se Azure NetApp Files

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure NetApp Files. 

## <a name="networking-faqs"></a>Nejčastější dotazy k síti

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Přechází se přes Internet cestu k datům NFS?  

No. Cesta k datům NFS nepřekračuje Internet. Azure NetApp Files je nativní služba Azure, která je nasazená do Azure Virtual Network (VNet), kde je služba dostupná. Azure NetApp Files používá delegovanou podsíť a zřizuje síťové rozhraní přímo ve virtuální síti. 

Podrobnosti najdete v tématu [pokyny pro Azure NetApp Files plánování sítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Můžu připojit virtuální síť, kterou už jsem vytvořili ve službě Azure NetApp Files?

Ano, virtuální sítě, které jste vytvořili ve službě, můžete propojit. 

Podrobnosti najdete v tématu [pokyny pro Azure NetApp Files plánování sítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Je možné připojit ke svazku NFS Azure NetApp Files pomocí názvu FQDN DNS?

Ano, pokud vytvoříte požadované položky DNS, můžete. Azure NetApp Files poskytuje IP adresu služby pro zřízený svazek. 

> [!NOTE] 
> Azure NetApp Files může v případě potřeby nasadit další IP adresy pro službu.  Je možné, že bude nutné pravidelně aktualizovat položky DNS.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Můžu u Azure NetApp Filesho svazku nastavit nebo vybrat vlastní IP adresu?  

No. Přiřazení IP adres Azure NetApp Files svazků je dynamické. Přiřazení statických IP adres se nepodporuje. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Podporuje Azure NetApp Files virtuální síť Dual Stack (IPv4 a IPv6)?

Ne, Azure NetApp Files v současné době nepodporuje virtuální síť duálního zásobníku (IPv4 a IPv6).  
 
## <a name="security-faqs"></a>Nejčastější dotazy týkající se zabezpečení

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Je možné šifrovat síťový provoz mezi virtuálním počítačem Azure a úložištěm?

Přenos dat mezi klienty NFSv 4.1 a Azure NetApp Files svazky se dá šifrovat pomocí protokolu Kerberos s šifrováním AES-256. Podrobnosti najdete v tématu [Konfigurace šifrování protokolu Kerberos nfsv 4.1 pro Azure NetApp Files](configure-kerberos-encryption.md) .   

Přenos dat mezi klienty NFSv3 nebo SMBv3 do Azure NetApp Files svazků není šifrovaný. Provoz z virtuálního počítače Azure (s operačním systémem souborů NFS nebo klienta SMB) ale Azure NetApp Files je tak bezpečný jako jakýkoli jiný provoz Azure-VM-to-VM. Tento provoz je místní pro síť Azure Data Center. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Je možné úložiště v klidovém stavu zašifrovat?

Všechny Azure NetApp Files svazky se šifrují pomocí standardu FIPS 140-2. Všechny klíče spravuje služba Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Jak se spravují šifrovací klíče? 

Správa klíčů pro Azure NetApp Files je zpracovávána službou. Pro každý svazek se vygeneruje jedinečný datový šifrovací klíč XTS-AES-256. K šifrování a ochraně všech klíčů svazků se používá hierarchie šifrovacího klíče. Tyto šifrovací klíče se nikdy nezobrazují ani nehlásí v nešifrovaném formátu. Šifrovací klíče se odstraní hned po odstranění svazku.

Podpora klíčů spravovaných zákazníkem (Bring Your Own Key) pomocí vyhrazeného modulu HARDWAROVÉho zabezpečení Azure je dostupná na řízeném základě Východní USA, Střed USA – jih, Západní USA 2 a US Gov – Virginie oblastí. Můžete požádat o přístup na adrese [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Jakmile bude kapacita k dispozici, žádosti budou schváleny.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Můžu nakonfigurovat pravidla zásad exportu pro systém souborů NFS pro řízení přístupu k cíli připojení služby Azure NetApp Files?

Ano, v jedné zásadě exportu systému souborů NFS můžete nakonfigurovat až pět pravidel.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Podporuje Azure NetApp Files skupiny zabezpečení sítě?

Ne, v současné době nemůžete použít skupiny zabezpečení sítě na delegovanou podsíť Azure NetApp Files ani na síťová rozhraní vytvořená službou.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Můžu použít Azure IAM s Azure NetApp Files?

Ano, Azure NetApp Files podporuje funkce RBAC s Azure IAM.

## <a name="performance-faqs"></a>Nejčastější dotazy k výkonu

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

Úroveň služby existujícího svazku můžete změnit přesunutím svazku do jiného fondu kapacity, který využívá požadovanou [úroveň služby](azure-netapp-files-service-levels.md) pro daný svazek. Viz [dynamické změny úrovně služby svazku](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Návody monitorovat výkon Azure NetApp Files?

Azure NetApp Files poskytuje metriky výkonu svazku. K monitorování metrik využití pro Azure NetApp Files můžete použít taky Azure Monitor.  Seznam metrik výkonu pro Azure NetApp Files najdete v tématu [metriky pro Azure NetApp Files](azure-netapp-files-metrics.md) .

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Jaký je dopad na výkon protokolu Kerberos v NFSv 4.1?

Informace o možnostech zabezpečení pro NFSv 4.1, testovaných vektorech výkonu a očekávaném dopadu na výkon najdete v části [vliv na výkon protokolu Kerberos v nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance) . 

## <a name="nfs-faqs"></a>Nejčastější dotazy k systému souborů NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chci automaticky připojen svazek, když se spustí nebo restartuje virtuální počítač Azure.  Návody nakonfigurovat můj hostitel pro trvalé svazky NFS?

Pokud se pro svazek NFS automaticky připojí při spuštění nebo restartování virtuálního počítače, přidejte položku do `/etc/fstab` souboru na hostiteli. 

Podrobnosti najdete v tématu [připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Proč příkaz DF na klientovi NFS nezobrazuje zřízený velikost svazku?

Velikost svazku nahlášená v DF je maximální velikost, na kterou může Azure NetApp Files svazek růst. Velikost Azure NetApp Filesho svazku v příkazu DF neodráží kvótu ani velikost svazku.  Můžete získat velikost Azure NetApp Filesho svazku nebo kvótu prostřednictvím Azure Portal nebo rozhraní API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jakou verzi systému souborů NFS podporuje Azure NetApp Files?

Azure NetApp Files podporuje NFSv3 a NFSv 4.1. Svazek můžete [vytvořit](azure-netapp-files-create-volumes.md) pomocí buď verze systému souborů NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Návody povolit root vtěsnání?

Můžete určit, jestli má kořenový účet ke svazku přístup, nebo ne pomocí zásad exportu svazku. Podrobnosti najdete v tématu [Konfigurace zásad exportu pro svazek NFS](azure-netapp-files-configure-export-policy.md) .

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Můžu použít stejnou cestu k souboru (token pro vytvoření svazku) pro více svazků?

Ano, můžete. Cesta k souboru se ale musí použít buď v jiném předplatném, nebo v jiné oblasti.   

Například vytvoříte svazek s názvem `vol1` . A pak vytvoříte další svazek, který se také nazývá `vol1` v jiném fondu kapacity, ale ve stejném předplatném a oblasti. V takovém případě se při použití stejného názvu svazku `vol1` způsobí chyba. Chcete-li použít stejnou cestu k souboru, musí být název v jiné oblasti nebo předplatném.

## <a name="smb-faqs"></a>Nejčastější dotazy k protokolu SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Které verze protokolu SMB podporuje Azure NetApp Files?

Azure NetApp Files podporuje SMB 2,1 a SMB 3,1 (což zahrnuje podporu protokolu SMB 3,0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Vyžaduje se pro přístup přes protokol SMB připojení ke službě Active Directory? 

Ano, před nasazením svazku SMB musíte vytvořit připojení ke službě Active Directory. Zadané řadiče domény musí být přístupné delegované podsíti Azure NetApp Files pro úspěšné připojení.  Podrobnosti najdete v tématu [vytvoření svazku SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Kolik připojení služby Active Directory je podporováno?

Azure NetApp Files nepodporuje více připojení služby Active Directory (AD) v jedné *oblasti*, i když jsou připojení služby AD v různých účtech NetApp. V jednom *předplatném*ale můžete mít několik připojení AD, pokud jsou připojení služby AD v různých oblastech. Pokud potřebujete více připojení služby AD v jedné oblasti, můžete k tomu použít samostatné odběry. 

Připojení AD je nakonfigurované na účet NetApp. připojení AD je viditelné jenom prostřednictvím účtu NetApp, ve kterém je vytvořený.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Podporuje Azure NetApp Files Azure Active Directory? 

Jsou podporovány jak [služby Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) , tak [Active Directory Domain Services (služba AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Pomocí Azure NetApp Files můžete použít existující řadiče domény služby Active Directory. Řadiče domény se můžou nacházet v Azure jako virtuální počítače nebo místně prostřednictvím sítě VPN ExpressRoute nebo S2S. Azure NetApp Files v tuto chvíli nepodporuje službu AD JOIN pro [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

Pokud používáte Azure NetApp Files s Azure Active Directory Domain Services, cesta k organizační jednotce je, `OU=AADDC Computers` když pro svůj účet NetApp nakonfigurujete službu Active Directory.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jaké verze Windows serveru Active Directory se podporují?

Azure NetApp Files podporuje verze Active Directory Domain Services Windows Server 2008r2SP1-2019.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Proč dostupné místo v klientovi SMB nezobrazuje zřízené velikosti?

Velikost svazku hlášená klientem SMB je maximální velikost, na kterou může Azure NetApp Files svazek růst. Velikost Azure NetApp Filesho svazku, jak je znázorněno na klientovi SMB, se nereflektuje na kvótu nebo velikost svazku. Můžete získat velikost Azure NetApp Filesho svazku nebo kvótu prostřednictvím Azure Portal nebo rozhraní API.

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="dual-protocol-faqs"></a>Nejčastější dotazy týkající se duálního protokolu

### <a name="i-tried-to-use-the-root-and-local-users-to-access-a-dual-protocol-volume-with-the-ntfs-security-style-on-a-unix-system-why-did-i-encounter-a-permission-denied-error"></a>Byl proveden pokus o použití kořenového a místního uživatele pro přístup ke svazku se dvěma protokoly se stylem zabezpečení systému souborů NTFS v systému UNIX. Proč se mi stala chyba "oprávnění zamítnuto"?   

Přečtěte si téma [řešení potíží se svazky s duálním protokolem](troubleshoot-dual-protocol-volumes.md) pro řešení.

### <a name="when-i-try-to-create-a-dual-protocol-volume-why-does-the-creation-process-fail-with-the-error-failed-to-validate-ldap-configuration-try-again-after-correcting-ldap-configuration"></a>Když se pokouším vytvořit svazek se dvěma protokoly, proč proces vytváření selže a dojde k chybě při ověřování konfigurace LDAP, zkuste to znovu po opravě konfigurace LDAP.  

Přečtěte si téma [řešení potíží se svazky s duálním protokolem](troubleshoot-dual-protocol-volumes.md) pro řešení.

## <a name="capacity-management-faqs"></a>Nejčastější dotazy ke správě kapacity

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Návody monitorování využití fondu kapacity a objemu Azure NetApp Files? 

Azure NetApp Files zajišťuje fond kapacit a metriky využití svazků. K monitorování využití Azure NetApp Files můžete použít taky Azure Monitor. Podrobnosti najdete v tématu [metriky pro Azure NetApp Files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Můžu Azure NetApp Files přes Průzkumník služby Azure Storage spravovat?

No. Průzkumník služby Azure Storage nepodporuje Azure NetApp Files.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Návody zjistit, jestli se adresář blíží velikosti limitu?

Pomocí `stat` příkazu z klienta můžete zjistit, jestli se adresář blíží omezení maximální velikosti pro metadata adresáře (320 MB).

V případě adresáře 320 MB je počet bloků 655360, přičemž každá velikost bloku je 512 bajtů.  (To znamená 320x1024x1024/512.)  

Příklady:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


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

No. Azure Data Box nepodporuje aktuálně Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Je migrace pomocí služby Azure import/export podporovaná?

No. Služba Azure import/export nepodporuje Azure NetApp Files aktuálně.

## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy k Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Nejčastější dotazy k Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox)
- [Nejčastější dotazy týkající se výkonu protokolu SMB pro Azure NetApp Files](azure-netapp-files-smb-performance.md)
