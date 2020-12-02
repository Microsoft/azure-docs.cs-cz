---
title: Vysoká dostupnost SAP HANA na virtuálních počítačích Azure v RHEL | Microsoft Docs
description: Navažte vysokou dostupnost SAP HANA na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 277ed8ad5f9888daa911cb3b5c7dcf00fd285bf4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489152"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Pro místní vývoj můžete k zajištění vysoké dostupnosti pro SAP HANA použít buď replikaci systému HANA, nebo použít sdílené úložiště.
Na virtuálních počítačích Azure je replikace systému HANA v Azure aktuálně jedinou podporovanou funkcí vysoké dostupnosti.
Replikace SAP HANA se skládá z jednoho primárního uzlu a alespoň jednoho sekundárního uzlu. Změny dat v primárním uzlu jsou replikovány do sekundárního uzlu synchronně nebo asynchronně.

Tento článek popisuje nasazení a konfiguraci virtuálních počítačů, instalaci architektury clusterů a instalaci a konfiguraci replikace systému SAP HANA.
V ukázkových konfiguracích se používají instalační příkazy, číslo instance **03** a ID systému Hana **HN1** .

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Poznámka SAP Poznámka [2002167] obsahuje doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Replikace SAP HANA systému v clusteru Pacemaker](https://access.redhat.com/articles/3004101)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentace k RHEL specifické pro Azure:
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Přehled

Aby se dosáhlo vysoké dostupnosti, SAP HANA je nainstalovaná na dvou virtuálních počítačích. Data se replikují pomocí replikace systému HANA.

![Přehled SAP HANA vysoké dostupnosti](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA instalace replikace systému používá vyhrazený virtuální hostitelský název a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení:

* Konfigurace front-endu: IP adresa 10.0.0.13 pro HN1-DB
* Konfigurace back-endu: připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí replikace systému HANA
* Port testu paměti: port 62503
* Pravidla vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Azure Marketplace obsahuje image pro Red Hat Enterprise Linux 7,4 pro SAP HANA, kterou můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení s využitím šablony

K nasazení všech požadovaných prostředků můžete použít jednu z šablon pro rychlý Start, které jsou na GitHubu. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti a tak dále.
K nasazení šablony použijte následující postup:

1. Otevřete [šablonu databáze][template-multisid-db] na Azure Portal.
1. Zadejte následující parametry:
    * **ID systému SAP**: Zadejte ID systému SAP pro systém SAP, který chcete nainstalovat. ID se používá jako předpona pro nasazené prostředky.
    * **Typ operačního systému**: vyberte jednu z distribucí systému Linux. V tomto příkladu vyberte **RHEL 7**.
    * **Typ databáze**: vyberte **Hana**.
    * **Velikost systému SAP**: zadejte počet SAP, který bude nový systém poskytovat. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
    * **Dostupnost systému**: vyberte **ha**.
    * **Uživatelské jméno správce, heslo správce nebo klíč SSH**: vytvoří se nový uživatel, který se dá použít k přihlášení k počítači.
    * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Pokud chcete vytvořit novou virtuální síť, ponechejte tuto položku prázdnou.

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvořte skupinu dostupnosti.  
   Nastavte maximální doménu aktualizace.
1. Vytvořte Nástroj pro vyrovnávání zatížení (interní). Doporučujeme [standardní nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md).
   * Vyberte virtuální síť vytvořenou v kroku 2.
1. Vytvořte virtuální počítač 1.  
   Pro SAP HANA použijte minimálně Red Hat Enterprise Linux 7,4. V tomto příkladu se používá Red Hat Enterprise Linux 7,4 pro SAP HANA image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Vyberte skupinu dostupnosti vytvořenou v kroku 3.
1. Vytvořte virtuální počítač 2.  
   Pro SAP HANA použijte minimálně Red Hat Enterprise Linux 7,4. V tomto příkladu se používá Red Hat Enterprise Linux 7,4 pro SAP HANA image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Vyberte skupinu dostupnosti vytvořenou v kroku 3.
1. Přidejte datové disky.

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.    

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

1. Pokud používáte standardní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
   1. Nejprve vytvořte front-end fond IP adres:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond** a vyberte **Přidat**.
      1. Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.0.0.13**).
      1. Vyberte **OK**.
      1. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.

   1. Dále vytvořte fond back-end:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu** a vyberte **Přidat**.
      1. Zadejte název nového fondu back-end (například **Hana-back-end**).
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte * * virtuální počítač * *.
      1. Vyberte virtuální počítače SAP HANA clusteru a jejich IP adresy.
      1. Vyberte **Přidat**.

   1. Potom vytvořte sondu stavu:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu** a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **Hana-HP**).
      1. Jako protokol a port 625 **03** vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
      1. Vyberte **OK**.

   1. Dále vytvořte pravidla vyrovnávání zatížení:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení** a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **Hana-kg**).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**, **Hana-back-endu** a **Hana-HP**).
      1. Vyberte **porty ha**.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.


