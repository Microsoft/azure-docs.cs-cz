---
title: Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na RHEL | Dokumenty společnosti Microsoft
description: Vytvořte vysokou dostupnost SAP HANA na virtuálních počítačích Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/28/2020
ms.author: radeltch
ms.openlocfilehash: 5e3512ce86bdf96a5e6cfcf0e4459b656a5ac5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565855"
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

Pro místní vývoj můžete použít replikaci systému HANA nebo použít sdílené úložiště k vytvoření vysoké dostupnosti pro SAP HANA.
Ve virtuálních počítačích Azure (VM) je replikace systému HANA v Azure aktuálně jedinou podporovanou funkcí vysoké dostupnosti.
SAP HANA Replication se skládá z jednoho primárního uzlu a alespoň jednoho sekundárního uzlu. Změny dat v primárním uzlu jsou replikovány do sekundárního uzlu synchronně nebo asynchronně.

Tento článek popisuje, jak nasadit a nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat a nakonfigurovat replikaci systému SAP HANA.
V ukázkových konfiguracích se používají instalační příkazy, číslo instance **03**a ID systému HANA **HN1.**

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a operační systém (OS) a databázové kombinace.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu (tento článek)][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [Replikace systému SAP HANA v clusteru kardiostimulátoru](https://access.redhat.com/articles/3004101)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti sap HANA je nainstalován na dvou virtuálních počítačích. Data jsou replikována pomocí replikace systému HANA.

![SAP HANA přehled vysoké dostupnosti](./media/sap-hana-high-availability-rhel/ha-hana.png)

Nastavení systémové replikace SAP HANA používá vyhrazený virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení:

* Front-end konfigurace: IP adresa 10.0.0.13 pro hn1-db
* Back-endkonfigurace: Připojení k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí replikace systému HANA
* Port sondy: Port 62503
* Pravidla vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Azure Marketplace obsahuje image pro Red Hat Enterprise Linux 7.4 pro SAP HANA, kterou můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Můžete použít jednu ze šablon rychlého startu, které jsou na GitHubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, nástroj pro vyrovnávání zatížení, sadu dostupnosti a tak dále.
Chcete-li šablonu nasadit, postupujte takto:

1. Otevřete [šablonu databáze][template-multisid-db] na webu Azure Portal.
1. Zadejte následující parametry:
    * **SAP System ID**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazeny.
    * **Typ operačního systému**: Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte **RHEL 7**.
    * **Typ db**: Vyberte **možnost HANA**.
    * **Velikost systému SAP**: Zadejte počet SAPS, který nový systém poskytne. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
    * **Dostupnost systému**: Vyberte **možnost HA**.
    * **Admin Uživatelské jméno, Admin Heslo nebo SSH klíč**: Nový uživatel je vytvořen, který lze použít k přihlášení k počítači.
    * **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako **/subscriptions/\<subscription ID\<>/resourceGroups/ název skupiny prostředků\<>/providers/Microsoft.Network/virtualNetworks/ název virtuální sítě>/podsítě/\<název podsítě>**. Pokud chcete vytvořit novou virtuální síť, nechte prázdné

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvořte sadu dostupnosti.  
   Nastavte doménu s maximální aktualizací.
1. Vytvořte systém pro vyrovnávání zatížení (interní). Doporučujeme [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   * Vyberte virtuální síť vytvořenou v kroku 2.
1. Vytvořte virtuální počítač 1.  
   Použijte alespoň Red Hat Enterprise Linux 7.4 pro SAP HANA. Tento příklad používá Red Hat Enterprise Linux 7.4 pro SAP HANA image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Vyberte sadu dostupnosti vytvořenou v kroku 3.
1. Vytvořte virtuální počítač 2.  
   Použijte alespoň Red Hat Enterprise Linux 7.4 pro SAP HANA. Tento příklad používá Red Hat Enterprise Linux 7.4 pro SAP HANA image <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Vyberte sadu dostupnosti vytvořenou v kroku 3.
1. Přidejte datové disky.
1. Pokud používáte standardní nástroj pro vyrovnávání zatížení, postupujte takto:
   1. Nejprve vytvořte fond front-endIP:

      1. Otevřete balancer na zatížení, vyberte **front-endový fond IP**adres a vyberte **Přidat**.
      1. Zadejte název nového front-endového fondu IP (například **hana-frontend).**
      1. Nastavte **přiřazení** na **statickou** a zadejte adresu IP (například **10.0.0.13).**
      1. Vyberte **OK**.
      1. Po vytvoření nového front-endového fondu IP adres si poznamenejte IP adresu fondu.

   1. Dále vytvořte back-endový fond:

      1. Otevřete balancer, vyberte **back-endové fondy**a vyberte **Přidat**.
      1. Zadejte název nového back-endového fondu (například **hana-backend).**
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte ** Virtuální počítač**.
      1. Vyberte virtuální počítače clusteru SAP HANA a jejich IP adresy.
      1. Vyberte **Přidat**.

   1. Dále vytvořte sondu stavu:

      1. Otevřete systém vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **hana-hp**).
      1. Jako protokol a port 625**03**vyberte **protokol TCP** . Ponechte hodnotu **Interval** nastavenou na hodnotu 5 a prahovou hodnotu **Nefunkční** nastavenou na hodnotu 2.
      1. Vyberte **OK**.

   1. Dále vytvořte pravidla vyrovnávání zatížení:
   
      1. Otevřete správce zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla pro vyrovnávání zatížení (například **hana-lb**).
      1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **hana-frontend**, **hana-backend** a **hana-hp).**
      1. Vyberte **porty HA**.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že **povolit plovoucí IP**.
      1. Vyberte **OK**.

   > [!Note]
   > Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

1. Případně pokud váš scénář diktuje pomocí základnínástroj pro vyrovnávání zatížení, postupujte podle následujících kroků konfigurace:
   1. Nakonfigurujte provynaci zatížení. Nejprve vytvořte fond front-endIP:

      1. Otevřete balancer na zatížení, vyberte **front-endový fond IP**adres a vyberte **Přidat**.
      1. Zadejte název nového front-endového fondu IP (například **hana-frontend).**
      1. Nastavte **přiřazení** na **statickou** a zadejte adresu IP (například **10.0.0.13).**
      1. Vyberte **OK**.
      1. Po vytvoření nového front-endového fondu IP adres si poznamenejte IP adresu fondu.

   1. Dále vytvořte back-endový fond:

      1. Otevřete balancer, vyberte **back-endové fondy**a vyberte **Přidat**.
      1. Zadejte název nového back-endového fondu (například **hana-backend).**
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte sadu dostupnosti vytvořenou v kroku 3.
      1. Vyberte virtuální počítače clusteru SAP HANA.
      1. Vyberte **OK**.

   1. Dále vytvořte sondu stavu:

      1. Otevřete systém vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **hana-hp**).
      1. Jako protokol a port 625**03**vyberte **protokol TCP** . Ponechte hodnotu **Interval** nastavenou na hodnotu 5 a prahovou hodnotu **Nefunkční** nastavenou na hodnotu 2.
      1. Vyberte **OK**.

   1. Pro SAP HANA 1.0 vytvořte pravidla vyrovnávání zatížení:

      1. Otevřete správce zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla pro vyrovnávání zatížení (například hana-lb-3**03**15).
      1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **hana-frontend).**
      1. Podržte **protokol** nastavený na **protokol TCP**a zadejte port 3**03**15.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že **povolit plovoucí IP**.
      1. Vyberte **OK**.
      1. Opakujte tyto kroky pro port 3**03**17.

   1. Pro SAP HANA 2.0 vytvořte pravidla vyrovnávání zatížení pro systémovou databázi:

      1. Otevřete správce zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla pro vyrovnávání zatížení (například hana-lb-3**03**13).
      1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **hana-frontend).**
      1. Podržte **protokol** nastavený na **protokol TCP**a zadejte port 3**03**13.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že **povolit plovoucí IP**.
      1. Vyberte **OK**.
      1. Opakujte tyto kroky pro port 3**03**14.

   1. Pro SAP HANA 2.0 nejprve vytvořte pravidla vyrovnávání zatížení pro databázi klienta:

      1. Otevřete správce zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla pro vyrovnávání zatížení (například hana-lb-3**03**40).
      1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **hana-frontend).**
      1. Podržte **protokol** nastavený na **protokol TCP**a zadejte port 3**03**40.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že **povolit plovoucí IP**.
      1. Vyberte **OK**.
      1. Opakujte tyto kroky pro porty 3**03**41 a 3**03**42.

