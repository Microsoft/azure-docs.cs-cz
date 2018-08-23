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
ms.openlocfilehash: 7a0797d79da95db77174a3e067a1e84276f286a5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060090"
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines"></a>Vysoká dostupnost SAP HANA na virtuálních počítačích Azure

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
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Pro místní vývoj můžete použít buď HANA System Replication nebo používat sdílené úložiště k vytvoření vysoké dostupnosti pro SAP HANA.
Na Azure virtual machines (VM) systémové replikace HANA v Azure je aktuálně že jediný podporovaný funkce vysoké dostupnosti. Replikace SAP HANA se skládá z jedné primární a alespoň jeden sekundární uzel. Změny dat na primárním uzlu jsou replikovány na sekundární uzel synchronně nebo asynchronně.

Tento článek popisuje, jak nasadit a nakonfigurovat službu virtual machines, nainstalujte rozhraní clusteru a nainstalujte a nakonfigurujte SAP HANA System Replication.
V ukázkové konfigurace příkazy instalace instance číslo **03**a ID systému HANA **HN1** se používají.

Přečtěte si následující poznámky SAP a Paper nejprve:

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP.
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure.
  * Podporované SAP software a operační systém (OS) a kombinace databáze.
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure.
* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2205917] se doporučuje nastavení operačního systému SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP [1944799] obsahuje pokyny pro SAP HANA pro SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik, které jsou hlášeny pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1984787] obsahuje obecné informace o operačním systémem SUSE Linux Enterprise Server 12.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux] [ planning-guide] průvodce.
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux] [ deployment-guide] (Tento článek).
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux] [ dbms-guide] průvodce.
* [SUSE Linux Enterprise Server pro SAP aplikace 12 SP3 osvědčené postupy vodítka][sles-for-sap-bp]
  * Nastavení SAP HANA SR výkonu optimalizované infrastruktury (SLES pro SAP aplikace 12 SP1). V Průvodci najdete všechny informace potřebné k nastavení systémové replikace SAP HANA pro místní vývoj. Tento průvodce použijte jako základ.
  * Nastavení SAP HANA SR náklady optimalizované infrastruktury (SLES pro SAP aplikace 12 SP1)

## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, se SAP HANA nainstaluje na dva virtuální počítače. Data se replikují pomocí systémové replikace HANA.

