---
title: Nejčastější dotazy o Azure NetApp souborech | Dokumentace Microsoftu
description: Odpovědi na nejčastější dotazy o službě soubory Azure NetApp.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806383"
---
# <a name="faqs-about-azure-netapp-files"></a>Nejčastější dotazy o souborech Azure NetApp

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o službě soubory Azure NetApp. 

## <a name="networking-faqs"></a>Nejčastější dotazy k sítě

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Přejděte na cestu k datům systému souborů NFS přes Internet?  

Ne. Cesta k datům systému souborů NFS se nepřenáší přes Internet. Služba soubory Azure NetApp je služba Azure nativní, který je nasazený do Azure Virtual Network (VNet) Pokud je služba dostupná. Služba soubory Azure NetApp používá delegované podsíť a zřizuje síťové rozhraní přímo ve virtuální síti. 

Zobrazit [pokyny pro soubory Azure NetApp sítě plánování](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) podrobnosti.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Můžete připojit virtuální síť, již vytvořenou ve službě soubory Azure NetApp?

Ano, můžete připojit virtuální sítě, které jste vytvořili ve službě. 

Zobrazit [pokyny pro soubory Azure NetApp sítě plánování](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) podrobnosti.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Můžete připojit svazek systému souborů NFS NetApp soubory Azure pomocí názvu plně kvalifikovaný název domény DNS?

Ano, je to možné, pokud jste vytvořili požadované položky DNS. Služba soubory Azure NetApp poskytuje integrační balíček služby zřízeného svazku. 

> [!NOTE] 
> Služba soubory Azure NetApp můžete podle potřeby nasadíte další IP adresy pro službu.  Záznamy DNS se možná muset pravidelně aktualizovat.

## <a name="security-faqs"></a>Nejčastější dotazy k zabezpečení

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Mohou být zašifrovaná síťový provoz mezi virtuálním Počítačem Azure a úložiště?

Přenos dat (provoz z klienta NFSv3 nebo SMBv3 do soubory Azure NetApp svazky) nejsou šifrována. Provoz z virtuálního počítače Azure (s klientem systému souborů NFS nebo podepisování SMB) do služby soubory Azure NetApp ale stejně bezpečné jako služby veškerý ostatní provoz Azure virtuálního počítače na počítač. Tento provoz je místní pro sítě Azure datových center. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Může být v klidovém stavu zašifrovaná úložiště?

Všechny soubory Azure NetApp svazky jsou šifrované pomocí FIPS 140-2 standard. Všechny klíče jsou spravované službou soubory Azure NetApp. 

### <a name="how-are-encryption-keys-managed"></a>Jak se spravují šifrovací klíče? 

Služba je zajištěná Správa klíčů pro Azure NetApp Files.  Klíče spravované zákazníkem uživatele (přineste si vlastní klíče) v současné době nejsou podporovány.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Můžete nakonfigurovat pravidla systému souborů NFS export zásad pro řízení přístupu k cíli připojení služby soubory Azure NetApp?


Ano, můžete nakonfigurovat až o pěti pravidla v jedné zásadě export systému souborů NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Podporuje Azure NetApp soubory skupiny zabezpečení sítě?

Ne, aktuálně nelze použít skupiny zabezpečení sítě pro delegované podsítě souborů NetApp Azure nebo síťová rozhraní vytvořené službou.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Můžete použít Azure IAM souborů NetApp Azure?

Ano, souborů NetApp Azure RBAC funkce podporuje prostřednictvím Azure IAM.

## <a name="performance-faqs"></a>Nejčastější dotazy k výkonu

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co mám dělat optimalizace nebo optimalizovat souborů NetApp Azure?

Můžete provést následující akce za požadavky na výkon: 
- Ujistěte se, že je virtuální počítač odpovídající velikost.
- Povolte akcelerované síťové služby pro virtuální počítač.
- Vyberte úroveň požadované služby a velikosti kapacity fondu.
- Vytvoření svazku s velikostí požadované kvóty pro kapacitu a výkon.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak převést úrovně služeb na základě propustnosti souborů NetApp Azure na vstupně-výstupních operací?

