---
title: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux s Azure NetApp Files | Dokumentace Microsoftu
description: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503575"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux s Azure Files NetApp pro aplikace SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů, instalaci rozhraní clusteru a instalace s vysokou dostupností systému SAP NetWeaver 7.50, pomocí [souborů NetApp Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
V ukázkové konfigurace příkazy instalace atd. ASCS instance je číslo 00, Lajících instance je číslo 01, instance primární aplikace (Pa adresy) je 02 a instanci aplikace (AAS) je 03. Se používá QAS ID systému SAP. 

V tomto článku se podrobně databázové vrstvě.  

Přečtěte si následující poznámky SAP a Paper nejprve:

* [Dokumentace ke službě Azure souborů NetApp][anf-azure-doc] 
* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2002167] má doporučené nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP [2009879] obsahuje pokyny pro SAP HANA pro Red Hat Enterprise Linux
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [SAP Netweaver v pacemaker clusteru](https://access.redhat.com/articles/3150081)
* Dokumentace ke službě obecné RHEL
  * [Přehled doplňků vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňku vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/Lajících pro SAP Netweaver pomocí samostatné prostředky v RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/Lajících nakonfigurovat zařazování samostatný Server 2 (ENSA2) v Pacemaker na RHEL ](https://access.redhat.com/articles/3974941)
* Konkrétní RHEL dokumentace k Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp aplikace SAP v Microsoft Azure pomocí služby soubory Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Vysoká availability(HA) pro centrální služby SAP Netweaver vyžaduje sdílené úložiště.
Abychom toho dosáhli v prostředí Red Hat Linux se zatím potřebné k vytvoření samostatného clusteru s vysokou dostupností GlusterFS. 

Nyní je možné dosáhnout SAP Netweaver HA pomocí sdíleného úložiště, které jsou nasazené v Azure NetApp soubory. Použití služby soubory Azure NetApp pro sdílené úložiště se eliminují potřebu další [GlusterFS clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker stále se vyžaduje pro vysokou dostupnost z centrální services(ASCS/SCS) SAP Netweaveru.

![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Lajících a databáze SAP HANA pomocí virtuální název hostitele a virtuální IP adresy. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení pomocí samostatných přední IP adresy (A) SCS a Lajících.

> [!IMPORTANT]
> S několika SID clusteringu SAP ASCS/Lajících s Red Hat Linux jako hostovaný operační systém na virtuálních počítačích Azure je **nepodporuje**. Clustering s několika SID popisuje instalaci více instancí SAP ASCS/Lajících s různé identifikátory SID v jednom clusteru Pacemaker.

### <a name="ascs"></a>(A)SCS

* Konfiguraci front-endu
  * IP adresa 192.168.14.9
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 620<strong>&lt;nr&gt;</strong>
* Pravidla Vyrovnávání zatížení
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguraci front-endu
  * IP adresa 192.168.14.10
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 621<strong>&lt;nr&gt;</strong>
* Pravidla Vyrovnávání zatížení
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Azure souborů NetApp 

SAP NetWeaver vyžaduje sdílené úložiště pro přenos a profil adresář.  Než budete pokračovat v instalaci pro infrastrukturu souborů Azure NetApp, seznamte se s [dokumentace ke službě soubory Azure NetApp][anf-azure-doc]. Zaškrtněte, pokud vybrané oblasti Azure nabízí NetApp soubory Azure. Následující odkaz zobrazí dostupnost souborů NetApp Azure podle oblasti Azure: [Soubory Azure NetApp dostupnost podle oblasti Azure][anf-avail-matrix].

Služba soubory Azure NetApp je k dispozici v několika [oblastí Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Před nasazením Azure NetApp Files, žádost o připojení ke službě Azure Files NetApp, následující [registrace pro Azure NetApp soubory pokynů][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure souborů NetApp  

V krocích se předpokládá, že jste už nasadili [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Prostředky souborů NetApp Azure a virtuální počítače, ve kterém se připojí prostředky Azure NetApp soubory musí být nasazeny ve stejné virtuální síti Azure nebo v partnerských virtuálních sítích Azure.  

1. Pokud jste dosud neučinili, který, požádat o [registrace do služby soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Ve vybrané oblasti Azure, po vytvoření účtu NetApp [pokyny k vytvoření účtu NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Nastavení kapacity fondu souborů NetApp Azure, po [pokyny, jak nastavit službu Azure NetApp Files kapacity fondu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura SAP Netweaver uvedené v tomto článku využívá jeden souborů NetApp Azure kapacitu fondu, SKU úrovně Premium. Doporučujeme, abyste SKU úrovně Premium souborů NetApp Azure pro úlohy aplikací SAP Netweaver v Azure.  
4. Podsíť, která se soubory Azure NetApp delegovat, jak je popsáno v [pokyny delegovat podsítě do služby soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Svazky souborů Azure NetApp, následující nasadit [pokyny pro vytvoření svazku pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Svazky v určené soubory NetApp Azure nasadit [podsítě](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Mějte na paměti, že Azure NetApp soubory prostředků a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo v partnerských virtuálních sítích Azure. V tomto příkladu používáme dva svazky souborů Azure NetApp: sap<b>QAS</b> a transSAP. Cesty k souborům, které jsou připojené k odpovídající přípojné body jsou /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, atd.  

   1. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>lajících)
   5. svazek transSAP (nfs://192.168.24.4/transSAP)
   6. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>Pa adresy)
   7. svazek sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
V tomto příkladu jsme použili souborů NetApp Azure pro všechny systémy souborů SAP Netweaver ukazují, jak je možné NetApp soubory Azure. Je také možné nasadit systémy SAP souborů, které nemusíte připojit prostřednictvím systému souborů NFS jako [Azure disk storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . V tomto příkladu <b>-e</b> musí být souborů NetApp Azure a <b>f-g</b> (to znamená, / USR/sap/<b>QAS</b>/D<b>02</b>, /USR/sap/<b>QAS </b>/D<b>03</b>) může být nasazený jako Azure disk storage. 

### <a name="important-considerations"></a>Důležité informace

Při zvažování souborů NetApp Azure pro SAP Netweaver na architektuře operačního systému SUSE vysokou dostupnost, mějte na paměti následující důležité aspekty:

- Minimální kapacitu fondu je 4 TB. Velikost kapacity fondu musí být v násobcích po 4 TB.
- Minimální objem je 100 GB
- Služba soubory Azure NetApp a všechny virtuální počítače, kde se svazky souborů NetApp Azure připojí, musí být ve stejné virtuální síti Azure nebo v [partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ve stejné oblasti. Nyní se podporuje Azure souborů NetApp přístup přes VNET peering ve stejné oblasti. Přístup ke službě Azure NetApp přes globální partnerský vztah se ještě nepodporuje.
- Vybranou virtuální síť musí mít podsíť delegovat do služby soubory Azure NetApp.
- Služba soubory Azure NetApp v současné době podporuje pouze NFSv3 
- Služba soubory Azure NetApp nabízí [exportovat zásady](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): povolených klientů, můžete řídit přístup typu (čtení a zápis, jen pro čtení, atd.). 
- Funkce Azure souborů NetApp ještě není zóny. Funkce souborů NetApp Azure není momentálně ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti možné důsledků latence v některých oblastech Azure. 

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

V tomto příkladu byly ručně nasadili prostředky prostřednictvím [webu Azure portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu prostřednictvím webu Azure portal

Nejprve musíte vytvořit svazky NetApp soubory Azure. Nasazení virtuálních počítačů. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvořte nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvoření front-endové IP adresy
      1. IP adresa 192.168.14.9 ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-endový fond IP adres a klikněte na tlačítko Přidat
         1. Zadejte název nové front-endový fond IP (například **front-endu. QAS. ASCS**)
         1. Nastavení přiřazení do statické a zadejte IP adresu (například **192.168.14.9**)
         1. Klikněte na tlačítko OK
      1. IP adresa 192.168.14.10 ASCS Lajících
         * Opakujte předchozí kroky v části "a" k vytvoření IP adresy pro Lajících (například **192.168.14.10** a **front-endu. QAS. Lajících**)
   1. Vytvoření back-endové fondy
      1. Vytvoření back-endový fond pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového back-endového fondu (například **back-endu. QAS**)
         1. Klikněte na tlačítko Přidat virtuální počítač.
         1. Vyberte skupinu dostupnosti jste vytvořili dříve pro ASC 
         1. Vyberte virtuální počítače (A) SCS clusteru
         1. Klikněte na tlačítko OK
   1. Vytvoření sondy stavu
      1. Port 620**00** pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení vyberte testy stavu a klikněte na tlačítko Přidat
         1. Zadejte název nového stavu testu (například **stavu. QAS. ASCS**)
         1. Jako protokol, port 620 vyberte TCP**00**, Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 621**01** pro ASCS Lajících
            * Opakujte předchozí kroky v části "c" k vytvoření sondy stavu pro Lajících (například 621**01** a **stavu. QAS. Lajících**)
   1. Pravidla Vyrovnávání zatížení
      1. 32**00** TCP pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, pravidla Vyrovnávání zatížení vyberte a klikněte na tlačítko Přidat
         1. Zadejte název nového pravidla služby load balancer (například **lb. QAS. ASCS.3200**)
         1. Vyberte IP adresu front-endu pro ASCS, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **front-endu. QAS. ASCS**)
         1. Zachovat protokol **TCP**, zadejte port **3200**
         1. Zvyšte časový limit nečinnosti až 30 minut.
         1. **Ujistěte se, že chcete povolit plovoucí IP adresy**
         1. Klikněte na tlačítko OK
      1. Další porty pro ASC
         * Opakujte předchozí kroky v části "d" pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASC
      1. Další porty pro ASCS Lajících
         * Opakujte předchozí kroky v části "d" pro porty 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 a TCP pro ASCS Lajících


> [!IMPORTANT]
> Nepovolujte TCP časová razítka na virtuálních počítačích Azure umístěných za nástrojem pro vyrovnávání zatížení Azure. Povolení protokolu TCP časová razítka způsobí, že sond stavu selhání. Nastavte parametr **net.ipv4.tcp_timestamps** k **0**. Podrobnosti najdete v tématu [sondy stavu nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) pro vytvoření základní Pacemaker clusteru pro tento server (A) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava instalace SAP NetWeaver

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

    ```
    sudo vi /etc/hosts
    ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  SAP vytvoření adresáře ve svazku souborů NetApp Azure.  
   Připojit dočasně svazek souborů NetApp Azure na jednom z virtuálních počítačů a vytvořit SAP adresáře (cesty k souborům).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr + můžu/sapmnt/QAS sudo chattr + můžu /usr/sap/trans sudo chattr + můžu /usr/sap/QAS/SYS sudo chattr + můžu /usr/sap/QAS/ASCS00 sudo chattr + můžu /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yumu -y instalace systému souborů nfs utils prostředků agentů prostředků – agentů sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yumu informace o prostředku – agentů sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Načíst moduly plug-in: langpacks, id produktu, hledání – zakázáno úložiště
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata je více než 2 týdny. Nainstalovat yumu cron? Nebo spusťte: rychlé makecache yum
   # <a name="installed-packages"></a>Nainstalované balíčky
   # <a name="name---------resource-agents-sap"></a>Název: prostředků agentů sap
   # <a name="arch---------x8664"></a>Arch: x86_64
   # <a name="version------395"></a>Verze: 3.9.5
   # <a name="release------124el7"></a>Verze: 124.el7
   # <a name="size---------100-k"></a>Velikost: 100 k
   # <a name="repo---------installed"></a>Úložiště: nainstalovaná
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Z úložiště: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Shrnutí: SAP agentů prostředků clusteru a konektor skriptu
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>ADRESA URL: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licence: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Popis: Agenti prostředků SAP a rozhraní connector skript s
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Pacemaker umožňující instance SAP pro správu v clusteru
   #          <a name="-environment"></a>: prostředí.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Přidejte následující řádky do fstab, uložit a ukončit
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS systému souborů nfs rw, pevný, parametru rsize = 65536 wsize = 65536, vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS systému souborů nfs rw, pevný, parametru rsize = 65536 wsize = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans systému souborů nfs rw, pevný, parametru rsize = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo připojení - a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Nastavte vlastnost ResourceDisk.EnableSwap y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Vytvoření a použití stránkovacího souboru na disk prostředků.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Nastavení velikosti ODKLÁDACÍHO souboru s vlastností ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Volné místo disk počítačových prostředků se liší podle velikosti virtuálního počítače. Ujistěte se, že nenastavujte hodnotu, která je příliš velký. Můžete zkontrolovat velikosti odkládacího souboru se příkaz swapon
   # <a name="size-of-the-swapfile"></a>Velikost stránkovacího souboru.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   restart waagent služby sudo
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   pohotovostní anftstsapcl2 sudo počítače uzlu
   
   Vytvoření zdroje počítače sudo fs_QAS_ASCS device='192.168.24.5:/sapQAS/usrsapQASascs systému souborů \
     adresáře = "/ usr/sap/QAS/ASCS00" fstype = 'systému souborů nfs' \
     --group g-QAS_ASCS
   
   Vytvoření zdroje počítače sudo vip_QAS_ASCS IPaddr2 \
     IP = 192.168.14.9 cidr_netmask = 24 \
     --group g-QAS_ASCS
   
   Vytvoření zdroje počítače sudo nc_QAS_ASCS azure lb port = 62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   stav počítače sudo
   
   # <a name="node-anftstsapcl2-standby"></a>Uzel anftstsapcl2: pohotovostním režimu
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Úplný seznam zdrojů:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):      Začínáme anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>Skupina prostředků: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Začínáme anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Začínáme anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Povolte přístup k SWPM. Toto pravidlo není trvalá. Pokud restartujete počítač, budete muset spustit příkaz znovu.
   Brána firewall sudo-cmd--zóny = veřejné - přidat port = 4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo změnit vlastníka qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo počítače uzlu unstandby anftstsapcl2 sudo počítače uzlu pohotovostní anftstsapcl1
   
   Vytvoření zdroje počítače sudo fs_QAS_AERS device='192.168.24.5:/sapQAS/usrsapQASers systému souborů \
     adresáře = "/ usr/sap/QAS/ERS01" fstype = 'systému souborů nfs' \
    --skupině g QAS_AERS

   Vytvoření zdroje počítače sudo vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --skupině g QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --skupině g QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   stav počítače sudo
   
   # <a name="node-anftstsapcl1-standby"></a>Uzel anftstsapcl1: pohotovostním režimu
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Úplný seznam zdrojů:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Začínáme anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>Skupina prostředků: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Začínáme anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Spuštění anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Začínáme anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>Skupina prostředků: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Začínáme anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Začínáme anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Povolte přístup k SWPM. Toto pravidlo není trvalá. Pokud restartujete počítač, budete muset spustit příkaz znovu.
   Brána firewall sudo-cmd--zóny = veřejné - přidat port = 4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo změnit vlastníka qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Změňte příkaz restart příkazu start.
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>Přidejte parametr keep alive
   modul/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Změňte příkaz restart příkazu start.
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>Odebrání profilu Lajících automatické spuštění
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Měnit konfiguraci systému Linux
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>Na uzlu, kde jste nainstalovali ASCS okomentujte následující řádek
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ASCS00/exe: $LD_LIBRARY_PATH; Export LD_LIBRARY_PATH; /USR/SAP/QAS/ASCS00/exe/sapstartsrv pf = / usr/sap/QAS/SYS/profil/QAS_ASCS00_anftstsapvh - D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>Na uzlu, kde jste nainstalovali Lajících okomentujte následující řádek
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: $LD_LIBRARY_PATH; Export LD_LIBRARY_PATH; /USR/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/profil/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   Vlastnosti počítače sudo nastavit režim údržby = true
   
    Vytvoření zdroje počítače sudo rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName = QAS_ASCS00_anftstsapvh START_PROFILE = "/ sapmnt/QAS/profil/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    prostředek meta-věrnosti = 5000 migrace – prahová hodnota = 1 \
    --group g-QAS_ASCS
   
    Vytvoření zdroje počítače sudo rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName = QAS_ERS01_anftstsapers START_PROFILE = "/ sapmnt/QAS/profil/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --skupině g QAS_AERS
      
    společné umístění omezení počítače sudo přidat g-QAS_AERS s g-QAS_ASCS-5000 sudo počítače omezení umístění rsc_sap_QAS_ASCS00 pravidlo skóre = 2000 runs_ers_QAS eq 1 sudo počítače omezení pořadí g-QAS_ASCS pak g-QAS_AERS druh = volitelné symetrické = false
    
    sudo počítače uzlu unstandby anftstsapcl1 sudo počítače vlastnost nastavit režim údržby = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    Vlastnosti počítače sudo nastavit režim údržby = true
    
    Vytvoření zdroje počítače sudo rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName = QAS_ASCS00_anftstsapvh START_PROFILE = "/ sapmnt/QAS/profil/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    prostředek meta-věrnosti = 5000 \
    --group g-QAS_ASCS
   
    Vytvoření zdroje počítače sudo rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName = QAS_ERS01_anftstsapers START_PROFILE = "/ sapmnt/QAS/profil/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --skupině g QAS_AERS
      
    společné umístění omezení počítače sudo přidat g-QAS_AERS s g-QAS_ASCS-5000 sudo počítače omezení pořadí g-QAS_ASCS pak g-QAS_AERS druh = volitelné symetrické = false
   
    sudo počítače uzlu unstandby anftstsapcl1 sudo počítače vlastnost nastavit režim údržby = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    stav počítače sudo
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Úplný seznam zdrojů:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Začínáme anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>Skupina prostředků: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Začínáme anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Začínáme anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>Skupina prostředků: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Začínáme anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Začínáme anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Port testu ascs
   brány firewall sudo-cmd – zóny = veřejného – přidání portu = 62000/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = brány firewall 62000/tcp sudo-cmd – zóny = veřejné - přidat port = 3200/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = sudo 3200/tcp brány firewall cmd – zóny = veřejné - přidat port = 3600/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = 3600/tcp sudo firewallu cmd – zóny = veřejného – přidání portu = 3900/tcp – brány firewall trvalé sudo-cmd – zóny = veřejného – přidání portu = sudo 3900/tcp brány firewall cmd – zóny = veřejné - přidat port = 8100/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = brány firewall sudo 8100/tcp-cmd – zóny = veřejného – přidání portu = 50013/tcp – brány firewall trvalé sudo-cmd – zóny = veřejného – přidání portu = sudo 50013/tcp brány firewall cmd – zóny = veřejného – přidání portu = 50014/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = brány firewall 50014/tcp sudo-cmd – zóny = veřejného – přidání portu = 50016/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = 50016/tcp
   # <a name="probe-port-of-ers"></a>Port testu z Lajících
   brány firewall sudo-cmd – zóny = veřejného – přidání portu = 62101/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = brány firewall 62101/tcp sudo-cmd – zóny = veřejné - přidat port = 3301/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = sudo 3301/tcp brány firewall cmd – zóny = veřejného – přidání portu = 50113/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = brány firewall 50113/tcp sudo-cmd – zóny = veřejné - přidat port = 50114/tcp – brány firewall trvalé sudo-cmd – zóny = veřejného – přidání portu = sudo 50114/tcp brány firewall cmd – zóny = veřejné - přidat port = 50116/tcp – brány firewall trvalé sudo-cmd – zóny = veřejné - přidat port = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi/etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>Adresa IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>Adresa IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro SAP NetWeaver ASCS Lajících
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr + můžu/sapmnt/QAS sudo chattr + můžu /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   instalace systému souborů nfs utils uuidd sudo yumu -y
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Přidejte následující řádky do fstab, uložit a ukončit
   192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS systému souborů nfs rw, pevný, parametru rsize = 65536 wsize = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans systému souborů nfs rw, pevný, parametru rsize = 65536 wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo připojení - a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Přidejte následující řádek fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 parametru systému souborů nfs pevný, rsize rw = 65536 wsize = 65536, vers = 3
   
   # <a name="mount"></a>Připojení
   sudo připojení - a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Přidejte následující řádek fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 parametru systému souborů nfs pevný, rsize rw = 65536 wsize = 65536, vers = 3
   
   # <a name="mount"></a>Připojení
   sudo připojení - a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Nastavte vlastnost ResourceDisk.EnableSwap y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Vytvoření a použití stránkovacího souboru na disk prostředků.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Nastavení velikosti ODKLÁDACÍHO souboru s vlastností ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Volné místo disk počítačových prostředků se liší podle velikosti virtuálního počítače. Ujistěte se, že nenastavujte hodnotu, která je příliš velký. Můžete zkontrolovat velikosti odkládacího souboru se příkaz swapon
   # <a name="size-of-the-swapfile"></a>Velikost stránkovacího souboru.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   restart waagent služby sudo
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore seznamu
   ```

   This should list all entries and should look similar to
   ```
   DATOVÝ soubor: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. SOUBOR klíče DAT: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. KLÍČ
   
   VÝCHOZÍ KLÍČE ENV: 192.168.14.4:30313 UŽIVATELE: SAPABAP1 DATABÁZE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su – qasadm hdbuserstore nastavit výchozí qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] přesunutí prostředku počítače # rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~] prostředků počítače # zrušte rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Odebrat nezdařené akce pro Lajících, ke které došlo v rámci migrace
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 vyčištění prostředků počítače #
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Úplný seznam zdrojů:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   
   Nezdařené akce:
   * rsc_sap_QAS_ERS01_monitor_11000 na anftstsapcl1 "neběží" (7): volání = 45, stav = dokončeno, exitreason = ",
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 vyčištění prostředků počítače #
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] # pgrep ms.sapQAS | xargs kill hodnotu -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 vyčištění prostředků počítače # [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 vyčištění prostředků počítače #
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] # pgrep en.sapQAS | xargs kill hodnotu -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ASCS00 vyčištění prostředků počítače # [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 vyčištění prostředků počítače #
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] # pgrep er.sapQAS | xargs kill hodnotu -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 vyčištění prostředků počítače #
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~] # pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Spuštění skupiny prostředků anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Spuštění skupiny prostředků anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    Spuštění anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Začínáme anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Spuštění anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Začínáme anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
