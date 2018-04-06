---
title: Nastavení replikace systému SAP HANA na virtuálních počítačích Azure (VM) | Microsoft Docs
description: Vytvořte vysokou dostupnost SAP HANA na virtuálních počítačích Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: e3fb06309dabd7f66d5873e4c5faa48b468854f6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Vysoká dostupnost SAP HANA na virtuálních počítačích Azure (VM)

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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Na místě, můžete použít buď replikaci HANA systému nebo používat sdílené úložiště, k vytvoření vysoké dostupnosti pro SAP HANA.
Na replikaci systému HANA Azure virtuální počítače v Azure je že jediný podporovaný funkce vysoké dostupnosti dosavadní práce. SAP HANA replikace se skládá z jedné primární uzel a alespoň jeden sekundární uzel. Změny dat na primárním uzlu, který se replikují na sekundární uzel synchronně nebo asynchronně.

Tento článek popisuje postup nasazení virtuálních počítačů, konfiguraci virtuálních počítačů, nainstalujte rozhraní framework clusteru, nainstalovat a nakonfigurovat replikaci systému SAP HANA.
V konfiguraci příklad instalace příkazy čísla instance atd 03 a ID HN1 HANA systému se používá.

Přečtěte si tyto poznámky k SAP a dokumenty Paper nejprve

