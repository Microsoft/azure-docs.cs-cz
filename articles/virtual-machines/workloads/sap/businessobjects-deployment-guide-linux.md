---
title: Nasazení platformy SAP BusinessObjects BI na platformě Azure pro Linux | Microsoft Docs
description: Nasazení a konfigurace platformy SAP BusinessObjects BI v Azure pro Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505932"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Průvodce nasazením platformy SAP BusinessObjects BI pro Linux v Azure

Tento článek popisuje strategii nasazení platformy SAP BOBI v Azure pro Linux. V tomto příkladu jsou nakonfigurovány dva virtuální počítače s SSD úrovně Premium Managed Disks jako instalační adresář. Azure Database for MySQL se používá pro databázi CMS a v obou serverech se sdílí Azure NetApp Files pro server úložiště souborů. Výchozí Tomcat webová aplikace v jazyce Java a aplikace BI Platform jsou nainstalovány společně na obou virtuálních počítačích. K vyrovnávání zatížení požadavku uživatele se používá Application Gateway, který má nativní možnosti snižování zátěže TLS/SSL.

Tento typ architektury je platný pro malé nasazení nebo pro jiné než produkční prostředí. V produkčním nebo rozsáhlém nasazení můžete mít samostatné hostitele pro webovou aplikaci a můžou mít i několik BOBIch aplikací, které umožňují serveru zpracovávat další informace.

