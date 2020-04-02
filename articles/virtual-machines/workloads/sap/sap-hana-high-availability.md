---
title: Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SLES | Dokumenty společnosti Microsoft
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
ms.date: 03/31/2020
ms.author: radeltch
ms.openlocfilehash: 215cfd033a3fe8eb0ad9896c1f45f1e0f788823f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521365"
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

Pro místní vývoj můžete použít replikaci systému HANA nebo použít sdílené úložiště k vytvoření vysoké dostupnosti pro SAP HANA.
Ve virtuálních počítačích Azure (VM) je replikace systému HANA v Azure aktuálně jedinou podporovanou funkcí vysoké dostupnosti. SAP HANA Replication se skládá z jednoho primárního uzlu a alespoň jednoho sekundárního uzlu. Změny dat v primárním uzlu jsou replikovány do sekundárního uzlu synchronně nebo asynchronně.

Tento článek popisuje, jak nasadit a nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat a nakonfigurovat replikaci systému SAP HANA.
V ukázkových konfiguracích se používají instalační příkazy, číslo instance **03**a ID systému HANA **HN1.**

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP.
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure.
  * Podporovaný software SAP a operační systém (OS) a databázové kombinace.
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure.
* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované SAP.
* SAP Note [2205917] doporučil nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP.
* SAP Note [1944799] má SAP HANA guidelines for SUSE Linux Enterprise Server for SAP Applications.
* SAP Note [2178632] má podrobné informace o všech metrikmonitorování, které jsou hlášeny pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* SAP Poznámka [401162] obsahuje informace o tom, jak se vyhnout "adresa již používá" při nastavování hana systémové replikace.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu.][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide] (tento článek).
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide] průvodce.
* [Příručky osvědčených postupů SUSE Linux Enterprise Server pro SAP Aplikace 12 SP3][sles-for-sap-bp]
  * Nastavení infrastruktury optimalizované pro výkon SAP HANA SR (SLES pro aplikace SAP 12 SP1). Průvodce obsahuje všechny požadované informace pro nastavení sap hana systémové replikace pro místní vývoj. Tuto příručku použijte jako směrný plán.
  * Nastavení infrastruktury optimalizované pro náklady SAP HANA SR (SLES pro aplikace SAP 12 SP1)

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti sap HANA je nainstalován na dvou virtuálních počítačích. Data jsou replikována pomocí replikace systému HANA.

![SAP HANA přehled vysoké dostupnosti](./media/sap-hana-high-availability/ha-suse-hana.png)

Nastavení systémové replikace SAP HANA používá vyhrazený virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení:

* Front-end konfigurace: IP adresa 10.0.0.13 pro hn1-db
* Back-endkonfigurace: Připojení k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí replikace systému HANA
* Port sondy: Port 62503
* Pravidla vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Agent prostředků pro SAP HANA je součástí SUSE Linux Enterprise Server pro aplikace SAP.
Azure Marketplace obsahuje image pro SUSE Linux Enterprise Server pro SAP Aplikace 12, které můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Můžete použít jednu ze šablon rychlého startu, které jsou na GitHubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, nástroj pro vyrovnávání zatížení, sadu dostupnosti a tak dále.
Chcete-li šablonu nasadit, postupujte takto:

1. Otevřete [šablonu databáze][template-multisid-db] nebo [konvergotelou šablonu][template-converged] na webu Azure Portal. 
    Šablona databáze vytvoří pravidla vyrovnávání zatížení pouze pro databázi. Konvergovaná šablona také vytvoří pravidla vyrovnávání zatížení pro instanci ASCS/SCS a ERS (pouze linux). Pokud plánujete nainstalovat systém založený na sap netweaveru a chcete nainstalovat instanci ASCS/SCS na stejných počítačích, použijte [konvergovní šablonu][template-converged].

1. Zadejte následující parametry:
    - **SAP System ID**: Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazeny.
    - **Typ zásobníku**: (Tento parametr je použitelný pouze v případě, že použijete konvergované šablony.) Vyberte typ zásobníku SAP NetWeaver.
    - **Typ operačního systému**: Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte **SLES 12**.
    - **Typ db**: Vyberte **možnost HANA**.
    - **Velikost systému SAP**: Zadejte počet SAPS, který nový systém poskytne. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora.
    - **Dostupnost systému**: Vyberte **možnost HA**.
    - **Admin Uživatelské jméno a heslo správce:** Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
    - **Nová nebo existující podsíť**: Určuje, zda má být vytvořena nová virtuální síť a podsíť nebo zda má být použita existující podsíť. Pokud už máte virtuální síť, která je připojená k vaší místní síti, vyberte **Existující**.
    - **ID podsítě**: Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k které by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako **/subscriptions/\<subscription ID\<>/resourceGroups/ název skupiny prostředků\<>/providers/Microsoft.Network/virtualNetworks/ název virtuální sítě>/podsítě/\<název podsítě>**.