1. Případně, pokud váš scénář používá základní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
   1. Nakonfigurujte Nástroj pro vyrovnávání zatížení. Nejprve vytvořte front-end fond IP adres:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond** a vyberte **Přidat**.
      1. Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.0.0.13**).
      1. Vyberte **OK**.
      1. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.

   1. Dále vytvořte fond back-end:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu** a vyberte **Přidat**.
      1. Zadejte název nového fondu back-end (například **Hana-back-end**).
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte skupinu dostupnosti vytvořenou v kroku 3.
      1. Vyberte virtuální počítače clusteru SAP HANA.
      1. Vyberte **OK**.

   1. Potom vytvořte sondu stavu:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu** a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **Hana-HP**).
      1. Jako protokol a port 625 **03** vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
      1. Vyberte **OK**.

   1. Pro SAP HANA 1,0 vytvořte pravidla vyrovnávání zatížení:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení** a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3 **03** 15).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
      1. Zachovejte **protokol** nastaven na **TCP** a zadejte port 3 **03** 15.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tento postup pro port 3 **03** 17.

   1. Pro SAP HANA 2,0 vytvořte pravidla vyrovnávání zatížení pro systémovou databázi:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení** a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3 **03** 13).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
      1. Zachovejte **protokol** nastaven na **TCP** a zadejte port 3 **03** 13.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tento postup pro port 3 **03** 14.

   1. Pro SAP HANA 2,0 nejprve vytvořte pravidla vyrovnávání zatížení pro databázi tenanta:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení** a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3 **03** 40).
      1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **Hana-Endu**).
      1. Zachovejte **protokol** nastaven na **TCP** a zadejte port 3 **03** 40.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tyto kroky pro porty 3 **03** 41 a 3 **03** 42.

