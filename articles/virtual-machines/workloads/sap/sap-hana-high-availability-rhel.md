---
title: Nastavení systémové replikace SAP HANA na Azure virtual machines (VMs) | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti SAP Hana na Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 637e926676c727c01c60fe0d3e4e758173bdbd18
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637559"
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

Pro místní vývoj můžete použít buď HANA System Replication nebo používat sdílené úložiště k vytvoření vysoké dostupnosti pro SAP HANA.
Na Azure virtual machines (VM) systémové replikace HANA v Azure je aktuálně že jediný podporovaný funkce vysoké dostupnosti.
Replikace SAP HANA se skládá z jedné primární a alespoň jeden sekundární uzel. Změny dat na primárním uzlu jsou replikovány na sekundární uzel synchronně nebo asynchronně.

Tento článek popisuje, jak nasadit a nakonfigurovat službu virtual machines, nainstalujte rozhraní clusteru a nainstalujte a nakonfigurujte SAP HANA System Replication.
V ukázkové konfigurace příkazy instalace instance číslo **03**a ID systému HANA **HN1** se používají.

Přečtěte si následující poznámky SAP a Paper nejprve:

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP.
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure.
  * Podporované SAP software a operační systém (OS) a kombinace databáze.
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure.
* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2002167] má doporučené nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP [2009879] obsahuje pokyny pro SAP HANA pro Red Hat Enterprise Linux
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [Replikace systému SAP HANA v pacemaker clusteru](https://access.redhat.com/articles/3004101)
* Dokumentace ke službě obecné RHEL
  * [Přehled doplňků vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňku vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Konkrétní RHEL dokumentace k Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, se SAP HANA nainstaluje na dva virtuální počítače. Data se replikují pomocí systémové replikace HANA.

![Přehled vysoké dostupnosti SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

Použití nastavení systémové replikace SAP HANA vyhrazené virtuální název hostitele a virtuální IP adresy. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení:

* Konfigurace front-endových: IP adresa 10.0.0.13 hn1 db
* Konfigurace back endu: připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí systémové replikace HANA
* Port testu: Port 62503
* Pravidla Vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Na webu Azure Marketplace obsahuje bitovou kopii pro Red Hat Enterprise Linux 7.4 pro SAP HANA, můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Můžete použít některou ze šablon rychlý start, které jsou na Githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti a tak dále.
Pokud chcete nasadit šablonu, postupujte podle těchto kroků:

1. Otevřít [databázové šablony] [ template-multisid-db] na portálu Azure portal.
1. Zadejte následující parametry:
    * **ID systému SAP**: Zadejte ID systému SAP systému SAP, kterou chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazené.
    * **Typ operačního systému**: vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte **RHEL 7**.
    * **Typ databáze**: vyberte **HANA**.
    * **Velikost systému SAP**: Zadejte počet protokoly, které je nový systém přechází k poskytování SAP. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
    * **Dostupnost systému**: vyberte **HA**.
    * **Uživatelské jméno správce, správce hesla nebo klíče SSH klíč**: je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
    * **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá jako **/subscriptions/\<ID předplatného > /resourceGroups/\<název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/\<název virtuální sítě > /subnets/ \<název podsítě >**. Nechte prázdné, pokud chcete vytvořit novou virtuální síť

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvoření skupiny dostupnosti.  
   Nastavte maximální počet aktualizační doména.
1. Vytvořte nástroj pro vyrovnávání zatížení (interní).
   * Vyberte virtuální síť vytvořili v kroku 2.
1. Vytvoření virtuálního počítače 1.  
   Použijte nejméně Red Hat Enterprise Linux 7.4 pro SAP HANA. Tento příklad používá pro SAP HANA image Red Hat Enterprise Linux 7.4 <https://ms.portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74forSAPHANA-ARM> vyberte skupinu vytvořenou v kroku 3 dostupnosti.
1. Vytvoření virtuálního počítače 2.  
   Použijte nejméně Red Hat Enterprise Linux 7.4 pro SAP HANA. Tento příklad používá pro SAP HANA image Red Hat Enterprise Linux 7.4 <https://ms.portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74forSAPHANA-ARM> vyberte skupinu vytvořenou v kroku 3 dostupnosti.
1. Použití datových disků.
1. Konfigurace nástroje pro vyrovnávání zatížení. Nejprve vytvořte front-endový fond IP:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-endový fond IP**a vyberte **přidat**.
   1. Zadejte název nové front-endového fondu IP adres (například **hana front-endu**).
   1. Nastavte **přiřazení** k **statické** a zadejte IP adresu (například **10.0.0.13**).
   1. Vyberte **OK**.
   1. Až se vytvoří nová front-endového fondu IP adres, zapište si IP adresu fondu.

1. Dále vytvořte fond back-end:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **back-endové fondy**a vyberte **přidat**.
   1. Zadejte název nového back endového fondu (například **hana back-endu**).
   1. Vyberte **přidat virtuální počítač**.
   1. Vyberte skupinu dostupnosti, které jsou vytvořené v kroku 3.
   1. Vyberte virtuální počítače clusteru SAP HANA.
   1. Vyberte **OK**.

1. Dále vytvořte sondu stavu:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sond stavu**a vyberte **přidat**.
   1. Zadejte název nového stavu testu (například **hana hp**).
   1. Vyberte **TCP** jako protokol a port 625**03**. Zachovat **Interval** hodnota nastavena na 5 a **prahová hodnota špatného stavu** nastavenou na 2.
   1. Vyberte **OK**.

1. Pro SAP HANA 1.0 vytvořte pravidla Vyrovnávání zatížení:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla Vyrovnávání zatížení**a vyberte **přidat**.
   1. Zadejte název nového pravidla služby load balancer (například hana-lb-3**03**15).
   1. Vyberte IP adresu front-endu, back endového fondu a sondu stavu, který jste vytvořili dříve (například **hana front-endu**).
   1. Zachovat **protokol** nastavena na **TCP**a zadejte port 3**03**15.
   1. Zvětšit **časový limit nečinnosti** až 30 minut.
   1. Ujistěte se, že k **povolte plovoucí IP adresy**.
   1. Vyberte **OK**.
   1. Tento postup opakujte pro port 3**03**17.

1. Pro SAP HANA 2.0 vytvořte pravidla Vyrovnávání zatížení pro systémovou databázi:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla Vyrovnávání zatížení**a vyberte **přidat**.
   1. Zadejte název nového pravidla služby load balancer (například hana-lb-3**03**13).
   1. Vyberte IP adresu front-endu, back endového fondu a sondu stavu, který jste vytvořili dříve (například **hana front-endu**).
   1. Zachovat **protokol** nastavena na **TCP**a zadejte port 3**03**13.
   1. Zvětšit **časový limit nečinnosti** až 30 minut.
   1. Ujistěte se, že k **povolte plovoucí IP adresy**.
   1. Vyberte **OK**.
   1. Tento postup opakujte pro port 3**03**14.

1. Pro SAP HANA 2.0 nejprve vytvořte pravidla Vyrovnávání zatížení pro databáze tenanta:

   1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla Vyrovnávání zatížení**a vyberte **přidat**.
   1. Zadejte název nového pravidla služby load balancer (například hana-lb-3**03**40).
   1. Vyberte IP adresu front-endu, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **hana front-endu**).
   1. Zachovat **protokol** nastavena na **TCP**a zadejte port 3**03**40.
   1. Zvětšit **časový limit nečinnosti** až 30 minut.
   1. Ujistěte se, že k **povolte plovoucí IP adresy**.
   1. Vyberte **OK**.
   1. Tento postup opakujte pro porty 3**03**41 a 3**03**42.

Další informace o požadované porty pro SAP HANA, najdete v kapitole [připojení k databázím Tenanta](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) v [databází Tenantů SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) průvodce nebo [2388694 Poznámka SAP][2388694].

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:

* **[A]** : Krok platí pro všechny uzly.
* **[1]** : Krok platí pro pouze uzlu 1.
* **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

1. **[A]**  Nastavení rozložení disků: **Správce logických svazků (LVM)**.

   Doporučujeme použít LVM pro svazky, které ukládají data a soubory protokolu. V následujícím příkladu se předpokládá, že virtuální počítače mají čtyři datové disky připojené, které se používají k vytváření dva svazky.

   Seznam všech dostupných disků:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Příklad výstupu:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvoření fyzických svazků pro všechny disky, které chcete použít:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvořte skupinu svazku pro data souborů. Jedna skupina svazků použijte pro soubory protokolů a jeden pro sdílený adresář pro SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Vytvoření logických svazků. Lineární svazku se vytvoří při použití `lvcreate` bez `-i` přepnout. Doporučujeme vytvořit prokládané svazku pro lepší výkon vstupně-výstupní operace, kde `-i` argument by měl být číslo podkladový fyzický svazek. V tomto dokumentu se používají dva fyzické svazky pro datový svazek, takže `-i` argument přepínače je nastavena na **2**. Jeden fyzický svazek se používá pro svazek s protokolem, tedy ne `-i` explicitně použít přepínač. Použít `-i` přepnutí a nastavte ho na počet podkladových fyzický svazek při použití více než jeden fyzický svazek pro každý datový protokol nebo sdílených svazků.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Vytvořte připojení adresáře a zkopírujte identifikátor UUID všech logických svazků:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Vytvoření `fstab` položky pro tři logické svazky:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Vložte následující řádek v `/etc/fstab` souboru:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Připojte nový svazky:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Nastavení rozložení disků: **standardní disky**.

   Pro ukázku systémy můžete umístit vaše HANA dat a souborů protokolu na jeden disk. Na /dev/disk/azure/scsi1/lun0 vytvořit oddíl a naformátovat s xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   V souboru /etc/fstab vložte tento řádek:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Vytvořte cílový adresář a připojte disk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele pro všechny hostitele.

   Můžete buď použít DNS server, nebo upravte soubor/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do souboru/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  RHEL pro konfiguraci HANA

   Konfigurace RHEL, jak je popsáno v Poznámka SAP [2292690] a [2455582] a <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Instalace SAP HANA

   Instalace systémové replikace SAP HANA, postupujte podle <https://access.redhat.com/articles/3004101>.

   * Spustit **hdblcm** program z disku DVD HANA. Zadejte následující hodnoty do příkazového řádku:
   * Zvolte typ instalace: Zadejte **1**.
   * Vyberte další součásti k instalaci: Zadejte **1**.
   * Zadejte instalační cesta [/ hana/sdílené]: Vyberte Enter.
   * Zadejte název místního hostitele [hodnota]: Vyberte Enter.
   * Opravdu chcete přidat další hostitele do systému? (Ano/Ne) [n]: Zadejte vyberte.
   * Zadejte ID systému SAP HANA: Zadejte SID HANA, například: **HN1**.
   * Zadejte číslo Instance [00]: Zadejte číslo HANA Instance. Zadejte **03** -li použít šablony Azure nebo následován ručního nasazení části tohoto článku.
   * Vyberte režim databáze / zadejte Index [1]: Zadejte vyberte.
   * Využití systému vyberte / zadejte Index [4]: vyberte hodnotu využití systému.
   * Zadejte umístění datové svazky [/ hana/data/HN1]: Vyberte Enter.
   * Zadejte umístění svazky s protokoly [/ hana/log/HN1]: Vyberte Enter.
   * Omezit maximální přidělení paměti? [n]: Zadejte vyberte.
   * Zadejte název hostitele certifikátu pro hostitele '...' [...]: Zadejte vyberte.
   * Zadejte SAP hostitele agenta uživatele (sapadm) heslo: Zadejte heslo uživatele agenta hostitele.
   * Potvrďte uživatel agenta hostitele systému SAP (sapadm) heslo: Zadejte heslo uživatele agenta hostitele znovu pro potvrzení.
   * Zadejte správce systému (hdbadm) heslo: Zadejte heslo správce systému.
   * Potvrzení správce systému (hdbadm) heslo: Zadejte heslo správce systému znovu pro potvrzení.
   * Správce systému ENTER domácí adresář [/ usr / / sap/HN1 home]: Vyberte Enter.
   * Zadejte prostředí přihlašovací jméno správce systému [/ bin/sh]: Vyberte Enter.
   * Zadejte ID uživatele pro správce systému [1001]: Vyberte Enter.
   * Zadejte ID ze skupiny uživatelů (sapsys) [79]: Vyberte Enter.
   * Zadejte heslo uživatele databáze (systém): Zadejte heslo uživatele databáze.
   * Potvrzení hesla uživatele databáze (systém): Zadejte heslo uživatele databáze znovu pro potvrzení.
   * Restartování systému po restartování počítače? [n]: Zadejte vyberte.
   * Chcete pokračovat? (Ano/Ne): Souhrn ověření. Zadejte **y** pokračujte.

1. **[A]**  Upgrade agenta hostitele SAP.

   Stáhněte si nejnovější Agent hostitele SAP archiv z [centra softwaru SAP] [ sap-swcenter] a spusťte následující příkaz pro upgrade agenta. Cesta k archivu tak, aby odkazoval na soubor, který jste si stáhli nahradíte:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]**  Konfigurace brány firewall

   Vytvoření pravidla brány firewall pro port testu nástroj pro vyrovnávání zatížení Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace 2.0 systémové replikace SAP HANA

Kroky v této části používají následující předpony:

* **[A]** : Krok platí pro všechny uzly.
* **[1]** : Krok platí pro pouze uzlu 1.
* **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

1. **[A]**  Konfigurace brány firewall

   Vytvoření pravidla brány firewall pro povolení provozu systémové replikace HANA a klienta. Požadované porty jsou uvedené na [portů TCP/IP, které všechny produkty SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou uvedené jenom jako příklad pro přenosy databáze SYSTEMDB, HN1 a NW1 2.0 systémové replikace HANA a klienta.

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

1. **[1]**  Vytvoření databáze tenanta.

   Pokud používáte SAP HANA 2.0 nebo MDC, vytvoření databáze tenanta pro váš systém SAP NetWeaver. Nahraďte **NW1** s identifikátorem SID systému SAP.

   Přihlaste se jako \<hanasid > správce a spusťte následující příkaz:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurace systémové replikace na prvním uzlu:

   Přihlaste se jako \<hanasid > adm a zálohování databází:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Zkopírujte soubory systému infrastruktury veřejných KLÍČŮ do sekundární lokality:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Vytvořte primární lokalitě:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurace systémové replikace na druhém uzlu:
    
   Zaregistrujte druhého uzlu do spuštění replikace systému. Přihlaste se jako \<hanasid > správce a spusťte následující příkaz:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]**  Zkontrolovat stav replikace

   Zkontrolovat stav replikace a počkejte, dokud všechny databáze jsou synchronizované. Pokud stav neznámý, zkontrolujte nastavení brány firewall.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace 1.0 systémové replikace SAP HANA

Kroky v této části používají následující předpony:

* **[A]** : Krok platí pro všechny uzly.
* **[1]** : Krok platí pro pouze uzlu 1.
* **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

1. **[A]**  Konfigurace brány firewall

   Vytvoření pravidla brány firewall pro povolení provozu systémové replikace HANA a klienta. Požadované porty jsou uvedené na [portů TCP/IP, které všechny produkty SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou uvedené jenom jako příklad umožňující systémové replikace HANA 2.0. Její přizpůsobení instalace SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]**  Vytvořit požadovaní uživatelé.

   Přihlaste se jako uživatel root a spusťte následující příkaz. Ujistěte se, že k nahrazení řetězců tučné (ID systému HANA **HN1** a číslo instance **03**) s hodnotami instalace SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Vytvořit položka úložiště klíčů.

   Přihlaste se jako uživatel root a spusťte následující příkaz k vytvoření nové položce úložiště klíčů:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Zálohování databáze.

   Přihlaste se jako uživatel root a zálohování databází:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Pokud používáte instalaci s více tenanty, také zálohujte databáze tenanta:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurace systémové replikace na prvním uzlu.

   Přihlaste se jako \<hanasid > adm a vytvořit primární lokality:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurace systémové replikace na sekundárním uzlu.

   Přihlaste se jako \<hanasid > adm a sekundární lokality registrace:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) vytvořit cluster základní Pacemaker pro tento server HANA.