* Poznámka SAP [1928533], na kterém je:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Kapacita důležité informace o velikosti virtuálního počítače Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure
* Poznámka SAP [2015553] uvádí požadavky pro nasazení softwaru podporovaných SAP SAP v Azure.
* Poznámka SAP [2205917] se doporučuje nastavení operačního systému SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [1944799] má SAP HANA pokyny pro SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [2178632] obsahuje podrobné informace o veškeré monitorování metriky pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o licencích SAP v systému Linux v Azure.
* Poznámka SAP [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* Poznámka SAP [1999351] Další informace o řešení problémů s Azure rozšířené monitorování rozšíření pro SAP.
* [SAP komunity WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP poznámky pro Linux.
* [Azure virtuálních počítačů, plánování a implementace pro SAP v systému Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP v systému Linux (v tomto článku)][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP v systému Linux][dbms-guide]
* [SAP HANA SR výkonu optimalizované scénář] [ suse-hana-ha-guide] průvodci obsahuje všechny požadované informace pro nastavení replikace systému SAP HANA na místě. Tohoto průvodce použijte jako Směrný plán.

## <a name="overview"></a>Přehled

K dosažení vysoké dostupnosti, je nainstalována SAP HANA na dva virtuální počítače. Data se replikují pomocí replikace systému HANA.

![Přehled SAP HANA vysokou dostupnost](./media/sap-hana-high-availability/ha-suse-hana.png)

Instalační program SAP HANA SR používá vyhrazený virtuální název hostitele a virtuální IP adresy. K použití virtuální IP adresy se v Azure, vyžaduje nástroj pro vyrovnávání zatížení. Následující seznam obsahuje konfiguraci služby Vyrovnávání zatížení.

* Front-endovou konfiguraci
  * IP adresa 10.0.0.13 pro hn1-db
* Konfigurace back-end
  * Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí replikaci systému HANA
* Port testu
  * Port 62503
* Pravidla Vyrovnávání zatížení
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Nasazení Linux

Agent prostředků pro SAP HANA je součástí systému SUSE Linux Enterprise Server pro aplikace SAP.
Azure Marketplace obsahuje bitovou kopii pro SUSE Linux Enterprise Server pro 12 aplikace SAP, který můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-template"></a>Nasazení pomocí šablony
Můžete jeden z šablony rychlý start na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti apod. Pokud chcete nasadit šablonu, postupujte takto:

1. Otevřete [databáze šablony] [ template-multisid-db] nebo [konvergované šablony] [ template-converged] na portálu Azure. 
   Zatímco sblížené Šablona také vytváří pravidla Vyrovnávání zatížení ASC nebo SCS a instance YBRAT (pouze Linux), vytvoří šablona databáze pouze pravidla Vyrovnávání zatížení pro databázi. Pokud máte v plánu pro instalaci systému SAP NetWeaver na základě a také chcete nainstalovat instanci ASC nebo SCS stejné počítače, použijte [konvergované šablony][template-converged].
1. Zadejte následující parametry
    1. ID systému SAP  
       Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se bude používat jako předpona pro prostředky, které jsou nasazeny.
    1. Typ zásobníku (platí pouze pokud použijete šablonu sblížené)   
       Vyberte typ SAP NetWeaver zásobníku
    1. Typ operačního systému  
       Vyberte jednu z distribucích systému Linux. V tomto příkladu vyberte SLES 12
    1. Typ databáze  
       Vyberte HANA
    1. Velikost systému SAP  
       Množství nový systém bude poskytovat protokoly SAP. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor
    1. Dostupnost systému  
       Vyberte HA
    1. Uživatelské jméno správce a heslo správce  
       Po vytvoření nového uživatele, který lze použít pro přihlášení k počítači.
    1. Nový nebo existující podsíť  
       Určuje, zda mají být vytvořeny nové virtuální sítě a podsítě nebo by měl použít existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte existující.
    1. ID podsítě  
    ID podsítě, ke které by měl být připojený virtuální počítače. Chcete-li připojit virtuální počítač k síti na pracovišti, vyberte podsíť virtuální sítě VPN nebo Express Route. ID obvykle vypadá /subscriptions/`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvořit skupinu dostupnosti  
   Sada maximální aktualizace domény
1. Vytvořit nástroj pro vyrovnávání zatížení (interní)  
   Vyberte virtuální síť vytvořili za sekundu
1. Vytvoření virtuálního počítače 1  
   Použijte alespoň SLES4SAP 12 SP1 v tomto příkladu použijeme bitovou kopii SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES pro SAP 12 SP2 (Premium)  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Vytvoření virtuálního počítače 2  
   Použijte alespoň SLES4SAP 12 SP1 v tomto příkladu použijeme bitovou kopii SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES pro SAP 12 SP2 (Premium)  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Přidat datových disků
1. Konfigurace pro vyrovnávání zatížení
    1. Vytvořte fond IP front-endu
        1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fond IP front-endu a klikněte na tlačítko Přidat
        1. Zadejte název nového fondu IP front-endu (například hana-front-endu)
        1. Nastavit statickou přiřazení a zadejte IP adresu (například **10.0.0.13**)
        1. Klikněte na tlačítko OK
        1. Po vytvoření nového fondu IP front-endu, zapište si jeho IP adresu
    1. Vytvořte fond back-end
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte back-endové fondy a klikněte na tlačítko Přidat
        1. Zadejte název nového fondu back-end (například hana-back-end)
        1. Klikněte na tlačítko Přidat virtuální počítač
        1. Vyberte jste dříve vytvořili sadu dostupnosti
        1. Vyberte virtuální počítače clusteru SAP HANA
        1. Klikněte na tlačítko OK
    1. Vytvoření sondy stavu
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte sondy stavu služby a klikněte na tlačítko Přidat
        1. Zadejte název nové kontroly stavu (například hana-hp)
        1. Vyberte TCP jako protokol, port 625**03**, zachovat Interval 5 a prahová hodnota špatného stavu 2
        1. Klikněte na tlačítko OK
    1. SAP HANA 1.0: Vytvoření pravidla Vyrovnávání zatížení
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
        1. Zadejte název nové pravidlo Vyrovnávání zatížení (například hana-lb-3**03**15)
        1. Vyberte front-endovou IP adresu, fond back-end a test stavu, který jste vytvořili dříve (pro front-endu – příklad hana)
        1. Zachovat protokol TCP, zadejte port 3**03**15
        1. Časový limit nečinnosti zvýšení do 30 minut
        1. **Nezapomeňte povolit plovoucí IP adresa**
        1. Klikněte na tlačítko OK
        1. Opakujte předchozí kroky pro port 3**03**17
    1. SAP HANA 2.0: Vytvoření pravidel pro systémovou databázi Vyrovnávání zatížení
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
        1. Zadejte název nové pravidlo Vyrovnávání zatížení (například hana-lb-3**03**13)
        1. Vyberte front-endovou IP adresu, fond back-end a test stavu, který jste vytvořili dříve (pro front-endu – příklad hana)
        1. Zachovat protokol TCP, zadejte port 3**03**13
        1. Časový limit nečinnosti zvýšení do 30 minut
        1. **Nezapomeňte povolit plovoucí IP adresa**
        1. Klikněte na tlačítko OK
        1. Opakujte předchozí kroky pro port 3**03**14
    1. SAP HANA 2.0: Vytvoření pravidel pro první Vyrovnávání zatížení databáze klienta
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
        1. Zadejte název nové pravidlo Vyrovnávání zatížení (například hana-lb-3**03**40)
        1. Vyberte front-endovou IP adresu, fond back-end a test stavu, který jste vytvořili dříve (pro front-endu – příklad hana)
        1. Zachovat protokol TCP, zadejte port 3**03**40
        1. Časový limit nečinnosti zvýšení do 30 minut
        1. **Nezapomeňte povolit plovoucí IP adresa**
        1. Klikněte na tlačítko OK
        1. Opakujte předchozí kroky pro port 3**03**41 a 3**03**42

Další informace o požadované porty pro SAP HANA, najdete v kapitole [připojení k databázím klienta](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) z [SAP HANA klienta databází](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) průvodce nebo [2388694 Poznámka SAP] [2388694].


## <a name="create-pacemaker-cluster"></a>Vytvoření clusteru kardiostimulátor

Postupujte podle kroků v [nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) vytvořit cluster základní kardiostimulátor pro tento server HANA. Můžete taky stejného clusteru kardiostimulátor pro SAP HANA a SAP NetWeaver (A) SCS.

## <a name="installing-sap-hana"></a>Instalace SAP HANA

Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2 kardiostimulátor clusteru.

1. **[A]**  Rozložení disku instalační program
    1. LVM  
       
       Obecně doporučujeme používat LVM pro svazky, které ukládají data a soubory protokolu. Následující příklad předpokládá, že máte virtuální počítače čtyři datových disků připojených, které se mají použít k vytvoření dva svazky.

       Zobrazí seznam všech dostupných disků
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Příklad výstupu
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Vytvořte fyzických svazků pro všechny disky, které chcete použít.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Vytvoření skupiny svazku pro datové soubory, jedna skupina svazku pro soubory protokolů a jeden pro do sdíleného adresáře SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       Vytvoření logické svazky

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Vytvořte připojení adresáře a zkopírujte identifikátor UUID všechny logické svazky
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Vytvoření položky fstab pro tři logické svazky
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Vložení tohoto řádku /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Připojit nové svazky
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Nešifrovaná disky  
       Pro systémy ukázku můžete umístit HANA soubory protokolu a data na jeden disk. Následující příkazy na /dev/disk/azure/scsi1/lun0 vytvořit oddíl a naformátovat s xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Vložení tohoto řádku /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Vytvořte cílový adresář a připojení disku.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele pro všechny hostitele  
    Můžete buď použít DNS server, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak chcete použít soubor/etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech
    ```bash
    sudo vi /etc/hosts
    ```
    Vložte následující řádky, které se/etc/hosts. Změnit IP adresu a název hostitele tak, aby odpovídaly prostředí    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Nainstalovat HANA HA balíčky  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

K instalaci replikaci systému SAP HANA, postupujte podle kapitoly 4 příručky pro SAP HANA SR výkonu optimalizované scénář v https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/

1. **[A]**  Hdblcm spustit z disku DVD HANA
    * Zvolte instalace-1 >
    * Vyberte další součásti k instalaci -> 1
    * Zadejte instalační cestu [/ hana/sdílené]: -> zadejte
    * Zadejte název místního hostitele [.]: -> zadejte
    * Opravdu chcete přidat další hostitele do systému? (y/n) [n]: -> ENTER
    * Zadejte ID systému SAP HANA: <SID of HANA e.g. HN1>
    * Zadejte čísla Instance [00]:   
  Čísla HANA Instance. Pokud používá šablony Azure nebo Ruční nasazení a potom použít 03
    * Vyberte režim databáze / zadejte Index [1]: -> zadejte
    * Vyberte použití systému / zadejte Index [4]:  
  Vyberte systém využití
    * Zadejte umístění datových svazků [/ hana/data/HN1]: -> zadejte
    * Zadejte umístění protokolu svazků [/ hana/log/HN1]: -> zadejte
    * Omezení přidělení paměti maximální? [n]: -> ENTER
    * Zadejte název hostitele certifikát pro hostitele,..." [...]: -> Zadejte
    * Zadejte SAP hostitele agenta uživatele (sapadm) heslo:
    * Potvrďte SAP hostitele agenta uživatele (sapadm) heslo:
    * Zadejte správce systému (hdbadm) heslo:
    * Potvrzení správce systému (hdbadm) heslo:
    * Zadejte domovský adresář správce systému [/ usr/sap nebo HN1/domovské]: -> zadejte
    * Zadejte prostředí přihlášení správce systému [/ bin/dílet]: -> zadejte
    * Zadejte ID uživatele správce systému [1001]: -> zadejte
    * Zadejte ID ze skupiny uživatelů (sapsys) [79]: -> zadejte
    * Zadejte heslo k databázi uživatelů (systém):
    * Potvrďte heslo k databázi uživatelů (systém):
    * Restartování systému po restartování počítače? [n]: -> ENTER
    * Chcete pokračovat? (Ano/Ne):   
  Ověřit, souhrn a zadejte y můžete pokračovat

1. **[A]**  Upgradu agenta hostitele SAP  
  Stáhněte si nejnovější archivu SAP Agent hostitele z [SAP Softwarecenter] [ sap-swcenter] a spusťte následující příkaz k aktualizaci agenta. Nahraďte cestu do archivu tak, aby odkazoval na soubor, který jste stáhli.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace replikace 2.0 systému SAP HANA

Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2 kardiostimulátor clusteru.

1. **[1]**  Vytvoření databáze klienta

   Pokud používáte SAP HANA 2.0 nebo MDC, vytvořte databázi klienta pro systém SAP NetWeaver. Nahraďte NW1 SID systému SAP.

   Přihlaste se jako `<hanasid`> adm a spusťte následující příkaz

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurace replikace systému na prvním uzlu
   
   Přihlaste se jako `<hanasid`> adm a zálohování databáze

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Zkopírujte soubory infrastruktury veřejných KLÍČŮ systému sekundární

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Vytvořte primární lokality.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurace replikace systému na druhém uzlu
    
    Zaregistrujte druhého uzlu pro spuštění systému replikace. Přihlaste se jako `<hanasid`> adm a spusťte následující příkaz

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace replikace 1.0 systému SAP HANA

1. **[1]**  Vytvoření požadovaných uživatelů

    Přihlaste se jako kořenového adresáře a spusťte následující příkaz. Nezapomeňte nahradit tučné řetězce (HANA systému ID HN1 a číslo instance 03) s hodnotami instalace SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]**  Vytvořit položku úložiště klíčů
   
    Přihlaste se jako kořenový a spusťte následující příkaz pro vytvoření nové položky úložiště klíčů.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]**  Zálohování databáze

   Přihlaste se jako kořenový a zálohování databáze

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Pokud používáte instalaci s více klienty, také zálohujte databázi klienta

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurace replikace systému na prvním uzlu
    
    Přihlaste se jako `<hanasid`> adm a vytvořit primární lokality.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Konfigurace replikace systému na sekundární uzel.

    Přihlaste se jako `<hanasid`> adm a zaregistrujte sekundární lokality.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Vytvořit prostředky clusteru SAP HANA

   Nejprve vytvořte topologie HANA. Spusťte následující příkazy v jednom z uzlů clusteru kardiostimulátor.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Dále vytvořte HANA prostředky.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Ujistěte se, zda je stav clusteru ok a zda jsou spuštěny všechny prostředky. Není důležité, na který uzel prostředky jsou spuštěné.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Nastavení clusteru s podporou testu
Tato kapitola popisuje, jak můžete otestovat vašeho nastavení. Každý test předpokládá, že jsou kořenové a hlavním serveru SAP HANA běží na virtuálním počítači hn1-db-0.

#### <a name="fencing-test"></a>Vymezení testu

Instalační program agenta vymezení můžete otestovat zakázáním síťové rozhraní na uzlu hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

Virtuální počítač by měl nyní restartovat, nebo byla zastavena v závislosti na konfiguraci clusteru.
Pokud jste nastavili stonith akce, která bude vypnuto, virtuální počítač bude zastavena a prostředky se migrují do spuštěného virtuálního počítače.

Po spuštění virtuálního počítače, prostředků SAP HANA nepodaří spustit jako sekundární Pokud nastavíte AUTOMATED_REGISTER = "false". V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testování ruční převzetí služeb při selhání

Ruční převzetí služeb při selhání můžete otestovat zastavování služby kardiostimulátor na uzlu hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Po převzetí služeb při selhání můžete spustit službu znovu. Pokud nastavíte AUTOMATED_REGISTER = "false", SAP HANA prostředku hn1-db-0 nepodaří spustit jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Testování migrace

Spuštěním následujícího příkazu můžete migrovat hlavní uzel SAP HANA
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Pokud nastavíte AUTOMATED_REGISTER = "false", tato sekvence příkazů by migrovat SAP HANA hlavní uzel a skupiny, která obsahuje virtuální IP adresu, kterou hn1-db-1.
SAP HANA prostředku hn1-db-0 se nepodaří spustit jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Migrace vytvoří omezení umístění, které je potřeba znovu odstranit.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Musíte také vyčištění stav prostředku sekundárního uzlu

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Další postup
* [Azure virtuálních počítačů, plánování a implementace pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md). 
