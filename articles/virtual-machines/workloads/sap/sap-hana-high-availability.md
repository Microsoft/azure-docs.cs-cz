---
title: Vysoká dostupnost SAP HANA na virtuálních počítačích Azure v SLES | Microsoft Docs
description: Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2020
ms.author: radeltch
ms.openlocfilehash: 1efa00962e63274c2cc02c8758725e5b11d70a9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567822"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server

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
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Pro místní vývoj můžete k zajištění vysoké dostupnosti pro SAP HANA použít buď replikaci systému HANA, nebo použít sdílené úložiště.
Na virtuálních počítačích Azure je replikace systému HANA v Azure aktuálně jedinou podporovanou funkcí vysoké dostupnosti. Replikace SAP HANA se skládá z jednoho primárního uzlu a alespoň jednoho sekundárního uzlu. Změny dat v primárním uzlu jsou replikovány do sekundárního uzlu synchronně nebo asynchronně.

Tento článek popisuje nasazení a konfiguraci virtuálních počítačů, instalaci architektury clusterů a instalaci a konfiguraci replikace systému SAP HANA.
V ukázkových konfiguracích se používají instalační příkazy, číslo instance **03**a ID systému Hana **HN1** .

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* Poznámka [2015553] SAP uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro SUSE Linux Enterprise Server pro aplikace SAP bylo doporučeno nastavení operačního systému SAP Poznámka [2205917] .
* Poznámka SAP Poznámka [1944799] obsahuje pokyny pro SAP HANA SUSE Linux Enterprise Server pro aplikace SAP.
* V části SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování, které jsou hlášeny pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* V části SAP Note [401162] najdete informace o tom, jak se při nastavování replikace systému Hana vyhnout adrese, která se už používá.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* Průvodce [plánováním a implementací Azure Virtual Machines pro SAP v systému Linux][planning-guide] .
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide] (Tento článek).
* Průvodce [nasazením Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide] .
* [Příručky k osvědčeným postupům SUSE Linux Enterprise Server pro SAP Applications 12 SP3][sles-for-sap-bp]
  * Nastavení SAP HANA infrastruktury optimalizované pro výkon SR (SLES for SAP Applications 12 SP1). Průvodce obsahuje všechny požadované informace pro nastavení SAP HANA systémové replikace pro místní vývoj. Tento průvodce použijte jako základ.
  * Nastavení SAP HANA infrastruktury optimalizované pro náklady na SR (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Přehled

Aby se dosáhlo vysoké dostupnosti, SAP HANA je nainstalovaná na dvou virtuálních počítačích. Data se replikují pomocí replikace systému HANA.

![Přehled SAP HANA vysoké dostupnosti](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA instalace replikace systému používá vyhrazený virtuální hostitelský název a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení:

* Konfigurace front-endu: IP adresa 10.0.0.13 pro HN1-DB
* Konfigurace back-endu: připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí replikace systému HANA
* Port testu paměti: port 62503
* Pravidla vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Agent prostředků pro SAP HANA je zahrnutý v SUSE Linux Enterprise Server pro aplikace SAP.
Azure Marketplace obsahuje obrázek pro SUSE Linux Enterprise Server aplikace SAP 12, které můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení s využitím šablony

K nasazení všech požadovaných prostředků můžete použít jednu z šablon pro rychlý Start, které jsou na GitHubu. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti a tak dále.
K nasazení šablony použijte následující postup:

1. Otevřete [šablonu databáze][template-multisid-db] nebo [sblíženou šablonu][template-converged] na Azure Portal. 
    Šablona databáze vytvoří pravidla vyrovnávání zatížení pouze pro databázi. Sblížená šablona také vytvoří pravidla vyrovnávání zatížení pro instanci ASCS/SCS a OLAJÍCÍCH (pouze Linux). Pokud máte v plánu nainstalovat systém založený na SAP NetWeaver a chcete nainstalovat instanci ASCS/SCS do stejného počítače, použijte [sblíženou šablonu][template-converged].

1. Zadejte následující parametry:
    - **ID systému SAP**: Zadejte ID systému SAP pro systém SAP, který chcete nainstalovat. ID se používá jako předpona pro nasazené prostředky.
    - **Typ zásobníku**: (Tento parametr lze použít pouze v případě, že použijete sblíženou šablonu.) Vyberte typ zásobníku SAP NetWeaver.
    - **Typ operačního systému**: vyberte jednu z distribucí systému Linux. V tomto příkladu vyberte **SLES 12**.
    - **Typ databáze**: vyberte **Hana**.
    - **Velikost systému SAP**: zadejte počet SAP, který bude nový systém poskytovat. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
    - **Dostupnost systému**: vyberte **ha**.
    - **Uživatelské jméno správce a heslo správce**: vytvoří se nový uživatel, který se dá použít k přihlášení k počítači.
    - **Nová nebo existující podsíť**: Určuje, jestli se má vytvořit nová virtuální síť a podsíť, nebo jestli se používá existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
    - **ID podsítě**: Pokud chcete nasadit virtuální počítač do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name> **.

### <a name="manual-deployment"></a>Ruční nasazení

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je SAP Certified for SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačních systémů pro tyto typy můžete vyhledat v [SAP HANA certifikovaných IaaS platformách](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ujistěte se, že kliknete na podrobnosti o typu virtuálního počítače a získáte úplný seznam SAP HANA podporovaných vydání operačních systémů pro konkrétní typ virtuálního počítače.
>  

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvořte skupinu dostupnosti.
   - Nastavte maximální doménu aktualizace.
1. Vytvořte Nástroj pro vyrovnávání zatížení (interní). Doporučujeme [standardní nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md).
   - Vyberte virtuální síť vytvořenou v kroku 2.
1. Vytvořte virtuální počítač 1.
   - Použijte SLES4SAP image v galerii Azure, která je podporovaná pro SAP HANA pro vybraný typ virtuálního počítače.
   - Vyberte skupinu dostupnosti vytvořenou v kroku 3.
1. Vytvořte virtuální počítač 2.
   - Použijte SLES4SAP image v galerii Azure, která je podporovaná pro SAP HANA pro vybraný typ virtuálního počítače.
   - Vyberte skupinu dostupnosti vytvořenou v kroku 3. 
1. Přidejte datové disky.
1. Pokud používáte standardní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
   1. Nejprve vytvořte front-end fond IP adres:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond**a vyberte **Přidat**.
      1. Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.0.0.13**).
      1. Vyberte **OK**.
      1. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.
   
   1. Dále vytvořte fond back-end:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu**a vyberte **Přidat**.
      1. Zadejte název nového fondu back-end (například **Hana-back-end**).
      1. Vyberte **Virtual Network**.
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte * * virtuální počítač * *.
      1. Vyberte virtuální počítače SAP HANA clusteru a jejich IP adresy.
      1. Vyberte **Přidat**.
   
   1. Potom vytvořte sondu stavu:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **Hana-HP**).
      1. Jako protokol a port 625**03**vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
      1. Vyberte **OK**.
   
   1. Dále vytvořte pravidla vyrovnávání zatížení:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **Hana-kg**).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**, **Hana-back-endu** a **Hana-HP**).
      1. Vyberte **porty ha**.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.

   > [!Note]
   > Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