## <a name="create-sap-hana-cluster-resources"></a>Vytvořit prostředky clusteru SAP HANA

Nainstalujte agenty prostředků SAP HANA na **všechny uzly**. Ujistěte se, že chcete povolit úložiště, který obsahuje balíček.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Dále vytvořte topologie HANA. Spuštěním následujících příkazů na jednom uzlu clusteru Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Dále vytvořte prostředky HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

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

## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

Tato část popisuje, jak otestovat vašeho nastavení. Před spuštěním testu, ujistěte se, že Pacemaker nemá žádné neúspěšné akci (prostřednictvím stav počítače), neexistují žádná omezení neočekávané umístění (například zbývajícími testu migrace) a že HANA je stav synchronizace, například pomocí systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Test migrace

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

Hlavní uzel SAP HANA můžete migrovat spuštěním následujícího příkazu:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"`, tento příkaz by měl migrovat hlavní uzel SAP HANA a skupiny, která obsahuje virtuální IP adresu na hn1-db-1.

Po dokončení migrace 'stav počítače sudo' výstup by měl vypadat takto

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Prostředek SAP HANA na hn1-db-0 je zastavená. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migrace vytvoří omezení umístění, které je potřeba znovu odstranit:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitorování stavu prostředků HANA pomocí "stav počítače". Po spuštění HANA na hn1-db-0, výstup by měl vypadat takto

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testovací agent monitorování geografických zón Azure

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