Další informace o požadovaných portech pro SAP HANA naleznete v kapitole [připojení k databázím tenantů](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) v průvodci [SAP HANA databáze klienta](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) nebo v tématu [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Viz také SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:

* **[A]**: krok platí pro všechny uzly.
* **[1]**: krok platí pouze pro uzel 1.
* **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

1. **[A]** nastavte rozložení disku: **Správce logických svazků (LVM)**.

   Pro svazky, které ukládají data a soubory protokolů, doporučujeme použít LVM. Následující příklad předpokládá, že virtuální počítače mají připojené čtyři datové disky, které se používají k vytvoření dvou svazků.

   Vypíše všechny dostupné disky:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Příklad výstupu:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvořte fyzické svazky pro všechny disky, které chcete použít:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvořte skupinu svazků pro datové soubory. Pro soubory protokolu použijte jednu skupinu svazků a jednu pro sdílený adresář SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvořte logické svazky. Při použití bez přepínače se vytvoří lineární svazek `lvcreate` `-i` . Doporučujeme vytvořit prokládaný svazek pro lepší vstupně-výstupní výkon a zarovnat velikosti pruhů k hodnotám popsaným v [SAP HANA konfiguracích úložiště virtuálních počítačů](./hana-vm-operations-storage.md). `-i`Argument by měl být počet základních fyzických svazků a `-I` argumentem je velikost pruhu. V tomto dokumentu se pro datový svazek používají dva fyzické svazky, takže `-i` je argument přepínače nastavený na **2**. Velikost pruhu pro datový svazek je **256KiB**. Pro svazek protokolu se používá jeden fyzický svazek, takže žádné `-i` nebo `-I` přepínače nejsou explicitně použity pro příkazy svazků protokolu.  

   > [!IMPORTANT]
   > Použijte `-i` přepínač a nastavte jej na číslo základního fyzického svazku, pokud pro každé z nich používáte více než jeden fyzický svazek. `-I`Při vytváření prokládaného svazku použijte přepínač k určení velikosti pruhu.  
   > Doporučené konfigurace úložiště, včetně velikosti pruhů a počtu disků, najdete v tématu [SAP HANA konfigurace úložiště virtuálních počítačů](./hana-vm-operations-storage.md) .  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Vytvořte adresáře připojení a zkopírujte identifikátor UUID všech logických svazků:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Vytvořte `fstab` položky pro tři logické svazky:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Do souboru vložte následující řádek `/etc/fstab` :

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Připojte nové svazky:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** nastavte rozložení disku: **prosté disky**.

   Pro ukázkové systémy můžete umístit data a soubory protokolu HANA na jeden disk. Vytvořte oddíl na/dev/disk/Azure/scsi1/lun0 a naformátujte ho pomocí xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Vložte tento řádek do souboru/etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Vytvořte cílový adresář a připojte disk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** nastavte překlad názvů hostitelů pro všechny hostitele.

   Můžete buď použít server DNS, nebo upravit soubor/etc/hosts na všech uzlech. V tomto příkladu se dozvíte, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Do souboru/etc/hosts vložte následující řádky. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL pro konfiguraci Hana

   Nakonfigurujte RHEL podle popisu v tématu <https://access.redhat.com/solutions/2447641> a v následujících komentářích SAP:  
   - [2292690-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: spouštění aplikací SAP s použitím RSZ 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: běžící aplikace SAP s aplikací RSZ 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: běžící aplikace SAP s aplikací RSZ 9. x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** instalace SAP HANA

   Pro instalaci replikace systému SAP HANA postupujte podle pokynů <https://access.redhat.com/articles/3004101> .

   * Spusťte program **hdblcm** z disku DVD Hana. Na příkazovém řádku zadejte následující hodnoty:
   * Vyberte možnost instalace: zadejte **1**.
   * Vyberte další komponenty k instalaci: zadejte **1**.
   * Zadejte instalační cestu [/Hana/Shared]: vyberte Enter.
   * Zadejte název místního hostitele [..]: vyberte Enter.
   * Chcete přidat další hostitele do systému? (a/n) [n]: vyberte Enter.
   * Zadejte ID SAP HANA systému: zadejte SID HANA, například: **HN1**.
   * Zadejte číslo instance [00]: zadejte číslo instance HANA. Pokud jste použili šablonu Azure nebo postupovali podle části Ruční nasazení tohoto článku, zadejte **03** .
   * Vyberte režim databáze/zadejte index [1]: vyberte Enter.
   * Vyberte využití systému/zadejte index [4]: vyberte hodnotu využití systému.
   * Zadejte umístění datových svazků [/hana/data/HN1]: vyberte Enter.
   * Zadejte umístění svazků protokolu [/hana/log/HN1]: vyberte Enter.
   * Omezit maximální přidělení paměti? [n]: vyberte Enter.
   * Zadejte název hostitele certifikátu pro hostitele... [...]: Vyberte Enter.
   * Zadejte heslo uživatele agenta SAP (sapadm): zadejte heslo uživatele agenta hostitele.
   * Potvrďte heslo uživatele agenta SAP hostitele (sapadm): Zadejte znovu heslo uživatele agenta hostitele a potvrďte ho.
   * Zadejte heslo správce systému (hdbadm): zadejte heslo správce systému.
   * Potvrzení hesla správce systému (hdbadm): zadejte heslo správce systému znovu a potvrďte ho.
   * Zadejte domovský adresář správce systému [/usr/sap/HN1/home]: vyberte Enter.
   * Zadejte prostředí přihlášení správce systému [/bin/sh]: vyberte Enter.
   * Zadejte ID uživatele správce systému [1001]: vyberte Enter.
   * Zadejte ID skupiny uživatelů (sapsys) [79]: vyberte Enter.
   * Zadejte heslo uživatele databáze (systém): zadejte heslo uživatele databáze.
   * Potvrďte heslo uživatele databáze (systém): zadejte heslo uživatele databáze a potvrďte ho.
   * Restartovat systém po restartování počítače? [n]: vyberte Enter.
   * Chcete pokračovat? (a/n): Ověřte souhrn. Pokračujte zadáním **y** .

1. **[A]** Upgradujte agenta hostitele SAP.

   Stáhněte si nejnovější archiv agenta hostitele SAP z [centra softwaru SAP][sap-swcenter] a spuštěním následujícího příkazu Upgradujte agenta. Nahraďte cestu k archivu, který bude odkazovat na stažený soubor:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** konfigurace brány firewall

   Vytvořte pravidlo brány firewall pro port testu služby Azure Load Balancer.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace replikace systému SAP HANA 2,0

Kroky v této části používají následující předpony:

* **[A]**: krok platí pro všechny uzly.
* **[1]**: krok platí pouze pro uzel 1.
* **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

1. **[A]** konfigurace brány firewall

   Vytvořte pravidla brány firewall, která umožní replikaci systému HANA a provoz klienta. Požadované porty jsou uvedené na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem povolení replikace systému HANA 2,0 a klientského provozu do databáze SYSTEMDB, HN1 a NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** vytvořte databázi tenanta.

   Pokud používáte SAP HANA 2,0 nebo MDC, vytvořte databázi tenanta pro systém SAP NetWeaver. Nahraďte **NW1** identifikátorem SID vašeho systému SAP.

   Provést jako <hanasid \> ADM následující příkaz:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** nakonfigurujte replikaci systému na prvním uzlu:

   Zálohujte databáze jako <hanasid \> ADM:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Zkopírujte systémové soubory PKI do sekundární lokality:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Vytvořit primární lokalitu:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** nakonfigurujte replikaci systému na druhém uzlu:
    
   Zaregistrujte druhý uzel pro spuštění replikace systému. Spusťte následující příkaz <hanasid \> ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** kontrolovat stav replikace

   Ověřte stav replikace a počkejte, dokud nebudou všechny databáze synchronizovány. Pokud stav zůstane neznámý, ověřte nastavení brány firewall.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace replikace systému SAP HANA 1,0

Kroky v této části používají následující předpony:

* **[A]**: krok platí pro všechny uzly.
* **[1]**: krok platí pouze pro uzel 1.
* **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

1. **[A]** konfigurace brány firewall

   Vytvořte pravidla brány firewall, která umožní replikaci systému HANA a provoz klienta. Požadované porty jsou uvedené na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem povolení replikace systému HANA 2,0. Přizpůsobte ho k instalaci SAP HANA 1,0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** vytvořit požadované uživatele.

   Spusťte následující příkaz jako kořenový. Nezapomeňte nahradit tučné řetězce (ID systému HANA **HN1** a číslo instance **03**) hodnotami instalace SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** vytvořte položku úložiště klíčů.

   Spusťte následující příkaz jako kořenový adresář pro vytvoření nové položky úložiště klíčů:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** zálohování databáze.

   Zálohovat databáze jako kořen:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Pokud používáte víceklientské instalace, zálohujte také databázi tenanta:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** nakonfigurujte replikaci systému na prvním uzlu.

   Vytvořte primární lokalitu jako <hanasid \> ADM:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** nakonfigurujte replikaci systému na sekundárním uzlu.

   Zaregistrujte sekundární lokalitu jako <hanasid \> ADM:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento server Hana základní cluster Pacemaker.

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

Nainstalujte agenty SAP HANA prostředků na **všech uzlech**. Ujistěte se, že jste povolili úložiště, které obsahuje balíček. Pokud používáte bitovou kopii s povoleným RHEL 8. x HA, nemusíte povolit další úložiště.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Pak vytvořte topologii HANA. Na jednom z uzlů clusteru Pacemaker spusťte následující příkazy:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Pak vytvořte prostředky HANA.

> [!NOTE]
> Tento článek obsahuje odkazy na *podřízený* termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

Pokud vytváříte cluster na **RHEL 7. x**, použijte následující příkazy:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Pokud vytváříte cluster na **RHEL 8. x**, použijte následující příkazy:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky. Není důležité, na kterém uzlu jsou prostředky spuštěné.

> [!NOTE]
> Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení HANA. Například může být nutné prodloužit časový limit spuštění, pokud bude trvat déle, než se spustí databáze SAP HANA.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Tato část popisuje, jak můžete otestovat instalaci. Než začnete s testem, ujistěte se, že Pacemaker nemá žádnou neúspěšnou akci (přes stav počítačů), neexistují žádná neočekávaná omezení umístění (například Leftovers testu migrace) a tento HANA je ve stavu synchronizace, například s systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testování migrace

Stav prostředku před spuštěním testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

SAP HANA hlavní uzel můžete migrovat spuštěním následujícího příkazu:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"` , tento příkaz by měl migrovat SAP HANA hlavní uzel a skupinu obsahující virtuální IP adresu na HN1-DB-1.

Po dokončení migrace bude výstup "sudo PC status" vypadat takto

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Prostředek SAP HANA na HN1-DB-0 se zastavil. V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migrace vytvoří omezení umístění, která je potřeba odstranit znovu:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitoruje stav prostředku HANA pomocí stavu počítače. Jakmile se HANA spustí na HN1-DB-0, výstup by měl vypadat nějak takto:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testování agenta pro oplocení Azure

> [!NOTE]
> Tento článek obsahuje odkazy na *podřízený* termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.  

Stav prostředku před spuštěním testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Instalaci agenta pro oplocení Azure můžete otestovat zakázáním síťového rozhraní na uzlu, ve kterém je SAP HANA spuštěná jako hlavní.
Popis způsobu simulace selhání sítě najdete v [článku znalostní báze Red Hat 79523](https://access.redhat.com/solutions/79523) . V tomto příkladu používáme skript net_breaker k blokování veškerého přístupu k síti.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

V závislosti na konfiguraci clusteru by se teď měl virtuální počítač restartovat nebo zastavit.
Pokud nastavíte `stonith-action` nastavení na vypnuto, virtuální počítač se zastaví a prostředky se migrují na běžící virtuální počítač.

Po opětovném spuštění virtuálního počítače se prostředek SAP HANA nepovede spustit jako sekundární, pokud jste nastavili `AUTOMATED_REGISTER="false"` . V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Stav prostředku po testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Test ručního převzetí služeb při selhání

Stav prostředku před spuštěním testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ruční převzetí služeb při selhání můžete otestovat zastavením clusteru na uzlu HN1-DB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po převzetí služeb při selhání můžete cluster znovu spustit. Pokud nastavíte `AUTOMATED_REGISTER="false"` , SAP HANA prostředku na uzlu HN1-DB-0 se nepovede spustit jako sekundární. V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Stav prostředku po testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Test ručního převzetí služeb při selhání

Stav prostředku před spuštěním testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ruční převzetí služeb při selhání můžete otestovat zastavením clusteru na uzlu HN1-DB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* [SAP HANA konfigurace úložiště virtuálních počítačů](./hana-vm-operations-storage.md)