1. Případně, pokud váš scénář používá základní nástroj pro vyrovnávání zatížení, postupujte podle těchto kroků konfigurace:
   1. Nejprve vytvořte front-end fond IP adres:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond**a vyberte **Přidat**.
      1. Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.0.0.13**).
      1. Vyberte **OK**.
      1. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.
   
   1. Dále vytvořte fond back-end:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu**a vyberte **Přidat**.
      1. Zadejte název nového fondu back-end (například **Hana-back-end**).
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte skupinu dostupnosti vytvořenou v kroku 3.
      1. Vyberte virtuální počítače clusteru SAP HANA.
      1. Vyberte **OK**.
   
   1. Potom vytvořte sondu stavu:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu**a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **Hana-HP**).
      1. Jako protokol a port 625**03**vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
      1. Vyberte **OK**.
   
   1. Pro SAP HANA 1,0 vytvořte pravidla vyrovnávání zatížení:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**15).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
      1. Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**15.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tento postup pro port 3**03**17.
   
   1. Pro SAP HANA 2,0 vytvořte pravidla vyrovnávání zatížení pro systémovou databázi:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**13).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**).
      1. Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**13.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tento postup pro port 3**03**14.
   
   1. Pro SAP HANA 2,0 nejprve vytvořte pravidla vyrovnávání zatížení pro databázi tenanta:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení**a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například Hana-kg-3**03**40).
      1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **Hana-Endu**).
      1. Zachovejte **protokol** nastaven na **TCP**a zadejte port 3**03**40.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.
      1. Opakujte tyto kroky pro porty 3**03**41 a 3**03**42.

   Další informace o požadovaných portech pro SAP HANA naleznete v kapitole [připojení k databázím tenantů](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) v průvodci [SAP HANA databáze klienta](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) nebo v tématu [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Viz také SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server Hana základní cluster Pacemaker. Stejný cluster Pacemaker můžete použít pro SAP HANA a SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:
- **[A]**: krok platí pro všechny uzly.
- **[1]**: krok platí pouze pro uzel 1.
- **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

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

1. **[A]** nainstalujte SAP HANA balíčky s vysokou dostupností:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Pokud chcete nainstalovat SAP HANA systémovou replikaci, postupujte podle kapitoly 4 [příručky SAP HANA optimalizovaného scénáře pro výkon SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** spusťte program **hdblcm** z disku DVD Hana. Na příkazovém řádku zadejte následující hodnoty:
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

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace replikace systému SAP HANA 2,0

Kroky v této části používají následující předpony:

* **[A]**: krok platí pro všechny uzly.
* **[1]**: krok platí pouze pro uzel 1.
* **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

1. **[1]** vytvořte databázi tenanta.

   Pokud používáte SAP HANA 2,0 nebo MDC, vytvořte databázi tenanta pro systém SAP NetWeaver. Nahraďte **NW1** identifikátorem SID vašeho systému SAP.

   Jako <hanasid ADM spusťte následující příkaz \> :

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace replikace systému SAP HANA 1,0

Kroky v této části používají následující předpony:

* **[A]**: krok platí pro všechny uzly.
* **[1]**: krok platí pouze pro uzel 1.
* **[2]**: Tento krok platí jenom pro uzel 2 clusteru Pacemaker.

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

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

Nejdřív vytvořte topologii HANA. Na jednom z uzlů clusteru Pacemaker spusťte následující příkazy:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Pak vytvořte prostředky HANA:

> [!IMPORTANT]
> Nedávné testování odhalilo situace, kde NetCat přestane reagovat na požadavky z důvodu nevyřízených položek a omezení zpracování pouze jednoho připojení. Prostředek NetCat přestane naslouchat požadavkům nástroje pro vyrovnávání zatížení Azure a plovoucí IP adresa přestane být k dispozici.  
> Pro existující clustery Pacemaker doporučujeme v minulosti nahradit NetCat pomocí Socat. V současné době doporučujeme použít agenta prostředků Azure-, který je součástí prostředků balíčku – agenti s následujícími požadavky na verzi balíčku:
> - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
> - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
>
> Všimněte si, že tato změna bude vyžadovat krátké výpadky.  
> U existujících clusterů Pacemaker se v případě, že konfigurace již změnila tak, aby používala socat, jak je popsáno v tématu [posílení zabezpečení azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), neexistuje žádný požadavek na přepnutí přímo do agenta prostředků Azure-No.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

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
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky. Není důležité, na kterém uzlu jsou prostředky spuštěné.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Tato část popisuje, jak můžete otestovat instalaci. Každý test předpokládá, že jste kořen a že je na virtuálním počítači **HN1-DB-0** spuštěná předloha SAP HANA.

### <a name="test-the-migration"></a>Testování migrace

Před zahájením testu se ujistěte, že Pacemaker nemá žádnou neúspěšnou akci (přes crm_mon-r), že neexistují žádná neočekávaná omezení umístění (například Leftovers testu migrace) a že HANA je stav synchronizace, například s SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

SAP HANA hlavní uzel můžete migrovat spuštěním následujícího příkazu:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"` , tato sekvence příkazů by měla migrovat SAP HANA hlavní uzel a skupinu obsahující virtuální IP adresu na HN1-DB-1.

Po dokončení migrace bude výstup crm_mon-r vypadat takto:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Prostředek SAP HANA na HN1-DB-0 se nepovede spustit jako sekundární. V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migrace vytvoří omezení umístění, která je potřeba odstranit znovu:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Také je nutné vyčistit stav prostředku sekundárního uzlu:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorujte stav prostředku HANA pomocí crm_mon-r. Jakmile se HANA spustí na HN1-DB-0, výstup by měl vypadat nějak takto:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testování agenta pro oplocení Azure (ne SBD)

Instalaci agenta pro oplocení Azure můžete otestovat zakázáním síťového rozhraní na uzlu HN1-DB-0:

<pre><code>sudo ifdown eth0
</code></pre>

V závislosti na konfiguraci clusteru by se teď měl virtuální počítač restartovat nebo zastavit.
Pokud nastavíte `stonith-action` nastavení na vypnuto, virtuální počítač se zastaví a prostředky se migrují na běžící virtuální počítač.

Po opětovném spuštění virtuálního počítače se prostředek SAP HANA nepovede spustit jako sekundární, pokud jste nastavili `AUTOMATED_REGISTER="false"` . V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testování SBDch zón

Instalaci SBD můžete otestovat ukončením procesu Inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Uzel clusteru HN1-DB-0 by měl být restartován. Služba Pacemaker možná nebude možné začít později. Ujistěte se, že ho znovu spustíte.

### <a name="test-a-manual-failover"></a>Test ručního převzetí služeb při selhání

Ruční převzetí služeb při selhání můžete otestovat zastavením `pacemaker` služby na uzlu HN1-DB-0:

<pre><code>service pacemaker stop
</code></pre>

Po převzetí služeb při selhání můžete službu spustit znovu. Pokud nastavíte `AUTOMATED_REGISTER="false"` , SAP HANA prostředku na uzlu HN1-DB-0 se nepovede spustit jako sekundární. V takovém případě Nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE testy

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je SAP Certified for SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačních systémů pro tyto typy můžete vyhledat v [SAP HANA certifikovaných IaaS platformách](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ujistěte se, že kliknete na podrobnosti o typu virtuálního počítače a získáte úplný seznam SAP HANA podporovaných vydání operačních systémů pro konkrétní typ virtuálního počítače.

Spusťte všechny testovací případy, které jsou uvedené ve scénáři SAP HANA optimalizovaného výkonu SR nebo průvodce scénářem SAP HANA SR pro optimalizaci v závislosti na vašem případu použití. Příručky najdete na [stránce s osvědčenými postupy pro SLES for SAP][sles-for-sap-bp].

Následující testy jsou kopie popisů testů SAP HANA scénář s optimalizovaným výkonem SR SUSE Linux Enterprise Server v průvodci pro SAP Applications 12 SP1. V případě aktuálnosti verze si vždy přečtěte také příručku samotné. Před spuštěním testu vždy zajistěte, aby byl HANA synchronizovaný, a ujistěte se také, že konfigurace Pacemaker je správná.

V následujících popisech testů jsme předpokládali PREFER_SITE_TAKEOVER = "true" a AUTOMATED_REGISTER = "false".
Poznámka: následující testy jsou navrženy tak, aby byly spouštěny v pořadí a závisí na stavu ukončení předchozích testů.

1. TEST 1: ZASTAVENÍ PRIMÁRNÍ DATABÁZE V UZLU 1

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker by měl detekovat zastavenou instanci HANA a převzetí služeb při selhání do jiného uzlu. Po převzetí služeb při selhání se instance HANA na uzlu HN1-DB-0 zastaví, protože Pacemaker automaticky neregistruje uzel jako sekundární databáze HANA.

   Spusťte následující příkazy pro registraci uzlu HN1-DB-0 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZASTAVENÍ PRIMÁRNÍ DATABÁZE V UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker by měl detekovat zastavenou instanci HANA a převzetí služeb při selhání do jiného uzlu. Až se převzetí služeb při selhání provede, instance HANA na uzlu HN1-DB-1 se zastaví, protože Pacemaker neregistruje uzel automaticky jako sekundární službu HANA.

   Spusťte následující příkazy pro registraci uzlu HN1-DB-1 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: PRIMÁRNÍ DATABÁZE SELHÁNÍ NA UZLU

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker by měl detekovat ukončenou instanci HANA a převzetí služeb při selhání do jiného uzlu. Po převzetí služeb při selhání se instance HANA na uzlu HN1-DB-0 zastaví, protože Pacemaker automaticky neregistruje uzel jako sekundární databáze HANA.

   Spusťte následující příkazy pro registraci uzlu HN1-DB-0 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: PRIMÁRNÍ DATABÁZE SELHÁNÍ V UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker by měl detekovat ukončenou instanci HANA a převzetí služeb při selhání do jiného uzlu. Až se převzetí služeb při selhání provede, instance HANA na uzlu HN1-DB-1 se zastaví, protože Pacemaker neregistruje uzel automaticky jako sekundární službu HANA.

   Spusťte následující příkazy pro registraci uzlu HN1-DB-1 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: UZEL PRIMÁRNÍ LOKALITY SELHÁNÍ (UZEL 1)

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu HN1-DB-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl detekovat uzel ukončeného clusteru a jeho plot. Jakmile je uzel v ohrazení, Pacemaker spustí převzetí instance HANA. Po restartování uzlu s ochranou se Pacemaker nespustí automaticky.

   Spuštěním následujících příkazů spusťte Pacemaker, vyčistěte zprávy SBD pro Node HN1-DB-0, registrujte uzel HN1-DB-0 jako sekundární a vyčistěte prostředek, který se nezdařil.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: UZEL SEKUNDÁRNÍ LOKALITY HAVÁRIE (UZEL 2)

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu HN1-DB-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl detekovat uzel ukončeného clusteru a jeho plot. Jakmile je uzel v ohrazení, Pacemaker spustí převzetí instance HANA. Po restartování uzlu s ochranou se Pacemaker nespustí automaticky.

   Spuštěním následujících příkazů spusťte Pacemaker, vyčistěte zprávy SBD pro Node HN1-DB-1, registrujte uzel HN1-DB-1 jako sekundární a vyčistěte prostředek, který se nezdařil.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: ZASTAVENÍ SEKUNDÁRNÍ DATABÁZE V UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker vyhledá zastavenou instanci HANA a označí prostředek jako neúspěšný na uzlu HN1-DB-1. Pacemaker by měl automaticky restartovat instanci HANA. Spuštěním následujícího příkazu vyčistěte stav selhání.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: ZHROUCENÍ SEKUNDÁRNÍ DATABÁZE V UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy, které <hanasid \> ADM v Node HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker vyhledá ukončenou instanci HANA a označí prostředek jako neúspěšný na uzlu HN1-DB-1. Spuštěním následujícího příkazu vyčistěte stav selhání. Pacemaker by pak měl automaticky restartovat instanci HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: UZEL SEKUNDÁRNÍ LOKALITY HAVÁRIE (UZEL 2) SE SPUŠTĚNOU SEKUNDÁRNÍ DATABÁZÍ HANA

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu HN1-DB-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl detekovat uzel ukončeného clusteru a jeho plot. Po restartování uzlu s ochranou se Pacemaker nespustí automaticky.

   Spuštěním následujících příkazů spusťte Pacemaker, vyčistěte zprávy SBD pro Node HN1-DB-1 a vyčistěte prostředek, který se nezdařil.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav prostředku po testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