Instalace agenta monitorování geografických zón Azure můžete otestovat tím, že zakážete síťového rozhraní na uzlu, SAP HANA se spuštěným systémem jako hlavní.

<pre><code>[root@hn1-db-1 ~]# sudo ifdown eth0
</code></pre>

Virtuální počítač by měl nyní restartovat nebo zastavit v závislosti na konfiguraci clusteru.
Pokud jste nastavili `stonith-action` nastavení vypnuté, se zastaví virtuální počítač a prostředky se migrují do spuštěného virtuálního počítače.

> [!NOTE]
> Může trvat až 15 minut, než virtuální počítače bude zase online.

Po spuštění virtuálního počítače se SAP HANA prostředků se nepodaří spustit jako sekundární, pokud jste nastavili `AUTOMATED_REGISTER="false"`. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
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

### <a name="test-a-manual-failover"></a>Ruční převzetí služeb při selhání testu

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

Ruční převzetí služeb při selhání můžete otestovat zastavením clusteru na uzlu hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po převzetí služeb při selhání můžete clusteru spustit znovu. Pokud nastavíte `AUTOMATED_REGISTER="false"`, SAP HANA prostředků na uzlu hn1-db-0 se nepodaří spustit jako sekundární. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
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

## <a name="next-steps"></a>Další postup

* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)