Můžete převést MB/s na vstupně-výstupních operací s použitím následujícího vzorce:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Změna úrovně služby svazku

Změna úrovně služby svazku se aktuálně nepodporuje.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak monitorovat výkon souborů NetApp Azure?

Služba soubory Azure NetApp poskytuje metriky výkonu svazku. Azure Monitor můžete použít také pro monitorování metrik využití pro soubory Azure NetApp.  Zobrazit [metriky pro soubory Azure NetApp](azure-netapp-files-metrics.md) seznam metrik výkonu pro soubory Azure NetApp.

## <a name="nfs-faqs"></a>Nejčastější dotazy k systému souborů NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chci mít svazek připojený automaticky při spuštění nebo restartování virtuálního počítače Azure.  Jak nakonfigurovat své hostitele pro trvalé svazky systému souborů NFS?

Svazek systému souborů NFS automaticky připojit při spuštění virtuálního počítače nebo restartovat počítač, přidejte záznam do `/etc/fstab` souboru na hostiteli. 

Příklad: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    IP adresa Azure NetApp Files svazku v okně Vlastnosti svazku
- $FILEPATH  
    Cesta exportu svazku NetApp soubory Azure
- $MOUNTPOINT  
    Adresář, vytvoří na hostiteli systému Linux použili k připojení systému souborů NFS exportu

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Proč DF příkaz na klienta systému souborů NFS nezobrazuje velikosti zřízeného svazku?

Velikost svazku v DF je maximální velikost, který můžete zvětšit svazek souborů NetApp Azure. Velikost svazku souborů NetApp Azure v příkazu DF není reflektivní kvóty nebo velikost svazku.  Velikost svazku souborů NetApp Azure nebo kvóty můžete získat pomocí webu Azure portal nebo rozhraní API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jaká verze systému souborů NFS podporuje souborů NetApp Azure?

Služba soubory Azure NetApp v současné době podporuje NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Jak povolit kořenový vtěsnání?

Kořenové vtěsnání se aktuálně nepodporuje.

## <a name="smb-faqs"></a>Nejčastější dotazy k protokolu SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Podporuje Azure NetApp soubory Azure Active Directory?

Ne, se aktuálně nepodporuje.  Služba soubory Azure NetApp podporu Active Directory Domain Services (přenést vaše Own AD), který můžete použít existující řadiče domény Active Directory s Azure NetApp Files. Řadiče domény se může nacházet v Azure jako virtuální počítače nebo v místním prostředí prostřednictvím ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Jde o připojení služby Active Directory, vyžaduje se pro SMB přístup? 

Ano, je nutné vytvořit připojení k službě Active Directory před nasazením svazku SMB. Zadaný řadiče domény musí být přístupné delegované podsítě souborů NetApp Azure pro úspěšné připojení.  Zobrazit [vytvoření svazku SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) podrobnosti. 

### <a name="how-many-active-directory-connections-are-supported"></a>Kolik připojení služby Active Directory jsou podporovány?

Služba soubory Azure NetApp v současné době podporuje jedno připojení služby Active Directory na jedno předplatné. Navíc je specifická pro jeden účet NetApp; připojení služby Active Directory není sdílen mezi různými účty. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Jaké verze Windows AD jsou podporovány?

Služba soubory Azure NetApp podporuje verzi Windows serveru 2008r2SP1 2016 služby Active Directory Domain Services.

## <a name="capacity-management-faqs"></a>Správa kapacity nejčastější dotazy

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak můžu monitorovat využití kapacity fondu a objemu souborů NetApp Azure? 

Služba soubory Azure NetApp poskytuje metriky využití kapacity fondu a svazku. Azure Monitor můžete také používají k sledování využití u souborů NetApp Azure. Zobrazit [metriky pro soubory Azure NetApp](azure-netapp-files-metrics.md) podrobnosti. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Můžete spravovat NetApp soubory Azure pomocí Průzkumníka služby Azure Storage?

