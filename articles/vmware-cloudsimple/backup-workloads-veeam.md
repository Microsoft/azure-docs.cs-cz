---
title: Řešení Azure VMware podle CloudSimple – zálohování virtuálních počítačů s úlohami v privátním cloudu pomocí Veeam
description: Popisuje, jak můžete zálohovat virtuální počítače, které běží v privátním cloudu CloudSimple založeném na Azure pomocí Veeam B&R 9,5.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899333"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Zálohování virtuálních počítačů s úlohami v privátním cloudu CloudSimple pomocí Veeam B&R

Tato příručka popisuje, jak můžete zálohovat virtuální počítače, které běží v privátním cloudu CloudSimple založeném na Azure, pomocí Veeam B&R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Řešení zálohování a obnovení Veeam

Řešení Veeam zahrnuje následující součásti.

**Záložní server**

Záložní server je Windows Server (VM), který slouží jako řídicí centrum pro Veeam a provádí tyto funkce: 

* Koordinuje úlohy zálohování, replikace, ověřování obnovení a obnovení.
* Řídí plánování úloh a přidělování prostředků.
* Umožňuje nastavit a spravovat komponenty infrastruktury zálohování a zadat globální nastavení pro infrastrukturu zálohování.

**Proxy servery**

Mezi záložním serverem a dalšími součástmi infrastruktury zálohování se nainstalují proxy servery. Spravují následující funkce:

* Načtení dat virtuálního počítače z produkčního úložiště
* Komprese
* Odstranění duplicit
* Šifrování
* Přenos dat do úložiště záloh

**Úložiště záloh**

Úložiště zálohování je umístění úložiště, ve kterém Veeam udržuje záložní soubory, kopie virtuálních počítačů a metadata replikovaných virtuálních počítačů.  Úložiště může být server s Windows nebo Linux s místními disky (nebo připojeným systémem souborů NFS/SMB) nebo zařízením pro odstranění duplicitních dat v hardwarovém úložišti.

### <a name="veeam-deployment-scenarios"></a>Scénáře nasazení Veeam
Azure můžete využít k poskytnutí úložiště zálohování a cíle úložiště pro dlouhodobé zálohování a archivaci. Veškerá síťová přenosová data mezi virtuálními počítači v privátním cloudu a úložištěm záloh v Azure se přenáší přes propojení s velkou šířkou pásma a nízkou latencí. Provoz replikace mezi oblastmi se cestuje přes interní síť Azure pro replánování, která snižuje náklady na šířku pásma pro uživatele.

**Základní nasazení**

Pro prostředí, která mají k zálohování méně než 30 TB, CloudSimple doporučuje následující konfiguraci:

* Veeam Backup Server a proxy server nainstalovaná na stejném virtuálním počítači v privátním cloudu.
* Primární úložiště záloh založené na systému Linux v Azure nakonfigurované jako cíl pro úlohy zálohování.
* `azcopy` používá se ke kopírování dat z primárního úložiště záloh do kontejneru objektů BLOB v Azure, který se replikuje do jiné oblasti.

![Diagram, který znázorňuje základní scénáře nasazení Veeam.](media/veeam-basicdeployment.png)

**Pokročilé nasazení**

Pro prostředí, která mají víc než 30 TB pro zálohování, CloudSimple doporučuje následující konfiguraci:

* Jednu proxy server na uzel v clusteru síti vSAN, jak to doporučila Veeam.
* Primární úložiště záloh založené na Windows v privátním cloudu pro ukládání dat do mezipaměti pro rychlé obnovení.
* Záložní úložiště pro Linux v Azure jako cíl pro úlohy zálohování s delší dobou trvání. Toto úložiště by mělo být nakonfigurované jako úložiště zálohování se škálováním na více instancí.
* `azcopy` používá se ke kopírování dat z primárního úložiště záloh do kontejneru objektů BLOB v Azure, který se replikuje do jiné oblasti.

![Základní scénáře nasazení](media/veeam-advanceddeployment.png)

Na předchozím obrázku si všimněte, že záložní proxy je virtuální počítač s nástrojem Hot Add Access pro úlohy virtuálních počítačů na síti vSAN úložiště dat. Veeam používá transportní režim proxy serveru pro zálohování virtuálních zařízení pro síti vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Požadavky na řešení Veeam na CloudSimple