![Nasazení SAP BOBI v Azure pro Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

V tomto příkladu se používá verze produktu a rozložení systému souborů.

- Platforma SAP BusinessObjects 4,3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (verze: 8.0.15)
- MySQL C API Connector – libmysqlclient (verze: 6.1.11)

| Systém souborů        | Description                                                                                                               | Velikost (GB)             | Vlastník  | Group (Skupina)  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Systém souborů pro instalaci instance SAP BOBI, výchozí webové aplikace Tomcat a ovladače databáze (v případě potřeby) | Pokyny pro určení velikosti SAP | bl1adm | sapsys | Spravovaný disk úrovně Premium – SSD |
| /usr/sap/frsinput  | Adresář pro připojení je určený pro sdílené soubory ve všech hostitelích BOBI, které se použijí jako adresář úložiště vstupních souborů.  | Obchodní potřeby         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Přípojný adresář pro sdílené soubory ve všech hostitelích BOBI, které se použijí jako výstupní adresář úložiště souborů | Obchodní potřeby         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Nasazení virtuálního počítače se systémem Linux prostřednictvím Azure Portal

V této části vytvoříme pro platformu SAP BOBI dva virtuální počítače s bitovou kopií operačního systému Linux. Postup vytvoření Virtual Machines vysoké úrovně je následující:

1. Vytvoření [skupiny prostředků](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Vytvořte [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Pro všechny služby Azure v nasazení platformy SAP BI Nepoužívejte jednu podsíť. V závislosti na architektuře platforem SAP BI potřebujete vytvořit několik podsítí. V tomto nasazení vytvoříme tři podsítě – podsíť aplikace, podsíť úložiště úložiště souborů a podsíť Application Gateway.
   - V Azure musí být Application Gateway a Azure NetApp Files vždy v samostatné podsíti. Další podrobnosti najdete v tématu Application Gateway a [pokyny pro Azure NetApp Files plánování sítě](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) v [Azure](../../../application-gateway/configuration-overview.md) .

3. Vytvořte skupinu dostupnosti.

   - Pro zajištění redundance pro každou vrstvu nasazení s více instancemi umístěte virtuální počítače pro každou vrstvu do skupiny dostupnosti. Ujistěte se, že oddělíte skupiny dostupnosti pro každou vrstvu na základě vaší architektury.

4. Vytvořte virtuální počítač 1 **(azusbosl1).**

   - Můžete buď použít vlastní image, nebo zvolit obrázek z Azure Marketplace. Další informace najdete v tématu [nasazení virtuálního počítače z Azure Marketplace pro SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) nebo [nasazení virtuálního počítače s vlastní imagí pro SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) podle svých potřeb.

5. Vytvořte virtuální počítač 2 **(azusbosl2).**
6. Přidejte jeden SSD úrovně Premium disk. Použije se jako instalační adresář SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Zřídit Azure NetApp Files

Než budete pokračovat v instalaci pro Azure NetApp Files, Seznamte se s dokumentaci k [souborům Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files je k dispozici v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Podívejte se, jestli vybraná oblast Azure nabízí Azure NetApp Files.

Pomocí stránky [Azure NetApp Files dostupnost podle oblasti Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) zkontrolujete dostupnost Azure NetApp Files podle oblasti.

Než nasadíte Azure NetApp Files, požádejte o [registraci pro Azure NetApp Files a počkejte](../../../azure-netapp-files/azure-netapp-files-register.md) na jejich Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure NetApp Files

V následujících pokynech se předpokládá, že jste už nasadili službu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Azure NetApp Files prostředky a virtuální počítače, kde budou připojené prostředky Azure NetApp Files, musí být nasazené ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem.

1. Pokud jste ještě neimplementovali prostředky, požádejte o [registraci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Podle pokynů v části [Vytvoření účtu NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)vytvořte ve vybrané oblasti Azure účet NetApp.

3. Nastavte fond kapacit Azure NetApp Files podle pokynů v části [nastavení fondu kapacity Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - Architektura platforem SAP BI uvedená v tomto článku používá jeden Azure NetApp Files fond kapacity na úrovni služby *Premium* . Pro server úložiště souborů SAP BI v Azure doporučujeme použít Azure NetApp Files [úrovně služeb](../../../azure-netapp-files/azure-netapp-files-service-levels.md) *Premium* nebo *Ultra* .

4. Delegování podsítě na Azure NetApp Files, jak je popsáno v pokynech [delegování podsítě na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Nasaďte Azure NetApp Files svazky podle pokynů v tématu [vytvoření svazku NFS pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   Svazek ANF se dá nasadit jako NFSv3 a NFSv 4.1, protože oba protokoly se podporují pro platformu SAP BOBI. Nasaďte svazky v příslušné Azure NetApp Files podsíti. IP adresy svazků Azure NetApp se přiřazují automaticky.

Pamatujte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure s partnerským vztahem. Například azusbobi-frsinput, azusbobi-frsoutput jsou názvy svazků a nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput jsou cesty k souborům pro Azure NetApp Files svazky.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Důležité informace

Při vytváření Azure NetApp Files pro server úložiště souborů platformy SAP BOBI je potřeba vzít v úvahu následující skutečnosti:

1. Minimální fond kapacit je 4 tebibytes (TiB).
2. Minimální velikost svazku je 100 gibibajtech (GiB).
3. Azure NetApp Files a všechny virtuální počítače, na kterých budou připojené Azure NetApp Files svazky, musí být ve stejné virtuální síti Azure nebo v [partnerských virtuálních sítích](../../../virtual-network/virtual-network-peering-overview.md) ve stejné oblasti. V současné době se podporuje Azure NetApp Files přístup přes partnerský vztah virtuálních sítí ve stejné oblasti. Přístup k Azure NetApp přes globální partnerský vztah se ještě nepodporuje.
4. Vybraná virtuální síť musí mít podsíť, která je delegována na Azure NetApp Files.
5. Pomocí Azure NetApp Files [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)můžete řídit povolené klienty, typ přístupu (jen pro čtení i zápis, jen pro čtení atd.).
6. Funkce Azure NetApp Files ještě nezohledňují zóny. V současné době není tato funkce nasazena ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.
7. Azure NetApp Files svazky lze nasadit jako svazky NFSv3 nebo NFSv 4.1. Pro aplikace SAP BI Platform jsou podporovány oba protokoly.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurace systémů souborů na serverech se systémem Linux

Kroky v této části používají následující předpony:

**[A]**: Tento krok platí pro všechny hostitele.

### <a name="format-and-mount-sap-file-system"></a>Formátování a připojení systému souborů SAP

1. **[A]** zobrazit seznam všech připojených disků

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** formát blokování zařízení pro/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** vytvoření adresáře pro připojení

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** získat UUID blokovaného zařízení

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** udržovat položku pro připojení systému souborů v/etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** připojení systému souborů

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Připojit Azure NetApp Files svazek

1. **[A]** vytvoření přípojných adresářů

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** konfigurace operačního systému klienta pro podporu připojení nfsv 4.1 **(platí pouze v případě použití nfsv 4.1)**

   Pokud používáte Azure NetApp Files svazky s protokolem NFSv 4.1, proveďte následující konfiguraci na všech virtuálních počítačích, Azure NetApp Files kde je potřeba připojit svazky NFSv 4.1.

   **Ověření nastavení domény systému souborů NFS**

   Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files  **defaultv4iddomain.com** a mapování je nastavené na **nikdo**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Nezapomeňte nastavit doménu systému souborů NFS v/etc/idmapd.conf na virtuálním počítači tak, aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **defaultv4iddomain.com**. Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se oprávnění k souborům na svazcích Azure NetApp, která jsou připojená k virtuálním počítačům, zobrazí jako nikdo.

   Ověřte `nfs4_disable_idmapping` . Měl by být nastaven na **Y**. Pokud chcete vytvořit adresářovou strukturu `nfs4_disable_idmapping` , kde se nachází, spusťte příkaz Mount. V/sys/modules nebudete moct ručně vytvořit adresář, protože přístup je vyhrazený pro jádro nebo ovladače.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** přidat položky připojení

   Pokud používáte NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Pokud používáte NFSv 4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** připojit svazky NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurace databáze CMS – Azure Database for MySQL

V této části najdete podrobné informace o tom, jak zřídit Azure Database for MySQL pomocí Azure Portal. Poskytuje taky pokyny, jak vytvořit CMS a auditovat databáze pro SAP BOBI Platform a uživatelský účet pro přístup k databázi.

Pokyny platí jenom v případě, že používáte Azure DB for MySQL. Další informace najdete v dokumentaci ke službě SAP nebo databázi pro příslušné databáze.

### <a name="create-an-azure-database-for-mysql"></a>Vytvoření Azure Database for MySQL

Přihlaste se k Azure Portal a postupujte podle kroků uvedených v této úvodní [příručce k Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Několik bodů, které se mají poznamenat při zřizování Azure Database for MySQL –

1. Vyberte stejnou oblast pro Azure Database for MySQL, kde běží aplikační servery platformy SAP BI.

2. Vyberte podporovanou verzi databáze založenou na [matrici dostupnosti produktů (pam) pro SAP BI](https://support.sap.com/pam) , která je specifická pro vaši verzi SAP BOBI. Řiďte se stejnými pokyny pro kompatibilitu, které jsou adresovány pro MySQL AB ve SAP PAM.

3. V části COMPUTE + úložiště vyberte **Konfigurovat Server** a vyberte odpovídající cenovou úroveň na základě toho, co chcete změnit velikost výstupu.

4. Automatické **zvětšování úložiště** je ve výchozím nastavení povolené. Mějte na paměti, že [úložiště](../../../mysql/concepts-pricing-tiers.md#storage) je možné škálovat jenom mimo provoz.

5. Ve výchozím nastavení je **Doba uchovávání dat** sedm dní, ale můžete ji [volitelně nakonfigurovat](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) až na 35 dní.

6. Zálohy Azure Database for MySQL jsou ve výchozím nastavení místně redundantní, takže pokud chcete zálohy serveru v geograficky redundantním úložišti, vyberte geograficky **redundantní** z **možností redundance zálohy**.

> [!NOTE]
> Změna [možností redundance zálohy](../../../mysql/concepts-backup.md#backup-redundancy-options) po vytvoření serveru se nepodporuje.

### <a name="configure-connection-security"></a>Konfigurace zabezpečení připojení

Ve výchozím nastavení je vytvořený server chráněný pomocí brány firewall a není veřejně přístupný. Pokud chcete poskytnout přístup k virtuální síti, ve které běží aplikační servery platformy SAP BI, postupujte podle následujících kroků:  

1. Přejděte na prostředky serveru v nabídce Azure Portal a vyberte možnost **zabezpečení připojení** z levé strany pro prostředek serveru.
2. Výběrem **Ano** **povolíte přístup ke službám Azure**.
3. V části pravidla virtuální **sítě vyberte Přidat existující virtuální síť**. Vyberte virtuální síť a podsíť aplikačního serveru platformy SAP BI. Také musíte poskytnout přístup k poli pro skok nebo k jiným serverům, ze kterého můžete připojit aplikaci [MySQL Workbench](../../../mysql/connect-workbench.md) k Azure Database for MySQL. K vytvoření CMS a auditu databáze se použije MySQL Workbench.
4. Po přidání virtuálních sítí vyberte **Uložit**.

### <a name="create-cms-and-audit-database"></a>Vytvoření databáze CMS a auditu

1. Stáhněte si a nainstalujte MySQL Workbench z [webu MySQL](https://dev.mysql.com/downloads/workbench/). Ujistěte se, že jste nainstalovali MySQL Workbench na server, který má přístup k Azure Database for MySQL.

2. Připojte se k serveru pomocí aplikace MySQL Workbench. Postupujte podle pokynů uvedených v tomto [článku](../../../mysql/connect-workbench.md#get-connection-information). Pokud je test připojení úspěšný, zobrazí se následující zpráva:

   ![Připojení k SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Na kartě dotaz SQL spusťte následující dotaz a vytvořte schéma pro CMS a audit databáze.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Vytvoření uživatelského účtu pro připojení ke schématu

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Postup kontroly oprávnění a rolí uživatelského účtu MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Instalace konektoru MySQL C API (libmysqlclient) na server Linux

Aby aplikační Server SAP BOBI měl přístup k databázi, vyžaduje databázového klienta nebo ovladače. Pro přístup k databázi CMS a auditu se musí použít konektor MySQL C API pro Linux. Připojení ODBC k databázi CMS není podporováno. V této části najdete pokyny k nastavení konektoru MySQL C API na platformě Linux.

1. Odkazy na [ovladače a nástroje pro správu MySQL kompatibilní s Azure Database for MySQL](../../../mysql/concepts-compatibility.md) najdete v tématu popisujícím ovladače, které jsou kompatibilní s Azure Database for MySQL. Vyhledejte v článku ovladač **MySQL Connector/C (libmysqlclient)** .

2. Chcete-li stáhnout ovladače, přečtěte si tento [odkaz](https://downloads.mysql.com/archives/c-c/) .

3. Vyberte operační systém a Stáhněte balíček ot./min. pro sdílenou součást konektoru MySQL. V tomto příkladu se používá verze MySQL-Connector-c-Shared-6.1.11 Connector.

4. Nainstalujte konektor do všech instancí aplikace SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Ověřte cestu k libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Nastavte LD_LIBRARY_PATH tak, aby ukazoval na `/usr/lib64` adresář pro uživatelský účet, který se bude používat pro instalaci.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Příprava serveru

Kroky v této části používají následující předpony:

**[A]**: Tento krok platí pro všechny hostitele.

1. **[A]** na základě charakteru systému Linux (SLES nebo RHEL) je nutné nastavit parametry jádra a nainstalovat požadované knihovny. Informace najdete v části **požadavky na systém** v [příručce pro instalaci platformy Business Intelligence pro systém UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** Ujistěte se, že je ve vašem počítači správně nastavené časové pásmo. Další informace najdete v [části Další požadavky pro systémy UNIX a Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) v Průvodci instalací.

3. **[A]** vytvořte uživatelský účet (**BL1** ADM) a skupinu (sapsys), ve kterých se můžou spouštět procesy na pozadí softwaru. Pomocí tohoto účtu můžete spustit instalaci a spustit software. Účet nevyžaduje oprávnění root.

4. **[A]** nastavte prostředí uživatelského účtu (**BL1** ADM) pro použití podporovaného národního prostředí UTF-8 a ujistěte se, že váš software konzoly podporuje znakové sady UTF-8. Aby se zajistilo, že váš operační systém používá správné národní prostředí, nastavte proměnné prostředí LC_ALL a LANG na své preferované národní prostředí ve vašem uživatelském prostředí (**BL1** ADM).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** nakonfigurujte uživatelský účet (**BL1** ADM).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Stáhněte si a rozbalte multimédia pro SAP BusinessObjects BI Platform z webu SAP Service Marketplace.

## <a name="installation"></a>Instalace

Ověřte národní prostředí pro **BL1** ADM uživatelského účtu na serveru.

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Přejděte na médium pro SAP BusinessObjects BI Platform a spusťte příkaz pomocí příkazu **BL1** ADM User-.

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Projděte si příručku pro instalaci [platformy SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) pro systém UNIX, která je specifická pro vaši verzi. Během instalace platformy SAP BOBI je potřeba poznamenat několik bodů.

- Na obrazovce **Konfigurovat registraci produktu** můžete buď použít dočasný licenční klíč pro řešení SAP BUSINESSOBJECTS ze sap Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) nebo může vygenerovat licenční klíč na webu SAP Service Marketplace.

- Na obrazovce **Vybrat typ instalace** vyberte možnost **Úplná** instalace na první server (azusbosl1), pro jiný server (Azusbosl2) vyberte možnost **vlastní/rozbalit** , čímž se rozbalí existující nastavení BOBI.

- Na obrazovce **Vybrat výchozí nebo existující databázi** vyberte **Konfigurovat existující databázi**, která vás vyzve k výběru CMS a auditu databáze. Vyberte **MySQL** pro typ databáze CMS a audit typ databáze.

  Pokud během instalace nechcete konfigurovat auditování, můžete také vybrat možnost bez auditování databáze.

- Vyberte odpovídající možnosti na **obrazovce vybrat server webové aplikace Java** na základě vaší architektury SAP BOBI. V tomto příkladu jsme vybrali možnost 1, která nainstaluje Tomcat Server na stejnou platformu SAP BOBI.

- Zadejte informace o databázi CMS v **konfiguraci databáze úložiště CMS – MySQL**. Příklad zadání informací o databázi CMS pro instalaci systému Linux. Azure Database for MySQL se používá na výchozím portu 3306.
  
  ![Nasazení SAP BOBI v databázi se systémem Linux-CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Volitelné Zadejte informace o databázi auditu v **konfiguraci databáze úložiště auditu – MySQL**. Příklad zadání informací o databázi auditu pro instalaci systému Linux.

  ![Nasazení SAP BOBI v systému Linux – databáze auditu](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Postupujte podle pokynů a zadáním požadovaných vstupů dokončete instalaci.

U nasazení s více instancemi spusťte instalační program instalace na druhém hostiteli (azusbosl2). Na obrazovce **Vybrat typ instalace** vyberte **vlastní/rozbalit** , čímž se rozbalí existující instalace BOBI.

V nabídce Azure Database for MySQL slouží brána k přesměrování připojení k instancím serveru. Po navázání připojení se v klientovi MySQL zobrazí verze MySQL nastavená v bráně, a ne verze skutečně spuštěná na instanci serveru MySQL. Pokud chcete zjistit verzi instance serveru MySQL, na příkazovém řádku MySQL spusťte příkaz `SELECT VERSION();`. Takže v konzole pro správu systému (CMC) najdete odlišnou verzi databáze, která je v podstatě nastavená na bráně. Další podrobnosti najdete v [podporovaných verzích Azure Database for MySQL serveru](../../../mysql/concepts-supported-versions.md) .

![Nasazení SAP BOBI v systému Linux – nastavení CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Po instalaci

### <a name="tomcat-clustering---session-replication"></a>Clustering Tomcat – replikace relací

Tomcat podporuje clusteringu dvou nebo více aplikačních serverů pro replikaci a převzetí služeb při selhání v relaci. Relace platformy SAP BOBI jsou serializovány. uživatelská relace může bezproblémově překlopit do jiné instance Tomcat, i když aplikační server selže.

Například pokud je uživatel připojený k webovému serveru, který selhává, když uživatel přejde do hierarchie složek v aplikaci SAP BI. V případě správného konfigurovaného clusteru může uživatel pokračovat v navigaci v hierarchii složek bez přesměrování na přihlašovací stránku.

V systému SAP Poznámka [2808640](https://launchpad.support.sap.com/#/notes/2808640)se postup konfigurace clusteringu Tomcat zajišťuje pomocí vícesměrového vysílání. V Azure se ale vícesměrové vysílání nepodporuje. Takže pokud chcete v Azure dělat práci s clustery Tomcat, musíte použít [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Ověřte cluster [Tomcat pomocí statického členství pro SAP BUSINESSOBJECTS BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) v blogu SAP a nastavte cluster Tomcat v Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Webová vrstva pro vyrovnávání zatížení platformy SAP BI

V případě nasazení s více instancemi SAP BOBI jsou servery webových aplikací Java (webová vrstva) spuštěné na dvou nebo více hostitelích. K rovnoměrnému zatížení uživatelů mezi webovými servery můžete použít nástroj pro vyrovnávání zatížení mezi koncovými uživateli a webovými servery. V Azure můžete buď pomocí Azure Load Balancer nebo Azure Application Gateway spravovat provoz na servery webových aplikací. Podrobnosti o každé nabídce jsou vysvětleny v následující části.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure (síťový nástroj pro vyrovnávání zatížení)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) je vysoce výkonný nástroj pro vyrovnávání zatížení vrstvy 4 (TCP, UDP) s nízkou latencí, který distribuuje provoz mezi dobrým Virtual Machines. Sonda stavu nástroje pro vyrovnávání zatížení monitoruje daný port na každém VIRTUÁLNÍm počítači a distribuuje provoz pouze do provozních virtuálních počítačů. Můžete buď zvolit veřejný Nástroj pro vyrovnávání zatížení nebo interní nástroj pro vyrovnávání zatížení v závislosti na tom, jestli chcete, aby byla Platforma SAP BI přístupná z Internetu, nebo ne. Jeho zóna je redundantní a zajišťuje vysokou dostupnost v rámci Zóny dostupnosti.

V části interní Load Balancer najdete obrázek, kde server webové aplikace běží na portu 8080, výchozí port HTTP Tomcat, který se bude monitorovat sondou stavu. Všechny příchozí požadavky, které pocházejí od koncových uživatelů, budou přesměrovány na servery webových aplikací (azusbosl1 nebo azusbosl2) ve fondu back-endu. Load Balancer nepodporuje ukončení TLS/SSL (označuje se také jako snižování zátěže TLS/SSL). Pokud k distribuci provozu mezi webovými servery používáte Azure Load Balancer, doporučujeme použít Standard Load Balancer.

> [!NOTE]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)

![Vyvážení provozu mezi webovými servery Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (Nástroj pro vyrovnávání zatížení webové aplikace)

Služba [Azure Application Gateway (AGW)](../../../application-gateway/overview.md) poskytuje řadič pro doručování aplikací (ADC) jako službu, která se používá k tomu, aby aplikace mohla směrovat uživatelský provoz na jeden nebo více serverů webové aplikace. Nabízí různé možnosti vyrovnávání zatížení vrstvy 7, jako je snižování zátěže TLS/SSL, Firewall webových aplikací (WAF), spřažení relací na základě souborů cookie a další pro vaše aplikace.

V rámci platformy SAP BI aplikace Application Gateway směruje webový provoz do určených prostředků ve fondu back-endu – azusbosl1 nebo azusbos2. Přiřadíte naslouchací proces k portu, vytvoříte pravidla a přidáte prostředky do back-endového fondu. V níže uvedeném obrázku funguje brána Application Gateway s privátní front-end IP adresou (10.31.3.20) jako vstupním bodem pro uživatele, zpracovává příchozí připojení TLS/SSL (HTTPS-TCP/443), dešifruje protokol TLS/SSL a předává je nešifrovaný požadavek (HTTP-TCP/8080) na servery ve fondu back-endu. V případě, že se používá funkce protokolu TLS/SSL, potřebujeme spravovat jeden certifikát TLS/SSL ve službě Application Gateway, což zjednodušuje operace.

![Vyvážení provozu mezi webovými servery Application Gateway](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Pokud chcete nakonfigurovat Application Gateway pro webový Server SAP BOBI, můžete se podívat na [Vyrovnávání zatížení webových serverů SAP BOBI pomocí Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) na blogu SAP.

> [!NOTE]
> Doporučujeme použít Azure Application Gateway k vyrovnávání zatížení provozu na webový server, protože poskytuje funkce jako třeba snižování zátěže protokolu SSL, centralizaci správy SSL pro omezení šifrování a dešifrování na serveru, Round-Robin algoritmus pro distribuci provozu, možnosti brány firewall webových aplikací (WAF), vysokou dostupnost a tak dále.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Platforma SAP BusinessObjects BI – zálohování a obnovení

Zálohování a obnovení je proces vytváření pravidelných kopií dat a aplikací do samostatného umístění. V případě ztráty nebo poškození původních dat nebo aplikací je tak možné obnovit nebo obnovit do předchozího stavu. Je to také základní součástí jakékoli strategie pro zotavení po havárii podniku.

Chcete-li vytvořit komplexní strategii zálohování a obnovení pro platformu SAP BOBI, identifikujte součásti, které vedou k výpadkům systému nebo přerušení v aplikaci. V platformě SAP BOBI je zálohování následujících komponent důležité k ochraně aplikace.

- Instalační adresář SAP BOBI (spravované disky úrovně Premium)
- Server úložiště souborů (soubory Azure NetApp Files nebo Azure Premium)
- Databáze CMS (Azure Database for MySQL nebo databáze na virtuálním počítači Azure)

Následující část popisuje, jak implementovat strategii zálohování a obnovení pro jednotlivé komponenty na platformě SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Zálohování & obnovení pro instalační adresář SAP BOBI

Nejjednodušší způsob, jak zálohovat aplikační servery a všechny připojené disky v Azure, je použití služby [Azure Backup](../../../backup/backup-overview.md) . Poskytuje nezávislé a izolované zálohy pro ochranu nezamýšleného zničení dat na vašich virtuálních počítačích. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálování jsou jednoduché, zálohy jsou optimalizované a v případě potřeby je můžete snadno obnovit.

V rámci procesu zálohování se snímek povede a data se přenesou do trezoru služby Recovery Services bez dopadu na produkční úlohy. Snímek poskytuje různé úrovně konzistence, jak je popsáno v článku [konzistence snímků](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . Můžete také zálohovat podmnožinu datových disků ve virtuálním počítači pomocí funkcí zálohování a obnovení selektivních disků. Další informace najdete v dokumentu [zálohování virtuálních počítačů Azure](../../../backup/backup-azure-vms-introduction.md) a [Nejčastější dotazy – zálohování virtuálních počítačů Azure](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Zálohování & obnovení pro server úložiště souborů

Pro **Azure NetApp Files** můžete vytvořit snímky na vyžádání a naplánovat automatické snímky pomocí zásad snímků. Kopie snímků poskytují kopii ANF svazku v čase. Další informace najdete v tématu [Správa snímků pomocí Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

Zálohování **souborů Azure** je integrované s nativní [Azure Backupovou](../../../backup/backup-overview.md) službou, která centralizovat funkci zálohování a obnovení společně s zálohováním virtuálních počítačů a zjednodušuje práci s operacemi. Další informace najdete v tématu [Zálohování sdílených složek Azure](../../../backup/azure-file-share-backup-overview.md) a [Nejčastější dotazy – zálohování souborů Azure](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Zálohování & obnovení pro databázi CMS

Azure Database of MySQL je DBaaS nabídka v Azure, která automaticky vytvoří zálohy serveru a ukládá je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Azure Database of MySQL přebírá zálohy datových souborů a transakčního protokolu. V závislosti na podporované maximální velikosti úložiště aplikace buď vybere úplné a rozdílové zálohy (4 TB maximálních úložných serverů) nebo zálohu snímků (až 16 TB maximálních úložných serverů). Tyto zálohy umožňují obnovení serveru v jakémkoli okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní, což můžete [volitelně nakonfigurovat](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) až na tři dny. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

Tyto záložní soubory nejsou přístupné uživateli a nelze je exportovat. Tyto zálohy lze použít pouze pro operace obnovení v Azure Database for MySQL. Pomocí [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) můžete zkopírovat databázi. Další informace najdete v tématu [zálohování a obnovení v Azure Database for MySQL](../../../mysql/concepts-backup.md).

Pro databázi nainstalovanou v Virtual Machines můžete použít standardní nástroje pro zálohování nebo [Azure Backup](../../../backup/sap-hana-db-about.md) pro databázi Hana. I když služby a nástroje Azure nevyhovují vašim požadavkům, můžete k vytvoření zálohování disků použít jiné nástroje nebo skripty pro zálohování.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Spolehlivost platformy SAP BusinessObjects BI

Platforma SAP BusinessObjects BI zahrnuje různé úrovně, které jsou optimalizované pro konkrétní úkoly a operace. Když se komponenta z nějaké vrstvy stane nedostupnou, aplikace SAP BOBI se buď nestane přístupná, nebo některé funkce aplikace nebudou fungovat. Proto je potřeba zajistit, aby byla každá úroveň spolehlivá, aby se zajistila činnost aplikace bez jakýchkoli přerušení provozu.

Tato část se zaměřuje na následující možnosti pro platformu SAP BOBI Platform –

- **Vysoká dostupnost:** Platforma s vysokou dostupností má alespoň dvě všechno, co je v oblasti Azure, aby se zajistila funkčnost aplikace v případě, že jeden ze serverů nebude k dispozici.
- **Zotavení po havárii:** Je to proces obnovení funkčnosti vaší aplikace, pokud dojde k nějaké závažné ztrátě, jako je celá oblast Azure, z důvodu nějaké přírodní havárie.

Implementace tohoto řešení se liší v závislosti na povaze nastavení systému v Azure. Takže zákazník musí přizpůsobit řešení vysoké dostupnosti a zotavení po havárii podle svého obchodního požadavku.

### <a name="high-availability"></a>Vysoká dostupnost

Vysoká dostupnost odkazuje na sadu technologií, které mohou minimalizovat přerušení IT tím, že zajišťují kontinuitu podnikových a aplikačních funkcí prostřednictvím redundantních součástí chráněných při selhání, odolnosti proti chybám nebo převzetí služeb při selhání v jednom datovém centru. V našem případě jsou datová centra v jedné oblasti Azure. Architektura s [vysokou dostupností a scénáři pro SAP](sap-high-availability-architecture-scenarios.md) poskytuje základní přehled o různých technikách vysoké dostupnosti a doporučeních nabízených v Azure pro aplikace SAP, které vám pomůžou pokyny v této části.

Na základě výsledků změny velikosti platformy SAP BOBI je potřeba navrhnout na šířku a určit distribuci komponent BI napříč Virtual Machines a podsítěmi Azure. Úroveň redundance v distribuované architektuře závisí na cíli požadované doby obnovení (RTO) a cíli bodu obnovení (RPO). Platforma SAP BOBI zahrnuje různé úrovně a komponenty na každé úrovni by měly být navržené tak, aby umožňovaly redundanci. Takže v případě, že dojde k výpadku jedné součásti, nedochází k žádnému přerušení vaší aplikace SAP BOBI. Třeba

- Redundantní aplikační servery, jako jsou aplikační servery BI a webový server
- Jedinečné součásti, jako je databáze CMS, server úložiště souborů, Load Balancer

Následující část popisuje, jak dosáhnout vysoké dostupnosti pro každou součást platformy SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Vysoká dostupnost aplikačních serverů

Pro BI a servery webových aplikací, ať už jsou nainstalované samostatně nebo společně, nepotřebují konkrétní řešení vysoké dostupnosti. Vysokou dostupnost můžete dosáhnout redundancí, to znamená konfigurací více instancí BI a webových serverů v různých Virtual Machinesch Azure.

Chcete-li snížit dopad výpadků z důvodu jedné nebo více událostí, je vhodné postupovat podle postupu vysoké dostupnosti pro aplikační servery běžící na více virtuálních počítačích.

- K ochraně selhání datacentra použijte Zóny dostupnosti.
- Nakonfigurujte více Virtual Machines ve skupině dostupnosti pro zajištění redundance.
- Pro virtuální počítače ve skupině dostupnosti použijte Managed Disks.
- Nakonfigurujte každou aplikační vrstvu na samostatné skupiny dostupnosti.

Další informace najdete v části [Správa dostupnosti virtuálních počítačů se systémem Linux](../../availability.md) .

#### <a name="high-availability-for-cms-database"></a>Vysoká dostupnost pro databázi CMS

Pokud používáte službu Azure Database as a Service (DBaaS) pro databázi CMS, je ve výchozím nastavení k dispozici architektura vysoké dostupnosti. Stačí jenom vybrat oblast a službu, která nabízí vysokou dostupnost, redundanci a odolnost proti chybám, aniž byste museli konfigurovat žádné další součásti. Další informace o smlouvě SLA pro podporovanou nabídku DBaaS v Azure najdete [v části vysoká dostupnost v Azure Database for MySQL](../../../mysql/concepts-high-availability.md) a [vysoká dostupnost pro Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

Pro jiné nasazení systému DBMS pro databázi CMS si přečtěte téma [Průvodce nasazením systému DBMS pro úlohu SAP](dbms_guide_general.md), které poskytuje přehled o různých nasazeních systému DBMS a jejich přístupu k dosažení vysoké dostupnosti.

#### <a name="high-availability-for-file-repository-server"></a>Vysoká dostupnost pro server úložiště souborů

Služba úložiště souborů (FRS) odkazuje na adresáře na disku, kde jsou uložené obsahy, jako jsou sestavy, celé části a připojení. Sdílí se mezi všemi aplikačními servery tohoto systému. Proto je nutné zajistit, aby byl vysoce dostupný.

V Azure můžete buď zvolit [soubory Azure Premium](../../../storage/files/storage-files-introduction.md) nebo [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) pro sdílenou složku, které jsou navržené tak, aby byly vysoce dostupné a vysoce odolnější. Další informace najdete v části [redundance](../../../storage/files/storage-files-planning.md#redundancy) pro soubory Azure.

> [!NOTE]
> Protokol SMB pro soubory Azure je všeobecně dostupný, ale podpora protokolu NFS pro soubory Azure je aktuálně ve verzi Preview. Další informace najdete v tématu [Podpora NFS 4,1 pro soubory Azure je teď ve verzi Preview](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) .

Protože tato služba sdílení souborů není k dispozici ve všech oblastech, ujistěte se, že odkazujete na [produkty, které jsou k dispozici v oblasti](https://azure.microsoft.com/en-us/global-infrastructure/services/) lokalita, aby bylo možné zjistit aktuální informace. Pokud služba není ve vaší oblasti k dispozici, můžete vytvořit server NFS, ze kterého můžete sdílet systém souborů do aplikace SAP BOBI. Musíte ale také zvážit jeho vysokou dostupnost.

#### <a name="high-availability-for-load-balancer"></a>Vysoká dostupnost pro nástroj pro vyrovnávání zatížení

K distribuci provozu mezi webovým serverem můžete použít Azure Load Balancer nebo Azure Application Gateway. Redundanci pro libovolný nástroj pro vyrovnávání zatížení je možné dosáhnout na základě SKU, kterou zvolíte pro nasazení.

- V případě Azure Load Balancer můžete redundanci dosáhnout konfigurací Standard Load Balancer front-endu jako redundantního pro zónu. Další informace najdete v tématu [Standard Load Balancer a zóny dostupnosti](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Pro Application Gateway je možné dosáhnout vysoké dostupnosti na základě typu vrstvy vybrané během nasazování.
  - SKU v1 podporuje scénáře s vysokou dostupností, pokud jste nasadili dvě nebo víc instancí. Azure distribuuje tyto instance napříč doménami aktualizace a selhání, aby se zajistilo, že se instance ve stejnou dobu nedaří. Díky této SKU je možné redundanci dosáhnout v rámci zóny.
  - verze V2 SKU automaticky zajišťuje rozprostření nových instancí mezi doménami selhání a aktualizačními doménami. Pokud zvolíte redundanci zóny, nejnovější instance se také rozšíří napříč zónami dostupnosti, aby nabízely odolnost v oblasti selhání. Další podrobnosti najdete v tématu Automatické [škálování a redundantní Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md) .

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Referenční architektura vysoké dostupnosti pro platformu SAP BusinessObjects BI

Pod referenční architekturou najdete popis nastavení platformy SAP BOBI pomocí skupiny dostupnosti, která poskytuje redundanci a dostupnost virtuálních počítačů v rámci zóny. Tato architektura předvádí používání různých služeb Azure, jako je Azure Application Gateway, Azure NetApp Files a Azure Database for MySQL pro platformu SAP BOBI, která nabízí integrovanou redundanci, která omezuje složitost správy různých řešení s vysokou dostupností.

V níže uvedeném obrázku je příchozí provoz (HTTPS-TCP/443) vyvážený pomocí skladové položky Azure Application Gateway V1, která je vysoce dostupná při nasazení ve dvou nebo více instancích. Víc instancí webového serveru, serverů pro správu a serverů pro zpracování se nasadí do samostatných Virtual Machines, aby bylo možné zajistit redundanci a každá vrstva je nasazená v samostatných skupinách dostupnosti. Azure NetApp Files obsahuje integrovanou redundanci v rámci datového centra, takže vaše ANF svazky pro server úložiště souborů budou vysoce dostupné. Databáze CMS se zřídí na Azure Database for MySQL (DBaaS), která má vysokou dostupnost. Další informace najdete v tématu [Vysoká dostupnost v Azure Database for MySQL](../../../mysql/concepts-high-availability.md) Guide.

![Redundance platformy SAP BusinessObjects BI s využitím skupin dostupnosti](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Výše uvedená architektura nabízí přehled o tom, jak se dá provést nasazení SAP BOBI v Azure. Ale nepokrývá všechny možné možnosti konfigurace platformy SAP BOBI v Azure. Zákazník může přizpůsobit své nasazení na základě jejich obchodního požadavku, a to výběrem různých produktů a služeb pro různé komponenty, jako je Load Balancer, server úložiště souborů a DBMS.

V několika oblastech Azure se nabízí Zóny dostupnosti, což znamená, že nezávisle dodal zdroj napájení, chlazení a síť. Umožňuje zákazníkům nasadit aplikaci ve dvou nebo třech zónách dostupnosti. Pro zákazníky, kteří chtějí dosáhnout vysoké dostupnosti napříč AZs, může nasadit platformu SAP BOBI napříč zónami dostupnosti a ujistit se, že každá součást aplikace je zóna redundantní.

### <a name="disaster-recovery"></a>Zotavení po havárii

Pokyny v této části popisují strategii pro poskytování ochrany zotavení po havárii pro platformu SAP BOBI. Doplňuje [zotavení po havárii pro dokument SAP](../../../site-recovery/site-recovery-sap.md) , který představuje primární prostředky pro celkový přístup k zotavení po havárii SAP.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referenční architektura zotavení po havárii pro SAP BusinessObjects BI Platform

Tato referenční architektura spouští nasazení platformy SAP BOBI s redundantními aplikačními servery s více instancemi. V případě zotavení po havárii byste měli převzít všechny úrovně do sekundární oblasti. Každá vrstva používá jinou strategii pro poskytování ochrany při zotavení po havárii.

![Zotavení po havárii platformy SAP BusinessObjects BI](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

Load Balancer se používá k distribuci provozu mezi servery webových aplikací na platformě SAP BOBI. Pokud chcete dosáhnout Application Gateway DR pro Azure, implementujte paralelní nastavení aplikační brány v sekundární oblasti.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuální počítače s aplikačními servery web a BI

Služba Azure Site Recovery se dá použít k replikaci Virtual Machines spuštěných webových serverů a aplikačních serverů BI v sekundární oblasti. Replikuje servery v sekundární oblasti, takže když dojde k haváriím a výpadkům, můžete snadno převzít služby při selhání replikovaného prostředí a pokračovat v práci.

#### <a name="file-repository-servers"></a>Servery úložiště souborů

- **Azure NetApp Files** poskytuje svazky se systémem souborů NFS a SMB, takže můžete k replikaci dat mezi oblastmi Azure použít jakýkoli souborový Nástroj pro kopírování. Další informace o tom, jak kopírovat svazek ANF do jiné oblasti, najdete v tématu [Nejčastější dotazy týkající se Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region) .

  Můžete použít replikaci Azure NetApp Files mezi oblastmi, která je aktuálně ve [verzi Preview](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) , která používá technologii® NetApp SnapMirror. Takže pouze změněné bloky jsou odesílány přes síť v komprimovaném, efektivním formátu. Tato proprietární technologie minimalizuje množství dat potřebných pro replikaci napříč oblastmi, které šetří náklady na přenos dat. Také zkracuje dobu replikace, takže můžete dosáhnout menšího cíle bodu obnovení (RPO). Další informace najdete v tématu [požadavky a předpoklady pro použití replikace mezi oblastmi](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) .

- **Soubory Azure Premium** podporují jenom místně redundantní (LRS) a redundantní úložiště zóny (ZRS). V případě strategie zotavení po havárii souborů Azure Premium můžete pomocí [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) nebo [Azure PowerShell](/powershell/module/az.storage/) zkopírovat soubory do jiného účtu úložiště v jiné oblasti. Další informace najdete v tématu [zotavení po havárii a převzetí služeb při selhání účtu úložiště](../../../storage/common/storage-disaster-recovery-guidance.md) .

#### <a name="cms-database"></a>Databáze CMS

Azure Database for MySQL poskytuje více možností pro obnovení databáze, pokud dojde k nějaké havárii. Vyberte odpovídající možnost, která bude fungovat pro vaši firmu.

- Povolte repliky čtení mezi oblastmi a zvyšte tak provozní kontinuitu a plánování zotavení po havárii. Můžete replikovat ze zdrojového serveru do pěti replik. Repliky čtení jsou asynchronně aktualizované pomocí technologie pro replikaci binárního protokolu MySQL. Repliky jsou nové servery, které spravujete podobně jako běžné Azure Database for MySQL servery. Další informace o replikách pro čtení, dostupných oblastech, omezeních a tom, jak převzít služby při selhání, [najdete v článku koncepty replik pro čtení](../../../mysql/concepts-read-replicas.md).

- Použijte funkci geografického obnovení Azure Database for MySQL, která obnoví Server pomocí geograficky redundantních záloh. Tyto zálohy jsou přístupné i v případě, že je oblast, na které je server hostovaný, v režimu offline. Můžete obnovit z těchto záloh do jakékoli jiné oblasti a převést Server zpátky do online režimu.

  > [!NOTE]
  > Geografické obnovení je možné pouze v případě, že jste zřídili Server s geograficky redundantním úložištěm záloh. Změna **možností redundance zálohy** po vytvoření serveru se nepodporuje. Další informace najdete v článku [redundance zálohy](../../../mysql/concepts-backup.md#backup-redundancy-options) .

Následuje doporučení pro zotavení po havárii jednotlivých vrstev používaných v tomto příkladu.

| Vrstvy platforem SAP BOBI   | Doporučení                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Paralelní nastavení Application Gateway v sekundární oblasti                                                |
| Servery webových aplikací   | Replikace pomocí Site Recovery                                                                         |
| Aplikační servery BI    | Replikace pomocí Site Recovery                                                                         |
| Azure NetApp Files        | Nástroj pro kopírování na základě souborů pro replikaci dat do sekundární oblasti **nebo** ANF replikace mezi oblastmi (Preview) |
| Azure Database for MySQL  | V různých oblastech se repliky čtou **nebo** obnovují zálohy z geograficky redundantního zálohování.                             |

## <a name="next-steps"></a>Další kroky

- [Nastavení zotavení po havárii pro nasazení aplikace ve více vrstvách SAP](../../../site-recovery/site-recovery-sap.md)
- [Plánování a implementace Azure Virtual Machines pro SAP](planning-guide.md)
- [Nasazení Azure Virtual Machines pro SAP](deployment-guide.md)
- [Nasazení Azure Virtual Machines DBMS pro SAP](./dbms_guide_general.md)