![Přehled vysoké dostupnosti SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Použití nastavení systémové replikace SAP HANA vyhrazené virtuální název hostitele a virtuální IP adresy. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení:

* Konfigurace front-endových: IP adresa 10.0.0.13 hn1 db
* Konfigurace back endu: připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí systémové replikace HANA
* Port testu: Port 62503
* Pravidla Vyrovnávání zatížení: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Nasazení pro Linux

Prostředek agenta pro SAP HANA je součástí operačního systému SUSE Linux Enterprise Server pro aplikace SAP.
Na webu Azure Marketplace obsahuje bitovou kopii operačního systému SUSE Linux Enterprise Server pro 12 aplikací SAP, který můžete použít k nasazení nových virtuálních počítačů.

### <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Můžete použít některou ze šablon rychlý start, které jsou na Githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti a tak dále.
Pokud chcete nasadit šablonu, postupujte podle těchto kroků:

1. Otevřít [databázové šablony] [ template-multisid-db] nebo [konvergované šablony] [ template-converged] na portálu Azure portal. 
    Pravidla Vyrovnávání zatížení pro databázi pouze vytvoří šablona databáze. Konvergované Šablona také vytvoří pravidla Vyrovnávání zatížení pro ASCS/SCS a instance Lajících (pouze Linux). Pokud máte v plánu pro instalaci systému založené na systému SAP NetWeaver a chcete nainstalovat instanci ASCS/SCS na stejných počítačů, [konvergované šablony][template-converged].

1. Zadejte následující parametry:
    - **ID systému SAP**: Zadejte ID systému SAP systému SAP, kterou chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazené.
    - **Stack – typ**: (Tento parametr platí pouze v případě, že používáte sblížené šablonu.) Vyberte typ SAP NetWeaver zásobníku.
    - **Typ operačního systému**: vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte **SLES 12**.
    - **Typ databáze**: vyberte **HANA**.
    - **Velikost systému SAP**: Zadejte počet protokoly, které je nový systém přechází k poskytování SAP. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor.
    - **Dostupnost systému**: vyberte **HA**.
    - **Uživatelské jméno Admin a heslo správce**: je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
    - **Nové nebo existující podsíti**: Určuje, zda má být vytvořena nová virtuální síť a podsíť, nebo použít existující podsítě. Pokud již máte virtuální síť, která je připojená k vaší místní síti, vyberte **existující**.
    - **ID podsítě**: ID podsítě, ke které má být připojen virtuální počítače. Virtuální počítač připojit k místní síti, vyberte podsíť virtuální sítě Azure ExpressRoute nebo VPN. ID obvykle vypadá jako **/subscriptions/\<ID předplatného > /resourceGroups/\<název skupiny prostředků > /providers/Microsoft.Network/virtualNetworks/\<název virtuální sítě > /subnets/ \<název podsítě >**.

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvořte skupinu prostředků.
1. Vytvořte virtuální síť.
1. Vytvoření skupiny dostupnosti.
   - Nastavte maximální počet aktualizační doména.
1. Vytvořte nástroj pro vyrovnávání zatížení (interní).
   - Vyberte virtuální síť vytvořili v kroku 2.
1. Vytvoření virtuálního počítače 1.
   - Použít minimálně SLES4SAP 12 SP1. Tento příklad používá image SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM.
   - Použít SLES pro SAP 12 SP2 (Premium).
   - Vyberte skupinu dostupnosti, které jsou vytvořené v kroku 3.
1. Vytvoření virtuálního počítače 2.
   - Použít minimálně SLES4SAP 12 SP1. Tento příklad používá image SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM.
   - Použít SLES pro SAP 12 SP2 (Premium).
   - Vyberte skupinu dostupnosti, které jsou vytvořené v kroku 3. 
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


## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) vytvořit cluster základní Pacemaker pro tento server HANA. Pro SAP HANA a SAP NetWeaver (A) SCS, můžete použít stejný cluster Pacemaker.

## <a name="install-sap-hana"></a>Instalace SAP HANA

Kroky v této části používají následující předpony:
- **[A]** : Krok platí pro všechny uzly.
- **[1]** : Krok platí pro pouze uzlu 1.
- **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

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

1. **[A]**  Nainstalujte balíčky vysoké dostupnosti SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Instalace systémové replikace SAP HANA, postupujte podle kapitoly 4 [Průvodce SAP HANA SR výkonu optimalizované scénářem](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]**  Spustit **hdblcm** program z disku DVD HANA. Zadejte následující hodnoty do příkazového řádku:
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

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace 2.0 systémové replikace SAP HANA

Kroky v této části používají následující předpony:

* **[A]** : Krok platí pro všechny uzly.
* **[1]** : Krok platí pro pouze uzlu 1.
* **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurace 1.0 systémové replikace SAP HANA

Kroky v této části používají následující předpony:

* **[A]** : Krok platí pro všechny uzly.
* **[1]** : Krok platí pro pouze uzlu 1.
* **[2]** : Krok se vztahuje na uzlu 2 pouze Pacemaker clusteru.

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

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Vytvořit prostředky clusteru SAP HANA

Nejprve vytvořte topologie HANA. Spuštěním následujících příkazů na jednom uzlu clusteru Pacemaker:

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

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

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

Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

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
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

Tato část popisuje, jak otestovat vašeho nastavení. Každý test předpokládá, že jste kořenový a hlavním serveru SAP HANA běží na **hn1-db-0** virtuálního počítače.

### <a name="test-the-migration"></a>Test migrace

Než spustíte test, ujistěte se, že Pacemaker nemá žádné neúspěšné akci (prostřednictvím crm_mon - r), neexistují žádná omezení neočekávané umístění (například zbývajícími testu migrace) a že HANA je stav synchronizace, například pomocí SAPHanaSR showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Hlavní uzel SAP HANA můžete migrovat spuštěním následujícího příkazu:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Pokud nastavíte `AUTOMATED_REGISTER="false"`, toto pořadí příkazů byste migrovat hlavní uzel SAP HANA a skupiny, která obsahuje virtuální IP adresu na hn1-db-1.

Po dokončení migrace crm_mon - r výstup vypadá takto

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Prostředek SAP HANA na hn1-db-0 nepodaří spustit jako sekundární. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migrace vytvoří omezení umístění, které je potřeba znovu odstranit:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Budete potřebovat k vyčištění stavu sekundárního uzlu prostředků:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorování stavu prostředků HANA pomocí crm_mon - r. Po spuštění HANA na hn1-db-0, výstup by měl vypadat takto

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testovací agent monitorování geografických zón Azure (ne SBD)

Instalace agenta monitorování geografických zón Azure můžete otestovat tím, že zakážete síťového rozhraní na uzlu hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Virtuální počítač by měl nyní restartovat nebo zastavit v závislosti na konfiguraci clusteru.
Pokud jste nastavili `stonith-action` nastavení vypnuté, se zastaví virtuální počítač a prostředky se migrují do spuštěného virtuálního počítače.

Po spuštění virtuálního počítače se SAP HANA prostředků se nepodaří spustit jako sekundární, pokud jste nastavili `AUTOMATED_REGISTER="false"`. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Monitorování geografických zón SBD testu

Je-li otestovat nastavení SBD, ukončuje se proces inquisitor.

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

By měla restartovat uzel clusteru hn1-db-0. Pacemaker služby nemusí Začínáme později. Ujistěte se, že ji znovu spustit.

### <a name="test-a-manual-failover"></a>Ruční převzetí služeb při selhání testu

Ruční převzetí služeb při selhání můžete otestovat pomocí zastavení `pacemaker` služby na uzlu hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Po převzetí služeb při selhání můžete spustit službu znovu. Pokud nastavíte `AUTOMATED_REGISTER="false"`, SAP HANA prostředků na uzlu hn1-db-0 se nepodaří spustit jako sekundární. V takovém případě konfigurace HANA instance jako sekundární spuštěním tohoto příkazu:

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

Spouští všechny testovací případy, které jsou uvedeny v Průvodci SAP HANA SR výkonu optimalizované scénář nebo SAP HANA SR náklady optimalizované scénáře, v závislosti na vašemu případu použití. Postupy najdete v [SLES pro SAP osvědčené postupy pro stránku][sles-for-sap-bp].

Následující testy jsou kopie popis testu SAP HANA SR výkonu optimalizované scénář SUSE Linux Enterprise Server pro Průvodce SP1 12 aplikací SAP. Pro aktuální verzi, vždycky si také přečíst Průvodce samotný. Vždy zajistěte, aby HANA synchronizované před spuštěním testu a také se ujistěte, že konfigurace Pacemaker je správná.

V následujících popisech test předpokládáme PREFER_SITE_TAKEOVER = "true" a AUTOMATED_REGISTER = "false".
Poznámka: Následující testy mají spouštět v pořadí a závisí na stavu ukončení předchozích testů.

1. TEST 1: ZASTAVTE PRIMÁRNÍ DATABÁZE NA UZLU 1

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker by měl zjistit zastavené HANA instance a převzetí služeb při selhání do jiného uzlu. Po dokončení převzetí služeb při selhání instance HANA na uzlu hn1-db-0 je zastavena, protože Pacemaker jako sekundární HANA automaticky nezaregistruje uzlu.

   Spusťte následující příkazy k registraci uzlu hn1-db-0 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZASTAVENÍ PRIMÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker by měl zjistit zastavené HANA instance a převzetí služeb při selhání do jiného uzlu. Po dokončení převzetí služeb při selhání instance HANA na uzlu hn1-db-1 je zastavena, protože Pacemaker jako sekundární HANA automaticky nezaregistruje uzlu.

   Spusťte následující příkazy k registraci uzlu hn1-db-1 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 3: PŘI SELHÁNÍ PRIMÁRNÍ DATABÁZE V UZLU

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker by měl zjistit ukončil. HANA instance a převzetí služeb při selhání do jiného uzlu. Po dokončení převzetí služeb při selhání instance HANA na uzlu hn1-db-0 je zastavena, protože Pacemaker jako sekundární HANA automaticky nezaregistruje uzlu.

   Spusťte následující příkazy k registraci uzlu hn1-db-0 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 4: PŘI SELHÁNÍ PRIMÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker by měl zjistit ukončil. HANA instance a převzetí služeb při selhání do jiného uzlu. Po dokončení převzetí služeb při selhání instance HANA na uzlu hn1-db-1 je zastavena, protože Pacemaker jako sekundární HANA automaticky nezaregistruje uzlu.

   Spusťte následující příkazy k registraci uzlu hn1-db-1 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: PŘI SELHÁNÍ PRIMÁRNÍ LOKALITY UZEL (1)

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře na uzlu hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl zjistit uzel clusteru ukončil a plotu uzlu. Jakmile se uzel ohraničených, aktivuje Pacemaker převzetí instance HANA. Po restartování uzlu ohraničených Pacemaker nespustí automaticky.

   Spuštěním následujících příkazů spusťte Pacemaker, čištění SBD zprávy pro uzel hn1-db-0, zaregistrujte uzel hn1-db-0 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 6: PŘI SELHÁNÍ SEKUNDÁRNÍ LOKALITY UZEL (2)

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře na uzlu hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl zjistit uzel clusteru ukončil a plotu uzlu. Jakmile se uzel ohraničených, aktivuje Pacemaker převzetí instance HANA. Po restartování uzlu ohraničených Pacemaker nespustí automaticky.

   Spuštěním následujících příkazů spusťte Pacemaker, čištění SBD zprávy pro uzel hn1-db-1, zaregistrujte uzel hn1-db-1 jako sekundární a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTU 7: ZASTAVENÍ SEKUNDÁRNÍ DATABÁZE NA UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker zjistí zastavená instance HANA a označit prostředek jako neúspěšný na uzlu hn1-db-1. Spusťte následující příkaz k vyčištění stavu selhání. Pacemaker by pak automaticky restartovat instanci HANA.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 8: PŘI SELHÁNÍ SEKUNDÁRNÍ DATABÁZI NA UZLU 2

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako \<hanasid > adm na uzlu hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker zjistí ukončil. instance HANA a označit prostředek jako neúspěšný na uzlu hn1-db-1. Spusťte následující příkaz k vyčištění stavu selhání. Pacemaker by pak automaticky restartovat instanci HANA.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 9: PŘI SELHÁNÍ SEKUNDÁRNÍ LOKALITY UZEL (2) SPUŠTĚNÍ SEKUNDÁRNÍ HANA DATABASE

   Stav prostředku před spuštěním testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře na uzlu hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker by měl zjistit uzel clusteru ukončil a plotu uzlu. Po restartování uzlu ohraničených Pacemaker nespustí automaticky.

   Spusťte následující příkazy, abyste mohli začít Pacemaker vyčistit SBD zprávy pro uzel hn1-db-1 a vyčištění prostředků se nezdařilo.

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
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Další postup

* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)