Ne. Služba soubory Azure NetApp nepodporuje Průzkumníka služby Azure Storage.

## <a name="data-migration-and-protection-faqs"></a>Nejčastější dotazy migrace a ochranu dat

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak migrovat data do služby soubory Azure NetApp?
Služba soubory Azure NetApp obsahuje svazky systému souborů NFS a protokolu SMB.  Pro migraci dat do služby můžete použít libovolný nástroj pro kopírování souborů. 

Řešení založené na SaaS, nabízí NetApp [NetApp cloudové synchronizace](https://cloud.netapp.com/cloud-sync-service).  Toto řešení umožňuje replikovat systému souborů NFS nebo sdílené složky SMB dat do Azure NetApp soubory systému souborů NFS exporty nebo podepisování SMB. 

Můžete také použít širokou škálu bezplatných nástrojů pro kopírování dat. Pro systém souborů NFS, můžete použít nástroje pro úlohy, jako [rsync](https://rsync.samba.org/examples.html) zkopírovat a synchronizace zdrojových dat do soubory Azure NetApp svazku. Pro protokol SMB, můžete použít úlohy [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje se dají taky replikovat oprávnění k souboru nebo složky. 

Požadavky pro migraci dat z místních souborů NetApp Azure jsou následující: 

- Zajistěte, aby že NetApp soubory Azure je k dispozici v cílové oblasti Azure.
- Ověření síťového připojení mezi zdrojem a IP adresu službě soubory Azure NetApp cílový svazek. Přenos dat mezi na místě a další služby soubory Azure NetApp je podporovaných přes ExpressRoute.
- Vytvořte cílovou službu Azure NetApp Files svazku.
- Přeneste zdroj dat na cílový svazek pomocí váš nástroj pro kopírování upřednostňované souborů.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak vytvořit kopii svazku souborů NetApp Azure v jiné oblasti Azure?
    
Služba soubory Azure NetApp obsahuje svazky systému souborů NFS a protokolu SMB.  Libovolný nástroj na základě kopie souboru lze použít k replikaci dat mezi oblastmi Azure. 

NetApp nabízí řešení SaaS na základě [NetApp cloudové synchronizace](https://cloud.netapp.com/cloud-sync-service).  Toto řešení umožňuje replikovat systému souborů NFS nebo sdílené složky SMB dat do Azure NetApp soubory systému souborů NFS exporty nebo podepisování SMB. 

Můžete také použít širokou škálu bezplatných nástrojů pro kopírování dat. Pro systém souborů NFS, můžete použít nástroje pro úlohy, jako [rsync](https://rsync.samba.org/examples.html) zkopírovat a synchronizace zdrojových dat do soubory Azure NetApp svazku. Pro protokol SMB, můžete použít úlohy [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) stejným způsobem.  Tyto nástroje se dají taky replikovat oprávnění k souboru nebo složky. 

Požadavky pro replikaci svazku souborů NetApp Azure do jiné oblasti Azure jsou následující: 
- Zajistěte, aby že NetApp soubory Azure je k dispozici v cílové oblasti Azure.
- Ověření síťového připojení mezi virtuálními sítěmi v jednotlivých oblastech. V současné době se nepodporuje globální partnerský vztah mezi virtuálními sítěmi.  Můžete navázat připojení mezi virtuálními sítěmi pomocí propojení s okruhem ExpressRoute nebo pomocí připojení S2S VPN. 
- Vytvořte cílovou službu Azure NetApp Files svazku.
- Přeneste zdroj dat na cílový svazek pomocí váš nástroj pro kopírování upřednostňované souborů.

### <a name="is-migration-with-azure-data-box-supported"></a>Migrace je s Azure Data Box podporovány?

Ne. Azure Data Box NetApp soubory Azure aktuálně nepodporuje. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Migrace se službou Azure Import/Export nepodporuje?

Ne. Služba Azure Import/Export souborů NetApp Azure aktuálně nepodporuje.

## <a name="next-steps"></a>Další postup  

- [Nejčastější dotazy k Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Nejčastější dotazy k virtuální síti Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)