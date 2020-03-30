---
title: Řešení Azure VMware od CloudSimple – Zálohování virtuálních počítačů v oblasti úloh v privátním cloudu pomocí veeamu
description: Popisuje, jak můžete zálohovat virtuální počítače, které běží v Azure-založené CloudSimple Privátní cloud pomocí Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025125"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Zálohování virtuálních počítačů s úlohami v cloudu Simple Privátní cloud pomocí Veeam B&R

Tato příručka popisuje, jak můžete zálohovat virtuální počítače, které běží v Azure-založené CloudSimple Privátní cloud pomocí Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>O zálohování a obnovení řešení Veeam

Řešení Veeam obsahuje následující komponenty.

**Záložní server**

Záložní server je server Windows (VM), který slouží jako řídicí centrum společnosti Veeam a plní tyto funkce: 

* Koordinuje úlohy zálohování, replikace, obnovení a obnovení
* Řídí plánování úloh a přidělování zdrojů.
* Umožňuje nastavit a spravovat součásti infrastruktury zálohování a určit globální nastavení pro infrastrukturu zálohování.

**Proxy servery**

Proxy servery jsou nainstalovány mezi záložním serverem a dalšími součástmi infrastruktury zálohování. Spravují následující funkce:

* Načítání dat virtuálního zařízení z produkčního úložiště
* Komprese
* Odstranění duplicit
* Šifrování
* Přenos dat do zálohovacího úložiště

**Úložiště záloh**

Úložiště záloh je úložiště úložiště, kde veeam uchovává záložní soubory, kopie virtuálních zařízení a metadata pro replikované virtuální počítače.  Repozitářem může být server Windows nebo Linux s místními disky (nebo připojenými nfs/smb) nebo zařízením pro odstranění duplicitního úložiště hardwarového úložiště.

### <a name="veeam-deployment-scenarios"></a>Scénáře nasazení Veeam
Azure můžete využít k poskytování úložiště záloh a cíle úložiště pro dlouhodobé zálohování a archivaci. Veškerý provoz v síti zálohování mezi virtuálními počítači v privátním cloudu a úložištěm záloh v Azure se pohybuje přes připojení s vysokou šířkou pásma a nízkou latencí. Provoz replikace napříč oblastmi cestuje přes interní síť backplane Azure, což snižuje náklady na šířku pásma pro uživatele.

**Základní nasazení**

Pro prostředí s méně než 30 TB zálohovat CloudSimple doporučuje následující konfiguraci:

* Záložní server Veeam a proxy server nainstalovaný na stejném virtuálním počítači v privátním cloudu.
* Primární úložiště záloh založené na Linuxu v Azure nakonfigurované jako cíl pro úlohy zálohování.
* `azcopy`slouží ke kopírování dat z úložiště primární zálohy do kontejneru objektů blob Azure, který je replikován do jiné oblasti.

![Základní scénáře nasazení](media/veeam-basicdeployment.png)

**Pokročilé nasazení**

Pro prostředí s více než 30 TB zálohovat CloudSimple doporučuje následující konfiguraci:

* Jeden proxy server na uzel v clusteru vSAN, jak doporučuje veeam.
* Úložiště primárního zálohování založeného na systému Windows v privátním cloudu ukládá pět dní dat do mezipaměti pro rychlé obnovení.
* Úložiště záloh Linuxu v Azure jako cíl pro úlohy záložní kopie pro delší dobu uchovávání. Toto úložiště by mělo být nakonfigurováno jako úložiště zálohování horizontálním navýšením kapacity.
* `azcopy`slouží ke kopírování dat z úložiště primární zálohy do kontejneru objektů blob Azure, který je replikován do jiné oblasti.

![Základní scénáře nasazení](media/veeam-advanceddeployment.png)

Na předchozím obrázku všimněte si, že záložní proxy je virtuální počítač s Hot Přidat přístup k disky virtuálního počítače zatížení na vSAN úložiště dat. Veeam používá pro vSAN režim přenosu záložního proxy serveru Virtual Appliance.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Požadavky na řešení Veeam v CloudSimple