Další informace o požadovaných portech pro SAP HANA načtete v kapitole [Připojení k databázím klientů](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) v průvodci [databázemi klienta SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) nebo [v poznámce SAP 2388694][2388694].

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Viz také poznámka SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:

* **[A]**: Krok platí pro všechny uzly.
* **[1]**: Krok platí pouze pro uzel 1.
* **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

1. **[A]** Nastavení rozložení disku: **Logický správce svazků (LVM)**.

   Doporučujeme používat LVM pro svazky, které ukládají data a soubory protokolu. Následující příklad předpokládá, že virtuální počítače mají připojené čtyři datové disky, které se používají k vytvoření dvou svazků.

   Seznam všech dostupných disků:

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

   Vytvořte logické svazky. Lineární svazek se vytvoří `lvcreate` při `-i` použití bez přepínače. Doporučujeme vytvořit pruhovaný svazek pro lepší výkon vstupně-va, kde `-i` argumentby měl být číslo základního fyzického svazku. V tomto dokumentu se pro objem dat používají dva `-i` fyzické svazky, takže argument přepínače je nastaven na **2**. Pro svazek protokolu se používá jeden `-i` fyzický svazek, takže se explicitně nepoužívá žádný přepínač. Použijte `-i` přepínač a nastavte jej na číslo základního fyzického svazku, pokud pro každé datové svazek, protokol nebo sdílené svazky použijete více než jeden fyzický svazek.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Vytvořte adresáře připojení a zkopírujte UUID všech logických svazků:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Vytvořte `fstab` položky pro tři logické svazky:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Do `/etc/fstab` souboru vložte následující řádek:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Připojte nové svazky:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Nastavení rozložení disku: **Plain Disks**.

   U ukázkových systémů můžete data HANA a soubory protokolu umístit na jeden disk. Vytvořte oddíl na /dev/disk/azure/scsi1/lun0 a naformátujte jej pomocí xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Vložte tento řádek do souboru /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Vytvořte cílový adresář a připojte disk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Nastavte překlad názvů hostitele pro všechny hostitele.

   Můžete použít server DNS nebo upravit soubor /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do souboru /etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL pro konfiguraci HANA

   Nakonfigurujte RHEL, jak je popsáno v poznámkách SAP [2292690] a [2455582] a <https://access.redhat.com/solutions/2447641>.