### <a name="manual-deployment"></a>Ruční nasazení

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je sap certifikovaný pro SAP HANA na konkrétní typy virtuálních počítačů, které používáte. Seznam typů virtuálních počítačů certifikovaných SAP HANA a verzí operačního systému pro tyto typy lze vyhledat v [platformách IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Nezapomeňte kliknout na podrobnosti o typu virtuálního uživatele uvedené maže získat úplný seznam SAP HANA podporované verze operačního systému pro konkrétní typ virtuálního uživatele
>  

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvořte sadu dostupnosti.
   - Nastavte doménu s maximální aktualizací.
1. Vytvořte systém pro vyrovnávání zatížení (interní). Doporučujeme [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   - Vyberte virtuální síť vytvořenou v kroku 2.
1. Vytvořte virtuální počítač 1.
   - Použijte bitovou kopii SLES4SAP v galerii Azure, která je podporovaná pro SAP HANA na typu virtuálního počítače, který jste vybrali.
   - Vyberte sadu dostupnosti vytvořenou v kroku 3.
1. Vytvořte virtuální počítač 2.
   - Použijte bitovou kopii SLES4SAP v galerii Azure, která je podporovaná pro SAP HANA na typu virtuálního počítače, který jste vybrali.
   - Vyberte sadu dostupnosti vytvořenou v kroku 3. 
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
      1. Vyberte **možnost Virtuální síť**.
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

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server HANA základní cluster pacemakeru. Můžete použít stejný cluster Kardiostimulátoru pro SAP HANA a SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:
- **[A]**: Krok platí pro všechny uzly.
- **[1]**: Krok platí pouze pro uzel 1.
- **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

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

   Vytvořte logické svazky. Lineární svazek se vytvoří `lvcreate` při `-i` použití bez přepínače. Doporučujeme vytvořit prokládaný svazek pro lepší výkon vstupně-va a zarovnat velikosti prokládaných s hodnotami zdokumentovaným v [konfiguracích úložiště virtuálních počítačů SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Argument `-i` by měl být číslo základní fyzické `-I` svazky a argument je velikost prokládání. V tomto dokumentu se pro objem dat používají dva `-i` fyzické svazky, takže argument přepínače je nastaven na **2**. Velikost prokládání datového svazku je **256 KiB**. Pro svazek protokolu se používá jeden `-i` `-I` fyzický svazek, takže pro příkazy svazku protokolu se explicitně nepoužívají žádné nebo přepínače.  

   > [!IMPORTANT]
   > Použijte `-i` přepínač a nastavte jej na číslo základního fyzického svazku, pokud pro každé datové svazek, protokol nebo sdílené svazky použijete více než jeden fyzický svazek. `-I` Přepínač slouží k určení velikosti proklápěcího pruhu při vytváření proklápěcího svazku.  
   > Najdete [v tématu konfigurace úložiště virtuálních počítačů SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pro doporučené konfigurace úložiště, včetně velikosti prokládaných a počtu disků.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
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

1. **[A]** Nainstalujte balíčky SAP HANA s vysokou dostupností:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Chcete-li nainstalovat replikaci systému SAP HANA, postupujte podle kapitoly 4 [průvodce scénáři optimalizovaný pro výkon sap HANA SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Spusťte **hdblcm** program z DISKU DVD HANA. Na výzvu zadejte následující hodnoty:
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

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace systémové replikace SAP HANA 2.0

Kroky v této části používají následující předpony:

* **[A]**: Krok platí pro všechny uzly.
* **[1]**: Krok platí pouze pro uzel 1.
* **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

1. **[1]** Vytvořte databázi klienta.

   Pokud používáte SAP HANA 2.0 nebo MDC, vytvořte databázi klienta pro váš systém SAP NetWeaver. Vyměňte **NW1** za SID vašeho systému SAP.

   Proveďte následující příkaz jako\><hanasid adm :

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurace systémové replikace na prvním uzlu:

   Záloha databází jako <hanasid\>adm:

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
    
   Zaregistrujte druhý uzel a spusťte replikaci systému. Spusťte následující příkaz jako\><hanasid adm :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace systémové replikace SAP HANA 1.0

Kroky v této části používají následující předpony:

* **[A]**: Krok platí pro všechny uzly.
* **[1]**: Krok platí pouze pro uzel 1.
* **[2]**: Krok se vztahuje pouze na uzel 2 clusteru Pacemaker.

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

   Vytvořte primární lokalitu jako\><hanasid adm :

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurace systémové replikace v sekundárním uzlu.

   Zaregistrujte sekundární lokalitu jako\><hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

Nejprve vytvořte topologii HANA. Spusťte následující příkazy na jednom z uzlů clusteru Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Dále vytvořte prostředky HANA:

> [!IMPORTANT]
> Nedávné testování odhalilo situace, kdy netcat přestane reagovat na požadavky z důvodu nevyřízených položek a jeho omezení zpracování pouze jednoho připojení. Prostředek netcat přestane naslouchat požadavkům azure balancer a plovoucí IP přestane být k dispozici.  
> Pro stávající pacemaker clustery, doporučujeme v minulosti nahradit netcat socat. V současné době doporučujeme používat agenta prostředků azure-lb, který je součástí agenty prostředků balíčku, s následujícími požadavky na verzi balíčku:
> - Pro SLES 12 SP4/SP5 verze musí být alespoň agenty prostředků-4.3.018.a7fb5035-3.30.1.  
> - Pro SLES 15/15 SP1 verze musí být alespoň agenty prostředků-4.3.0184.6ee15eb2-4.13.1.  
>
> Všimněte si, že změna bude vyžadovat krátké prostoje.  
> Pro existující clustery Pacemaker, pokud konfigurace již byla změněna na použití socat, jak je popsáno v [Azure Load Balancer detekce posílení ,](https://www.suse.com/support/kb/doc/?id=7024128)není nutné okamžitě přepnout na agenta prostředků azure-lb.

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
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b>

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

Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

<pre><code>sudo crm_mon -r

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
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

Tato část popisuje, jak můžete otestovat nastavení. Každý test předpokládá, že jste kořenové a sap hana master běží na virtuálním počítači **hn1-db-0.**

### <a name="test-the-migration"></a>Testování migrace

Před zahájením testu se ujistěte, že Pacemaker nemá žádnou neúspěšnou akci (přes crm_mon -r), neexistují žádná neočekávaná omezení umístění (například zbytky testu migrace) a že HANA je stav synchronizace, například s SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Hlavní uzel SAP HANA můžete migrovat provedením následujícího příkazu:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"`, tato posloupnost příkazů by měla migrovat hlavní uzel SAP HANA a skupinu, která obsahuje virtuální IP adresu na hn1-db-1.

Po dokončení migrace vypadá výstup crm_mon -r takto

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

Prostředek SAP HANA na hn1-db-0 se nespustí jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migrace vytvoří omezení umístění, která je třeba znovu odstranit:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Je také nutné vyčistit stav prostředku sekundárního uzlu:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Sledujte stav prostředku HANA pomocí crm_mon -r. Po spuštění HANA na hn1-db-0 by výstup měl vypadat takto.

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

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testování agenta oplocení Azure (ne SBD)

Nastavení agenta oplocení Azure můžete otestovat zakázáním síťového rozhraní v uzlu hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Virtuální počítač by se teď měl restartovat nebo zastavit v závislosti na konfiguraci clusteru.
Pokud nastavíte `stonith-action` nastavení vypnout, virtuální počítač se zastaví a prostředky se migrují do spuštěného virtuálního počítače.

Po spuštění virtuálního počítače znovu, SAP HANA prostředek se nezdaří spustit jako sekundární, pokud nastavíte `AUTOMATED_REGISTER="false"`. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Test SBD oplocení

Můžete otestovat nastavení SBD tím, že zabije inkvizitor procesu.

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

Uzel clusteru hn1-db-0 by měl být restartován. Služba Pacemaker nemusí začít později. Ujistěte se, že jej znovu spustíte.

### <a name="test-a-manual-failover"></a>Testování ručního převzetí služeb při selhání

Ruční převzetí služeb při selhání `pacemaker` můžete otestovat zastavením služby v uzlu hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Po převzetí služeb při selhání můžete službu znovu spustit. Pokud nastavíte `AUTOMATED_REGISTER="false"`, prostředek SAP HANA na uzlu hn1-db-0 se nespustí jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testy SUSE

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je sap certifikovaný pro SAP HANA na konkrétní typy virtuálních počítačů, které používáte. Seznam typů virtuálních počítačů certifikovaných SAP HANA a verzí operačního systému pro tyto typy lze vyhledat v [platformách IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Nezapomeňte kliknout na podrobnosti o typu virtuálního uživatele uvedené maže získat úplný seznam SAP HANA podporované verze operačního systému pro konkrétní typ virtuálního uživatele

Spusťte všechny testovací případy, které jsou uvedeny v SAP HANA SR výkon optimalizovaný scénář nebo SAP HANA SR nákladově optimalizovaný scénář průvodce, v závislosti na případu použití. Návody najdete na [stránce Osvědčených postupů pro SAP][sles-for-sap-bp].

Následující testy jsou kopií popisů testů sap HANA SR performance optimized scenario SUSE Linux Enterprise Server for SAP Applications 12 SP1 guide. Pro aktuální verzi si vždy přečtěte také samotnou příručku. Před zahájením testu se vždy ujistěte, že hana je synchronizovaná, a také se ujistěte, že konfigurace kardiostimulátoru je správná.

V následujících popisech testů předpokládáme PREFER_SITE_TAKEOVER="true" a AUTOMATED_REGISTER="false".
Poznámka: Následující testy jsou navrženy tak, aby spustit v pořadí a závisí na stavu ukončení předchozí testy.

1. TEST 1: ZASTAVENÍ PRIMÁRNÍ DATABÁZE NA UZLU 1

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Kardiostimulátor by měl zjistit zastavenou instanci HANA a převzetí služeb při selhání do druhého uzlu. Po dokončení převzetí služeb při selhání je instance HANA v uzlu hn1-db-0 zastavena, protože pacemaker automaticky neregistruje uzel jako sekundární HANA.

   Spusťte následující příkazy pro registraci uzlu hn1-db-0 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZASTAVENÍ PRIMÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Kardiostimulátor by měl zjistit zastavenou instanci HANA a převzetí služeb při selhání do druhého uzlu. Po dokončení převzetí služeb při selhání je instance HANA v uzlu hn1-db-1 zastavena, protože pacemaker automaticky neregistruje uzel jako sekundární HANA.

   Spusťte následující příkazy pro registraci uzlu hn1-db-1 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: PRIMÁRNÍ DATABÁZE SELHÁNÍ V UZLU

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Kardiostimulátor by měl zjistit usmrcené hana instance a převzetí služeb při selhání do druhého uzlu. Po dokončení převzetí služeb při selhání je instance HANA v uzlu hn1-db-0 zastavena, protože pacemaker automaticky neregistruje uzel jako sekundární HANA.

   Spusťte následující příkazy pro registraci uzlu hn1-db-0 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: PRIMÁRNÍ DATABÁZE SELHÁNÍ NA UZLU 2

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Kardiostimulátor by měl zjistit usmrcené hana instance a převzetí služeb při selhání do druhého uzlu. Po dokončení převzetí služeb při selhání je instance HANA v uzlu hn1-db-1 zastavena, protože pacemaker automaticky neregistruje uzel jako sekundární HANA.

   Spusťte následující příkazy pro registraci uzlu hn1-db-1 jako sekundárního a vyčištění neúspěšného prostředku.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav zdroje po zkoušce:

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

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako root na uzlu hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Kardiostimulátor by měl zjistit usmrcený uzel clusteru a oplotit uzel. Jakmile je uzel oplocen, kardiostimulátor spustí převzetí instance HANA. Po restartování oploceného uzlu se kardiostimulátor nespustí automaticky.

   Spusťte následující příkazy ke spuštění kardiostimulátoru, vyčistěte zprávy SBD pro uzel hn1-db-0, zaregistrujte uzel hn1-db-0 jako sekundární a vyčistěte neúspěšný prostředek.

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

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: CRASH SEKUNDÁRNÍ UZLOVÁ NOD (NODE 2)

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Spusťte následující příkazy jako root na uzlu hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Kardiostimulátor by měl zjistit usmrcený uzel clusteru a oplotit uzel. Jakmile je uzel oplocen, kardiostimulátor spustí převzetí instance HANA. Po restartování oploceného uzlu se kardiostimulátor nespustí automaticky.

   Spusťte následující příkazy ke spuštění kardiostimulátoru, vyčistěte zprávy SBD pro uzel hn1-db-1, zaregistrujte uzel hn1-db-1 jako sekundární a vyčistěte neúspěšný prostředek.

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

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: ZASTAVENÍ SEKUNDÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Kardiostimulátor detekuje zastavenou instanci HANA a označí prostředek jako neúspěšný na uzlu hn1-db-1. Kardiostimulátor by měl automaticky restartovat instanci HANA. Chcete-li vyčistit stav selhání, spusťte následující příkaz.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: CRASH SEKUNDÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako\><hanasid adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Kardiostimulátor detekuje usmrcenou instanci HANA a označí prostředek jako neúspěšný na uzlu hn1-db-1. Chcete-li vyčistit stav selhání, spusťte následující příkaz. Kardiostimulátor by pak měl automaticky restartovat instanci HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: CRASH SEKUNDÁRNÍ UZEL LOKALITY (NODE 2) BĚŽÍ SEKUNDÁRNÍ HANA DATABÁZE

   Stav prostředku před zahájením testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Spusťte následující příkazy jako root na uzlu hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Kardiostimulátor by měl zjistit usmrcený uzel clusteru a oplotit uzel. Po restartování oploceného uzlu se kardiostimulátor nespustí automaticky.

   Spusťte následující příkazy ke spuštění kardiostimulátoru, vyčistěte zprávy SBD pro uzel hn1-db-1 a vyčistěte neúspěšný prostředek.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stav zdroje po zkoušce:

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

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]