Řešení Veeam vyžaduje následující kroky:

* Poskytněte vlastní licence Veeam.
* Nasazujte a spravujte Veeam a zálohujte úlohy spuštěné v privátním cloudu CloudSimple.

Toto řešení vám poskytuje plnou kontrolu nad nástrojem pro zálohování Veeam a nabízí možnost použít nativní rozhraní Veeam nebo modul plug-in Veeam vCenter pro správu úloh zálohování virtuálních her.

Pokud jste stávajícím uživatelem veeamu, můžete část o součástech řešení Veeam přeskočit a přímo přejít k [scénářům nasazení veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instalace a konfigurace záloh Veeam ve vašem cloudu CloudSimple Private Cloud

Následující části popisují, jak nainstalovat a nakonfigurovat řešení zálohování Veeam pro váš CloudSimple Private Cloud.

Proces nasazení se skládá z těchto kroků:

1. [Ui vCenter: Nastavení infrastrukturních služeb v privátním cloudu](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portál CloudSimple: Nastavení privátní sítě cloudu pro Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portál CloudSimple: Eskalovat oprávnění](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portál Azure: Připojení virtuální sítě k privátnímu cloudu](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure Portal: Vytvoření úložiště záloh v Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure Portal: Konfigurace úložiště objektů blob Azure pro dlouhodobé uchovávání dat](#configure-azure-blob-storage-for-long-term-data-retention)
7. [VCenter UI privátního cloudu: Instalace Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Konzole Veeam: Konfigurace softwaru Veeam Backup & Recovery](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portál CloudSimple: Nastavte oprávnění veeam pro přístup a deeskalaci](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Než začnete

Před zahájením nasazení veeamu je třeba provést následující:

* Předplatné Azure, které vlastníte
* Předem vytvořená skupina prostředků Azure
* Virtuální síť Azure ve vašem předplatném
* Účet úložiště Azure
* [Privátní cloud](create-private-cloud.md) vytvořený pomocí portálu CloudSimple.  

Během fáze implementace jsou zapotřebí následující položky:

* Šablony VMware pro Windows pro instalaci veeam (například Windows Server 2012 R2 - 64bitová bitová bitová bitová kopie)
* Jedna dostupná síť VLAN identifikovaná pro záložní síť
* CIDR podsítě, která má být přiřazena k záložní síti
* Instalovatelná média Veeam 9.5 u3 (ISO) nahraná do úložiště dat vSAN privátního cloudu

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>Ui vCenter: Nastavení infrastrukturních služeb v privátním cloudu

Nakonfigurujte služby infrastruktury v privátním cloudu, abyste mohli snadno spravovat své úlohy a nástroje.

* Můžete přidat externího zprostředkovatele identity, jak je popsáno v [části Nastavení zdrojů identit y vCenter, aby používal službu Active Directory,](set-vcenter-identity.md) pokud platí některá z následujících položek:

  * Chcete identifikovat uživatele z místní služby Active Directory (AD) ve vašem privátním cloudu.
  * Chcete nastavit službu AD v privátním cloudu pro všechny uživatele.
  * Chcete použít Azure AD.
* Chcete-li zajistit vyhledávání ADRES IP, správu IP adres a služby překladu názvů pro vaše úlohy v privátním cloudu, nastavte server DHCP a DNS, jak je popsáno v části [Nastavení aplikací a úloh DNS a DHCP ve vašem cloudu CloudSimple Private Cloud](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Nastavení privátní sítě cloudu pro Veeam

Přístup k portálu CloudSimple k nastavení privátní ho cloudové sítě pro řešení Veeam.

Vytvořte síť VLAN pro záložní síť a přiřaďte jí podsíť CIDR. Pokyny naleznete v [tématu Vytvoření a správa sítí VLAN/podsítí](create-vlan-subnet.md).

Vytvořte pravidla brány firewall mezi podsítí pro správu a záložní sítí, abyste povolili síťový provoz na portech používaných společností Veeam. Podívejte se na téma Veeam [Použité porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Pokyny k vytváření pravidel brány firewall naleznete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

V následující tabulce je uveden seznam portů.

| Ikona | Popis | Ikona | Popis |
| ------------ | ------------- | ------------ | ------------- |
| Záložní server  | vCenter  | HTTPS / TCP  | 443 |
| Záložní server <br> *Vyžadováno pro nasazení součástí veeam backup & replication* | Záložní proxy server  | Protokol TCP/UDP  | 135, 137 až 139 a 445 |
    | Záložní server   | DNS  | UDP  | 53  | 
    | Záložní server   | Server oznámení o aktualizaci Veeam  | TCP  | 80  | 
    | Záložní server   | Server aktualizace licencí Veeam  | TCP  | 443  | 
    | Záložní proxy server   | vCenter |   |   | 
    | Záložní proxy server  | Úložiště záloh Linuxu   | TCP  | 22  | 
    | Záložní proxy server  | Úložiště záloh systému Windows  | TCP  | 49152 - 65535   | 
    | Úložiště záloh  | Záložní proxy server  | TCP  | 2500 -5000  | 
    | Úložiště zálohování zdroje<br> *Používá se pro úlohy záložní kopie*  | Úložiště cílové zálohy  | TCP  | 2500 - 5000  | 

Vytvořte pravidla brány firewall mezi podsítí pracovního vytížení a záložní sítí, jak je popsáno v [části Nastavení tabulek brány firewall a pravidel](firewall.md).  Pro zálohování a obnovení s vědomím aplikace musí být [otevřeny další porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) na virtuálních počítačích zatížení, které hostují konkrétní aplikace.

Ve výchozím nastavení cloudsimple poskytuje odkaz 1Gbps ExpressRoute. U větších velikostí prostředí může být vyžadováno připojení s vyšší šířkou pásma. Další informace o propojeních s vyšší šířkou pásma získáte od podpory Azure.

Chcete-li pokračovat v nastavení, budete potřebovat autorizační klíč a identifikátor URI okruhu a přístup k předplatnému Azure.  Tyto informace jsou k dispozici na stránce Připojení k virtuální síti na portálu CloudSimple. Pokyny najdete v [tématu Získání informací o partnerské síti pro virtuální síť Azure připojení CloudSimple](virtual-network-connection.md). Máte-li potíže se získáním informací, [obraťte se](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)na podporu .

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: Eskalovat oprávnění pro vlastníka cloudu

Výchozí uživatel vlastníka cloudu nemá v centru private cloudu dostatečná oprávnění k instalaci veeam, takže oprávnění vcenter uživatele musí být eskalována. Další informace naleznete v tématu [Escalate privileges](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portál Azure: Připojení virtuální sítě k privátnímu cloudu

Připojte virtuální síť k privátnímu cloudu podle pokynů v [aplikaci Azure Virtual Network Connection pomocí ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portál Azure: Vytvoření virtuálního počítače úložiště záloh

1. Vytvořte standardní virtuální virtuální zařízení D2 v3 s (2 virtuální procesory a 8 GB paměti).
2. Vyberte obrázek založený na CentOS 7.4.
3. Konfigurace skupiny zabezpečení sítě (NSG) pro virtuální počítače. Ověřte, že virtuální hod nemá veřejnou IP adresu a není dostupný z veřejného internetu.
4. Vytvořte uživatelské jméno a uživatelské jméno na základě hesla pro nový virtuální počítače. Pokyny najdete [v tématu Vytvoření virtuálního počítače s Linuxem na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md).
5. Vytvořte 1x512 GiB standardní HDD a připojte ho k virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu virtuálnímu mísu úložiště.  Pokyny najdete v [tématu Jak připojit spravovaný datový disk k virtuálnímu počítači s Windows na webu Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Vytvořte svazek XFS na spravovaném disku](https://www.digitalocean.com/docs/volumes/how-to/). Přihlaste se k virtuálnímu virtuálnímu mísu pomocí výše uvedených přihlašovacích údajů. Spusťte následující skript pro vytvoření logického svazku, přidejte do něj disk, vytvořte [oddíl](https://www.digitalocean.com/docs/volumes/how-to/partition/) souborového systému XFS a [připojte](https://www.digitalocean.com/docs/volumes/how-to/mount/) oddíl pod cestou /backup1.

    Ukázkový skript:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Vystavit /backup1 jako přechodový bod nfs na záložní server Veeam, který běží v privátním cloudu. Pokyny naleznete v článku Digital Ocean [How To Set Up an NFS Mount on CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Tento název sdílené položky systému souborů NFS použijte při konfiguraci úložiště záloh na záložním serveru Veeam.

8. Nakonfigurujte pravidla filtrování v souboru zabezpečení sítě pro virtuální počítače úložiště zálohování explicitně povolit veškerý síťový provoz do a z virtuálního počítače.

> [!NOTE]
> Veeam Backup & Replication používá protokol SSH ke komunikaci s úložišti záloh Linuxu a vyžaduje nástroj SCP v úložištích Linuxu. Ověřte, zda je daemon SSH správně nakonfigurován a zda je scp k dispozici na hostiteli Linuxu.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurace úložiště objektů blob Azure pro dlouhodobé uchovávání dat

1. Vytvořte účet úložiště pro obecné účely (GPv2) standardního typu a kontejner objektů blob, jak je popsáno v Microsoft video [Začínáme s Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Vytvořte kontejner úložiště Azure, jak je popsáno v odkazu [Vytvořit kontejner.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Stáhněte `azcopy` si nástroj příkazového řádku pro Linux od společnosti Microsoft. V prostředí bash můžete použít následující příkazy v centos7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Pomocí `azcopy` příkazu zkopírujte záložní soubory do kontejneru objektů blob a z nich.  Podrobné příkazy najdete v [tématu Přenos dat pomocí AzCopy v Linuxu.](../storage/common/storage-use-azcopy-linux.md)

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>konzola vCenter privátního cloudu: Nainstalujte veeam B&R

Přístup k programu vCenter ze svého privátního cloudu a vytvořte si účet služby Veeam, nainstalujte veeam B&R 9.5 a nakonfigurujte veeam pomocí účtu služby.

1. Vytvořte novou roli s názvem Role zálohování Veeam a přiřaďte jí potřebná oprávnění podle doporučení společnosti Veeam. Podrobnosti naleznete v tématu Veeam [Required Permissions](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Vytvořte novou skupinu veeam ových uživatelů ve službě vCenter a přiřaďte jí roli zálohování Veeam.
3. Vytvořte si nového uživatele služby Veeam a přidejte jej do skupiny uživatelů Veeam.

    ![Vytvoření účtu služby Veeam](media/veeam-vcenter01.png)

4. Vytvořte skupinu distribuovaných portů v programu vCenter pomocí záložní sítě VLAN. Podrobnosti naleznete ve videu VMware [Creating a Distributed Port Group ve webovém klientovi vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Vytvořte virtuální počítače pro záložní a proxy servery Veeam v programu ve vCenter podle [požadavků systému Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Můžete použít Windows 2012 R2 nebo Linux. Další informace naleznete [v tématu Požadavky na používání úložišť záloh linuxu](https://www.veeam.com/kb2216).
6. Namontujte instalovatelný veeam ISO jako cdrom zařízení do virtuálního počítače záložního serveru Veeam.
7. Pomocí relace RDP do počítače se systémem Windows 2012 R2 (cíl pro instalaci veeamu) [nainstalujte veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) do virtuálního počítače Windows 2012 R2.
8. Najděte interní IP adresu virtuálního počítače záložního serveru Veeam a nakonfigurujte adresu IP na serveru DHCP jako statickou. Přesné kroky, které jsou k tomu nutné, závisí na serveru DHCP. Například <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statické mapování DHCP</a> v článku Netgate vysvětluje, jak nakonfigurovat server DHCP pomocí směrovače pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Konzole Veeam: Instalace softwaru pro zálohování a obnovu Veeam

Pomocí konzoly Veeam nakonfigurujte software pro zálohování a obnovu veeam. Podrobnosti naleznete [v tématu Veeam Backup & Replication v9 – Instalace a nasazení](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Přidejte VMware vSphere jako prostředí spravovaného serveru. Po zobrazení výzvy zadejte přihlašovací údaje k účtu služby Veeam, který jste vytvořili na začátku [konzoly vCenter Private Cloud: Nainstalujte veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Pro řízení zatížení a výchozí upřesňující nastavení použijte výchozí nastavení.
    * Nastavte umístění přípojného serveru jako záložní server.
    * Změňte umístění zálohy konfigurace serveru Veeam na vzdálené úložiště.

2. Přidejte linuxový server do Azure jako úložiště záloh.

    * Výchozí nastavení použijte pro řízení zatížení a pro upřesňující nastavení. 
    * Nastavte umístění přípojného serveru jako záložní server.
    * Změňte umístění zálohy konfigurace serveru Veeam na vzdálené úložiště.

3. Povolte šifrování zálohy konfigurace pomocí **nastavení zálohování konfigurace home>**.

4. Přidejte virtuální hod serveru Windows jako proxy server pro prostředí VMware. Pomocí 'Pravidla provozu' pro proxy, šifrovat záložní data přes drát.

5. Konfigurace úloh zálohování.
    * Chcete-li konfigurovat úlohy zálohování, postupujte podle pokynů v [části Vytvoření úlohy zálohování](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Povolte šifrování záložních souborů v části **Upřesnit nastavení > úložiště**.

6. Konfigurace úloh záložní kopie.

    * Chcete-li konfigurovat úlohy záložní kopie, postupujte podle pokynů ve videu [Vytvoření úlohy záložní kopie](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Povolte šifrování záložních souborů v části **Upřesnit nastavení > úložiště**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portál CloudSimple: Nastavte oprávnění veeam pro přístup a deeskalaci
Vytvořte veřejnou IP adresu pro záložní a obnovovací server Veeam. Pokyny naleznete v tématu [Přidělení veřejných IP adres](public-ips.md).

Vytvořte pravidlo brány firewall, které umožní záložnímu serveru Veeam vytvořit odchozí připojení k webu Veeam pro stahování aktualizací/oprav na port UCP 80. Pokyny naleznete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

Chcete-li de-eskalovat oprávnění, naleznete [v tématu De-escalate oprávnění](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Odkazy

### <a name="cloudsimple-references"></a>CloudSimple odkazy

* [Vytvoření privátního cloudu](create-private-cloud.md)
* [Vytvoření a správa sítí VLAN/podsítí](create-vlan-subnet.md)
* [vCenter Zdroje identit](set-vcenter-identity.md)
* [Nastavení dns a DHCP zatížení](dns-dhcp-setup.md)
* [Zvýšení úrovně oprávnění](escalate-privileges.md)
* [Nastavení pravidel a tabulek bran firewall](firewall.md)
* [Oprávnění privátního cloudu](learn-private-cloud-permissions.md)
* [Přidělení veřejných IP adres](public-ips.md)

### <a name="veeam-references"></a>Veeam Reference

* [Použité porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Požadovaná oprávnění](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Systémové požadavky](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalace replikace zálohování veeam &](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Požadované moduly a oprávnění pro podporu multi-OS FLR a repozitáře pro Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replikace v9 – Instalace a nasazení – video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Vytvoření zálohovací úlohy – video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Vytvoření úlohy záložní kopie – video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Odkazy azure

* [Konfigurace brány virtuální sítě pro ExpressRoute pomocí portálu Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Připojení virtuální sítě k okruhu – jiné předplatné](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Vytvoření virtuálního počítače s Linuxem na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Jak připojit spravovaný datový disk k virtuálnímu počítači s Windows na webu Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Začínáme s Azure Storage – video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Vytvoření kontejneru](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Přenos dat pomocí AzCopy v Linuxu](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Odkazy v vmware

* [Vytvoření skupiny distribuovaných portů ve webovém klientovi vSphere – video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Další odkazy

* [Vytvoření svazku XFS na spravovaném disku – RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Jak nastavit NFS Mount na CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurace serveru DHCP – Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