1. **[A]** Instalace SAP HANA

   Chcete-li nainstalovat systémovou <https://access.redhat.com/articles/3004101>replikaci SAP HANA, postupujte podle .

   * Spusťte **hdblcm** program z DISKU DVD HANA. Na výzvu zadejte následující hodnoty:
   * Zvolte instalaci: Zadejte **1**.
   * Vyberte další součásti pro instalaci: Zadejte **1**.
   * Zadejte Instalační cestu [/hana/shared]: Vyberte Enter.
   * Zadejte název místního hostitele [..]: Vyberte Enter.
   * Chcete do systému přidat další hostitele? (a/n) [n]: Vyberte Enter.
   * Zadejte ID systému SAP HANA: Zadejte SID HANA, například: **HN1**.
   * Zadejte číslo instance [00]: Zadejte číslo instance HANA. Zadejte **03,** pokud jste použili šablonu Azure nebo postupovali podle části ruční nasazení tohoto článku.
   * Vyberte režim databáze / zadejte index [1]: Vyberte Enter.
   * Vyberte Možnost Využití systému / Zadejte index [4]: Vyberte hodnotu využití systému.
   * Zadejte umístění datových svazků [/hana/data/HN1]: Vyberte Enter.
   * Zadejte umístění svazků protokolu [/hana/log/HN1]: Vyberte Enter.
   * Omezit maximální přidělení paměti? [n]: Vyberte Enter.
   * Zadejte název hostitele certifikátu pro hostitele '...' [...]: Vyberte Enter.
   * Zadejte heslo uživatele hostitelského agenta SAP (sapadm): Zadejte heslo uživatele hostitelského agenta.
   * Potvrďte heslo uživatele hostitele SAP (sapadm): Znovu zadejte heslo uživatele hostitelského agenta pro potvrzení.
   * Zadejte heslo správce systému (hdbadm): Zadejte heslo správce systému.
   * Potvrdit heslo správce systému (hdbadm): Znovu zadejte heslo správce systému pro potvrzení.
   * Zadejte domovský adresář správce systému [/usr/sap/HN1/home]: Vyberte Enter.
   * Zadejte přihlašovací prostředí správce systému [/bin/sh]: Vyberte Enter.
   * Zadejte ID uživatele správce systému [1001]: Vyberte Enter.
   * Zadejte ID skupiny uživatelů (sapsys) [79]: Vyberte Enter.
   * Zadejte heslo uživatele databáze (SYSTEM): Zadejte uživatelské heslo databáze.
   * Potvrdit heslo uživatele databáze (SYSTEM): Znovu zadejte uživatelské heslo databáze pro potvrzení.
   * Restartujte systém po restartu počítače? [n]: Vyberte Enter.
   * Chcete pokračovat? (a/n): Ověřte souhrn. Zadejte **y,** chcete-li pokračovat.