Řešení Veeam vyžaduje, abyste provedete následující kroky:

* Zadejte vlastní licence Veeam.
* Nasaďte a spravujte Veeam a zálohujte úlohy spuštěné v privátním cloudu CloudSimple.

Toto řešení poskytuje úplnou kontrolu nad nástrojem Veeam Backup a nabízí možnost použít nativní rozhraní Veeam nebo modul plug-in Veeam vCenter ke správě úloh zálohování virtuálních počítačů.

Pokud jste existující uživatel Veeam, můžete přeskočit oddíl na součásti řešení Veeam a přímo přejít ke [scénářům nasazení Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instalace a konfigurace záloh Veeam v privátním cloudu CloudSimple

Následující části popisují, jak nainstalovat a nakonfigurovat řešení zálohování Veeam pro privátní cloud CloudSimple.

Proces nasazení se skládá z těchto kroků:

1. [uživatelské rozhraní vCenter: nastavení služby infrastruktury ve vašem privátním cloudu](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portál CloudSimple: nastavení sítě privátního cloudu pro Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portál CloudSimple: eskalace oprávnění](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure Portal: připojení virtuální sítě k privátnímu cloudu](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure Portal: vytvoření úložiště záloh v Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure Portal: konfigurace úložiště objektů BLOB v Azure pro dlouhodobé uchovávání dat](#configure-azure-blob-storage-for-long-term-data-retention)
7. [uživatelské rozhraní vCenter privátního cloudu: instalace Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Konzola Veeam: konfigurace softwaru Veeam Backup pro obnovení &](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portál CloudSimple: nastavení přístupu Veeam a oprávnění ke zrušení eskalace](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Než začnete

Než začnete Veeam nasazení, jsou potřeba následující:

* Předplatné Azure, které vlastníte
* Předem vytvořená skupina prostředků Azure
* Virtuální síť Azure ve vašem předplatném
* Účet úložiště Azure
* [Privátní cloud](create-private-cloud.md) vytvořený pomocí portálu CloudSimple.  

Během fáze implementace jsou potřeba tyto položky:

* Šablony VMware pro Windows k instalaci Veeam (například Windows Server 2012 R2 – 64 bitová kopie)
* Zjištěna jedna dostupná síť VLAN pro záložní síť
* CIDR podsítě, která se má přiřadit k síti zálohování
* Veeam 9,5 U3 installed Media (ISO) nahrané do úložiště dat síti vSAN privátního cloudu

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>uživatelské rozhraní vCenter: nastavení služby infrastruktury ve vašem privátním cloudu

Konfigurujte služby infrastruktury v privátním cloudu, abyste usnadnili správu úloh a nástrojů.

* Můžete přidat externího poskytovatele identity, jak je popsáno v části [Nastavení zdrojů identity vCenter na používání služby Active Directory](set-vcenter-identity.md) , pokud platí některá z následujících možností:

  * Chcete identifikovat uživatele z místní služby Active Directory (AD) ve vašem privátním cloudu.
  * Chcete nastavit službu AD v privátním cloudu pro všechny uživatele.
  * Chcete používat Azure AD.
* Pokud chcete pro vaše úlohy v privátním cloudu poskytnout vyhledávání IP adres, správu IP adres a služby překladu IP adres, nastavte server DHCP a DNS, jak je popsáno v tématu [nastavení aplikací DNS a DHCP a úloh v privátním cloudu CloudSimple](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>Privátní cloud CloudSimple: nastavení sítě privátního cloudu pro Veeam

Přístup k portálu CloudSimple k nastavení sítě privátního cloudu pro řešení Veeam.

Vytvořte síť VLAN pro záložní síť a přiřaďte ji k podsíti CIDR. Pokyny najdete v tématu [Vytvoření a správa sítí VLAN a podsítí](create-vlan-subnet.md).

Vytvořte pravidla brány firewall mezi podsítí pro správu a sítí zálohování, aby se povolil síťový provoz na portech, které používá Veeam. Přečtěte si téma Veeam [použité porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Pokyny k vytvoření pravidla brány firewall najdete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

V následující tabulce je uveden seznam portů.

| Ikona | Description | Ikona | Description |
| ------------ | ------------- | ------------ | ------------- |
| Záložní server  | vCenter  | PROTOKOL HTTPS/TCP  | 443 |
| Záložní server <br> *Vyžaduje se pro nasazení součástí Veeam Backup & pro replikaci.* | Záložní proxy server  | TCP/UDP  | 135, 137 až 139 a 445 |
    | Záložní server   | DNS  | UDP  | 53  | 
    | Záložní server   | Veeam aktualizace serveru oznámení  | TCP  | 80  | 
    | Záložní server   | Server aktualizace licence Veeam  | TCP  | 443  | 
    | Záložní proxy server   | vCenter |   |   | 
    | Záložní proxy server  | Záložní úložiště pro Linux   | TCP  | 22  | 
    | Záložní proxy server  | Úložiště záloh Windows  | TCP  | 49152 – 65535   | 
    | Úložiště záloh  | Záložní proxy server  | TCP  | 2500 – 5000  | 
    | Zdrojové úložiště záloh<br> *Používá se pro úlohy záložního kopírování.*  | Cílové úložiště zálohování  | TCP  | 2500 – 5000  | 

Vytvořte pravidla brány firewall mezi podsítí úloh a záložní sítí, jak je popsáno v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).  Pro zálohování a obnovení, které podporuje aplikace, je nutné otevřít [Další porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) na virtuálních počítačích úloh, které hostují konkrétní aplikace.

Ve výchozím nastavení CloudSimple poskytuje odkaz 1Gb/s ExpressRoute. U větších velikostí prostředí se může vyžadovat odkaz na větší šířku pásma. Pokud chcete získat další informace o propojeních s větší šířkou pásma, kontaktujte podporu Azure.

Abyste mohli pokračovat v instalaci, budete potřebovat autorizační klíč a identifikátor URI partnerského okruhu a přístup k vašemu předplatnému Azure.  Tyto informace jsou k dispozici na stránce Virtual Network připojení na portálu CloudSimple. Pokyny najdete v tématu [získání informací o partnerském vztahu pro službu Azure Virtual Network do CloudSimple připojení](virtual-network-connection.md). Pokud máte potíže s získáním informací, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>Privátní cloud CloudSimple: zvýšení oprávnění pro cloudowner

Výchozí uživatel cloudowner nemá dostatečná oprávnění v privátním cloudu vCenter pro instalaci VEEAM, takže musí být namířená oprávnění pro vCenter uživatele. Další informace najdete v tématu [Eskalace oprávnění](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure Portal: připojení virtuální sítě k privátnímu cloudu

Připojte virtuální síť k privátnímu cloudu podle pokynů v tématu [připojení k Azure Virtual Network pomocí nástroje ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: Vytvoření virtuálního počítače úložiště zálohování

1. Vytvořte standardní virtuální počítač D2 v3 s (2 vCPU a 8 GB paměti).
2. Vyberte bitovou kopii založenou na CentOS 7,4.
3. Nakonfigurujte skupinu zabezpečení sítě (NSG) pro virtuální počítač. Ověřte, že virtuální počítač nemá veřejnou IP adresu a není dosažitelný z veřejného Internetu.
4. Vytvořte uživatelský účet založený na uživatelských jménech a hesle pro nový virtuální počítač. Pokyny najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure Portal](../virtual-machines/linux/quick-create-portal.md).
5. Vytvořte 1x512 GiB Standard HDD a připojte ho k virtuálnímu počítači úložiště.  Pokyny najdete v tématu [Postup připojení spravovaného datového disku k virtuálnímu počítači s Windows v Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Vytvořte na spravovaném disku svazek XFS](https://www.digitalocean.com/docs/volumes/how-to/). Přihlaste se k virtuálnímu počítači pomocí dříve uvedených přihlašovacích údajů. Spuštěním následujícího skriptu vytvořte logický svazek, přidejte do něj disk, vytvořte [oddíl](https://www.digitalocean.com/docs/volumes/how-to/partition/) systému souborů XFS a [Připojte](https://www.digitalocean.com/docs/volumes/how-to/mount/) oddíl pod cestu/Backup1.

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

7. Zveřejněte/Backup1 jako přípojný bod systému souborů NFS na záložní server Veeam, který běží v privátním cloudu. Pokyny najdete v článku o digitálním oceánu, [jak nastavit připojení NFS na CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Tento název sdílené složky systému souborů NFS použijte při konfiguraci úložiště zálohování na serveru Veeam Backup.

8. Nakonfigurujte pravidla filtrování v NSG pro virtuální počítač úložiště zálohování tak, aby explicitně povolovala veškerý síťový provoz do a z virtuálního počítače.

> [!NOTE]
> Veeam Backup & replikace používá protokol SSH ke komunikaci s úložišti záloh Linux a vyžaduje nástroj SCP v úložištích se systémem Linux. Ověřte, zda je démon procesu SSH správně nakonfigurován a zda je spojovací bod služby k dispozici na hostiteli se systémem Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurace úložiště objektů BLOB v Azure pro dlouhodobé uchovávání dat

1. Vytvořte účet úložiště pro obecné účely (GPv2) standardního typu a kontejner objektů blob, jak je popsáno v části Microsoft video [Začínáme with Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Vytvořte kontejner úložiště Azure, jak je popsáno v tématu [Vytvoření](/rest/api/storageservices/create-container) odkazu na kontejner.
2. Stáhněte si `azcopy` Nástroj příkazového řádku pro Linux od Microsoftu. V prostředí bash v CentOS 7,5 můžete použít následující příkazy.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Pomocí `azcopy` příkazu zkopírujte záložní soubory do kontejneru objektů BLOB a z něj.  Podrobné příkazy najdete v tématu [přenos dat pomocí AzCopy v systému Linux](../storage/common/storage-use-azcopy-v10.md) .

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>Konzola vCenter pro privátní cloud: Nainstalujte Veeam B&R

Přístup k vCenter z vašeho privátního cloudu pro vytvoření účtu služby Veeam, nainstalujte Veeam B&R 9,5 a nakonfigurujte Veeam pomocí účtu služby.

1. Vytvořte novou roli s názvem Veeam Backup role a přiřaďte jí potřebná oprávnění, která doporučila Veeam. Podrobnosti najdete v tématu Veeam s [požadovanými oprávněními](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Vytvořte novou skupinu Veeam User Group v vCenter a přiřaďte ji k roli zálohování Veeam.
3. Vytvořte nového uživatele účtu služby Veeam a přidejte ho do skupiny uživatelů Veeam.

    ![Vytvoření účtu služby Veeam](media/veeam-vcenter01.png)

4. Vytvořte distribuovanou skupinu portů v vCenter pomocí sítě VLAN pro zálohování. Podrobnosti najdete v části video VMware [Vytvoření distribuované skupiny portů ve webovém klientovi vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Vytvořte virtuální počítače pro zálohování Veeam a proxy servery v vCenter podle [požadavků na systém Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Můžete použít systém Windows 2012 R2 nebo Linux. Další informace najdete v tématu [požadavky na používání úložišť záloh pro Linux](https://www.veeam.com/kb2216).
6. Připojte instalovatelný Veeam ISO jako zařízení CD na virtuálním počítači záložního serveru Veeam.
7. Pomocí relace RDP na počítač s Windows 2012 R2 (cíl pro instalaci Veeam) [nainstalujte Veeam B&R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) do virtuálního počítače s Windows 2012 R2.
8. Najděte interní IP adresu virtuálního počítače Veeam Backup serveru a na serveru DHCP nakonfigurujte IP adresu na statickou. Přesný postup, který je nutný k tomu, závisí na serveru DHCP. Příklad: <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statická mapování DHCP</a> v článku NETGATE popisují, jak nakonfigurovat server DHCP pomocí směrovače pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Konzola Veeam: instalace softwaru pro zálohování a obnovení Veeam

Pomocí konzoly Veeam nakonfigurujte software pro zálohování a obnovení Veeam. Podrobnosti najdete v tématu [Veeam Backup & Replication v9-Installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Přidejte VMware vSphere jako prostředí spravovaného serveru. Po zobrazení výzvy zadejte přihlašovací údaje k účtu služby Veeam, který jste vytvořili na začátku [konzoly vCenter v privátním cloudu: Nainstalujte Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Použijte výchozí nastavení pro řízení zatížení a výchozí upřesňující nastavení.
    * Nastavte umístění serveru připojení na záložní server.
    * Změňte umístění zálohy konfigurace serveru Veeam na vzdálené úložiště.

2. Jako úložiště záloh přidejte server Linux do Azure.

    * Použijte výchozí nastavení pro řízení zatížení a pro pokročilá nastavení. 
    * Nastavte umístění serveru připojení na záložní server.
    * Změňte umístění zálohy konfigurace serveru Veeam na vzdálené úložiště.

3. Povolte šifrování zálohy konfigurace pomocí **nastavení zálohování konfigurace pro domovskou>**.

4. Přidejte virtuální počítač s Windows serverem jako proxy server pro prostředí VMware. Pomocí pravidel přenosů pro proxy server Zašifrujte zálohovaná data prostřednictvím tohoto drátu.

5. Nakonfigurujte úlohy zálohování.
    * Pokud chcete nakonfigurovat úlohy zálohování, postupujte podle pokynů v [tématu Vytvoření úlohy zálohování](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * V části **Upřesnit nastavení > úložiště** Povolte šifrování záložních souborů.

6. Nakonfigurujte úlohy zálohování kopírováním.

    * Pokud chcete nakonfigurovat úlohy zálohování, postupujte podle pokynů ve videu [Vytvoření úlohy zálohování při kopírování](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * V části **Upřesnit nastavení > úložiště** Povolte šifrování záložních souborů.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portál CloudSimple: nastavení přístupu Veeam a oprávnění ke zrušení eskalace
Vytvořte veřejnou IP adresu pro Veeam Backup and Recovery Server. Pokyny najdete v tématu [přidělování veřejných IP adres](public-ips.md).

Vytvořte pravidlo brány firewall pomocí nástroje, aby server Veeam Backup mohl vytvořit odchozí připojení k webu Veeam pro stahování aktualizací nebo oprav na portu TCP 80. Pokyny najdete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

Chcete-li zrušit oprávnění, přečtěte si téma oprávnění ke zrušení [Eskalace](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Reference

### <a name="cloudsimple-references"></a>Odkazy na CloudSimple

* [Vytvoření privátního cloudu](create-private-cloud.md)
* [Vytváření a správa sítí VLAN a podsítí](create-vlan-subnet.md)
* [Zdroje identity vCenter](set-vcenter-identity.md)
* [Nastavení služby DNS a DHCP pro úlohy](dns-dhcp-setup.md)
* [Zvýšení úrovně oprávnění](escalate-privileges.md)
* [Nastavení pravidel a tabulek bran firewall](firewall.md)
* [Oprávnění privátního cloudu](learn-private-cloud-permissions.md)
* [Přidělit veřejné IP adresy](public-ips.md)

### <a name="veeam-references"></a>Odkazy na Veeam

* [Používané porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Požadovaná oprávnění](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Požadavky na systém](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalace Veeam Backup & replikace](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Požadované moduly a oprávnění pro FLR a podporu úložiště s více operačními systémy pro Linux](https://www.veeam.com/kb2216)
* [Veeam zálohování & replikace v9 – instalace a nasazení – video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 vytváření úlohy zálohování – video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 vytvoření úlohy záložního kopírování – video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Odkazy na Azure

* [Konfigurace brány virtuální sítě pro ExpressRoute pomocí Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Připojení virtuální sítě k okruhu jiné předplatné](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Vytvoření virtuálního počítače se systémem Linux v Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Postup připojení spravovaného datového disku k virtuálnímu počítači s Windows v Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Začínáme s využitím Azure Storage-video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Vytvoření kontejneru](/rest/api/storageservices/create-container)
* [Přenos dat pomocí AzCopy v Linuxu](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>Reference VMware

* [Vytvoření distribuované skupiny portů ve webovém klientovi vSphere – video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Další odkazy

* [Vytvoření svazku XFS na spravovaném disku – RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Jak nastavit připojení NFS na CentOS 7 – HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurace serveru DHCP – NETGATE](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