1. **[A]** Inovujte hostitelského agenta SAP.

   Stáhněte si nejnovější archiv sap host agenta z [SAP Software Center][sap-swcenter] a spusťte následující příkaz pro upgrade agenta. Nahraďte cestu k archivu tak, aby přecštoval na stažený soubor:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** Konfigurace brány firewall

   Vytvořte pravidlo brány firewall pro port sondy Azure pro vyrovnávání zatížení.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace systémové replikace SAP HANA 2.0

Kroky v této části používají následující předpony:

* **[A]**: Krok platí pro všechny uzly.
* **[1]**: Krok platí pouze pro uzel 1.
* **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

1. **[A]** Konfigurace brány firewall

   Vytvořte pravidla brány firewall, která umožní replikaci systému HANA a přenosy klientů. Požadované porty jsou uvedeny na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem pro povolení replikace systému HANA 2.0 a klientského provozu do databáze SYSTEMDB, HN1 a NW1.

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

1. **[1]** Vytvořte databázi klienta.

   Pokud používáte SAP HANA 2.0 nebo MDC, vytvořte databázi klienta pro váš systém SAP NetWeaver. Vyměňte **NW1** za SID vašeho systému SAP.

   Provést jako <\>hanasid adm následující příkaz:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurace systémové replikace na prvním uzlu:

   Zálohování databází jako <hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Zkopírujte systémové soubory PKI do sekundární lokality:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Vytvořte primární lokalitu:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurace systémové replikace na druhém uzlu:
    
   Zaregistrujte druhý uzel a spusťte replikaci systému. Spusťte následující příkaz <\>hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** Kontrola stavu replikace

   Zkontrolujte stav replikace a počkejte, dokud nebudou synchronizovány všechny databáze. Pokud stav zůstává NEZNÁMÝ, zkontrolujte nastavení brány firewall.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace systémové replikace SAP HANA 1.0

Kroky v této části používají následující předpony:

* **[A]**: Krok platí pro všechny uzly.
* **[1]**: Krok platí pouze pro uzel 1.
* **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

1. **[A]** Konfigurace brány firewall

   Vytvořte pravidla brány firewall, která umožní replikaci systému HANA a přenosy klientů. Požadované porty jsou uvedeny na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem pro povolení replikace systému HANA 2.0. Přizpůsobte jej instalaci SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Vytvořte požadované uživatele.

   Spusťte následující příkaz jako root. Ujistěte se, že nahradit tučné řetězce (HANA System ID **HN1** a číslo instance **03**) s hodnotami instalace SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Vytvořte položku úložiště klíčů.

   Spusťte následující příkaz jako root a vytvořte novou položku úložiště klíčů:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Záloha databáze.

   Záloha databází jako root:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Pokud používáte víceklientskou instalaci, zálohujte také databázi klienta:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Konfigurace systémové replikace v prvním uzlu.

   Vytvořte primární lokalitu jako\><hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurace systémové replikace v sekundárním uzlu.

   Zaregistrujte sekundární lokalitu jako\><hanasid adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na Red Hat Enterprise Linux u Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte základní cluster Pacemakeru pro tento server HANA.

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

Nainstalujte agenty prostředků SAP HANA do **všech uzlů**. Ujistěte se, že povolit úložiště, které obsahuje balíček.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Dále vytvořte topologii HANA. Spusťte následující příkazy na jednom z uzlů clusteru Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Dále vytvořte prostředky HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

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

Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

> [!NOTE]
> Časové tomožné časové osy ve výše uvedené konfiguraci jsou pouze příklady a může být nutné přizpůsobit konkrétní nastavení HANA. Například budete muset zvýšit časový čas spuštění, pokud trvá déle spuštění databáze SAP HANA.  

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

## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

Tato část popisuje, jak můžete otestovat nastavení. Před zahájením testu se ujistěte, že Pacemaker nemá žádnou neúspěšnou akci (prostřednictvím stavu pcs), neexistují žádná neočekávaná omezení umístění (například zbytky testu migrace) a že HANA je stav synchronizace, například s systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testování migrace

Stav prostředku před zahájením testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Hlavní uzel SAP HANA můžete migrovat provedením následujícího příkazu:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"`, tento příkaz by měl migrovat hlavní uzel SAP HANA a skupinu, která obsahuje virtuální IP adresu na hn1-db-1.

Jakmile je migrace hotová, výstup "sudo pcs status" vypadá takto

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Prostředek SAP HANA na hn1-db-0 je zastaven. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migrace vytvoří omezení umístění, která je třeba znovu odstranit:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Sledujte stav prostředku HANA pomocí stavu pcs. Po spuštění HANA na hn1-db-0 by výstup měl vypadat takto.

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testování agenta oplocení Azure

Stav prostředku před zahájením testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Nastavení agenta oplocení Azure můžete otestovat zakázáním síťového rozhraní v uzlu, kde sap HANA běží jako hlavní.
Informace o tom, jak simulovat selhání sítě, naleznete v [článku 79523 znalostní báze Red Hat Knowledgebase.](https://access.redhat.com/solutions/79523) V tomto příkladu používáme net_breaker skript blokovat veškerý přístup k síti.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Virtuální počítač by se teď měl restartovat nebo zastavit v závislosti na konfiguraci clusteru.
Pokud nastavíte `stonith-action` nastavení vypnout, virtuální počítač se zastaví a prostředky se migrují do spuštěného virtuálního počítače.

> [!NOTE]
> Může trvat až 15 minut, než budou virtuální počítače znovu online.

Po spuštění virtuálního počítače znovu, SAP HANA prostředek se nezdaří spustit jako sekundární, pokud nastavíte `AUTOMATED_REGISTER="false"`. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stav zdroje po zkoušce:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testování ručního převzetí služeb při selhání

Stav prostředku před zahájením testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ruční převzetí služeb při selhání můžete otestovat zastavením clusteru v uzlu hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po převzetí služeb při selhání můžete cluster znovu spustit. Pokud nastavíte `AUTOMATED_REGISTER="false"`, prostředek SAP HANA na uzlu hn1-db-0 se nespustí jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stav zdroje po zkoušce:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)
