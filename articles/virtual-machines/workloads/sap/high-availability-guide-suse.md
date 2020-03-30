---
title: Vysoká dostupnost virtuálních počítačích Azure pro SAP NetWeaver na webu SLES | Dokumenty společnosti Microsoft
description: Průvodce vysokou dostupností pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348826"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7.50.
V ukázkových konfiguracích, instalačních příkazech atd. Používá se instance ASCS číslo 00, číslo ers instance 02 a SAP System ID NW1. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste [konvergované šablony][template-converged] s ID systému SAP NW1 k vytvoření prostředků.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* SAP Poznámka [1928533][1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553][2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2205917][2205917] doporučil nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [1944799][1944799] má SAP HANA pokyny pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [2178632][2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498][2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692][2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1984787][1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* SAP Note [1999351][1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [SUSE SAP HA Průvodce osvědčenými postupy][suse-ha-guide] Vodítka obsahují všechny požadované informace pro místní nastavení aplikace Netweaver HA a SAP HANA System Replication. Tato vodítka použijte jako obecný směrný plán. Poskytují mnohem podrobnější informace.
* [SUSE Rozšíření o vysokou dostupnost 12 SP3 Poznámky k verzi][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti vyžaduje sap netweaver server nfs. Server systému systému zabezpečení systému zabezpečení je nakonfigurován v samostatném clusteru a může být používán více systémy SAP.

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-suse/ha-suse.png)

Server NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. Doporučujeme používat [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení (A)SCS a ERS.

### <a name="ascs"></a>(A) Scs

* Front-endová konfigurace
  * IP adresa 10.0.0.7
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Front-endová konfigurace
  * IP adresa 10.0.0.8
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Back-endová konfigurace
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A)SCS/ERS


## <a name="setting-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupného serveru systému windows

SAP NetWeaver vyžaduje sdílené úložiště pro adresář přenosu a profilu. Přečtěte si [vysokou dostupnost pro nfs na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha] o tom, jak nastavit server nfs pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A)SCS

Buď můžete použít šablonu Azure z GitHubu k nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, sady dostupnosti a nástroje pro vyrovnávání zatížení, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu přes šablonu Azure

Azure Marketplace obsahuje image pro SUSE Linux Enterprise Server pro SAP Aplikace 12, které můžete použít k nasazení nových virtuálních počítačů. Obrázek tržiště obsahuje agenta prostředků pro SAP NetWeaver.

Můžete použít jednu ze šablon rychlého startu na GitHubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, nástroj pro vyrovnávání zatížení, sadu dostupnosti atd. Chcete-li šablonu nasadit, postupujte takto:

1. Otevřete [šablonu ASCS/SCS Multi SID][template-multisid-xscs] nebo [konvergotelou šablonu][template-converged] na webu Azure Portal. 
   Šablona ASCS/SCS vytvoří pouze pravidla vyrovnávání zatížení pro instance SAP NetWeaver ASCS/SCS a ERS (pouze linux), zatímco konvergovaná šablona také vytvoří pravidla vyrovnávání zatížení pro databázi (například Microsoft SQL Server nebo SAP HANA). Pokud plánujete nainstalovat systém založený na programu SAP NetWeaver a chcete také nainstalovat databázi na stejných počítačích, použijte [konvergované šablony][template-converged].
1. Zadejte následující parametry
   1. Předpona prostředků (pouze šablona ASCS/SCS Multi SID)  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   3. ID systému Sap (pouze konvergovaná šablona)  
      Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazeny.
   4. Typ zásobníku  
      Výběr typu zásobníku SAP NetWeaver
   5. Typ operačního příkazu  
      Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte SLES 12 BYOS
   6. Typ db  
      Vybrat HANA
   7. Velikost systému Sap.  
      Množství SAPS, které nový systém poskytuje. Pokud si nejste jisti, kolik SAPS systém vyžaduje, zeptejte se svého technologického partnera SAP nebo systémového integrátora
   8. Dostupnost systému  
      Vybrat HA
   9. Admin uživatelské jméno a admin heslo  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   10. ID podsítě  
   Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k čemuž by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako /subscriptions/**&lt;&gt;ID předplatného**/resourceGroups/**&lt;název&gt;skupiny prostředků**/providers/Microsoft.Network/virtualNetworks/**&lt;název&gt;virtuální sítě**/podsítě/**&lt;název&gt; podsítě**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu přes portál Azure

Nejprve je třeba vytvořit virtuální počítače pro tento cluster systému připojení k systému sítě. Poté vytvoříte nástroj pro vyrovnávání zatížení a použijete virtuální počítače v back-endovém fondu.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření sady dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte alespoň SLES4SAP 12 SP1, v tomto příkladu slez4SAP 12 SP1 obrazhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Používá se SLES Pro SAP Aplikace 12 SP1  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Vytvořit virtuální počítač 2  
   Použijte alespoň SLES4SAP 12 SP1, v tomto příkladu slez4SAP 12 SP1 obrazhttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Používá se SLES Pro SAP Aplikace 12 SP1  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Přidání alespoň jednoho datového disku do obou virtuálních počítačů  
   Datové disky se používají pro adresář /usr/sap/`<SAPSID`>
1. Vytvoření systému vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření front-endových IP adres
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **nw1-ascs-frontend)**
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.7**)
         1. Klikněte na OK.
      1. IP adresa 10.0.0.8 pro ASCS ERS
         * Zopakujte výše uvedené kroky a vytvořte IP adresu pro ERS (například **10.0.0.8** a **nw1-aers-backend)**
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **nw1-backend)**
      1. Klikněte na Přidat virtuální počítač.
      1. Vybrat virtuální počítač
      1. Vyberte virtuální počítače clusteru (A)SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **nw1-ascs-hp)**
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**02** pro ASCS ERS
         * Opakujte výše uvedené kroky a vytvořte sondu stavu pro ERS (například 621**02** a **nw1-aers-hp)**
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb-ascs)**
         1. Vyberte front-endovou IP adresu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-ascs-frontend**, **nw1-backend** a **nw1-ascs-hp)**
         1. Vybrat **porty HA**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
         * Opakováním výše uvedených kroků vytvořte pravidla vyrovnávání zatížení pro ERS (například **nw1-lb-ers)**
1. Případně pokud váš scénář vyžaduje základní vyrovnávání zatížení (interní), postupujte takto:  
   1. Vytvoření front-endových IP adres
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **nw1-ascs-frontend)**
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.7**)
         1. Klikněte na OK.
      1. IP adresa 10.0.0.8 pro ASCS ERS
         * Chcete-li vytvořit IP adresu pro ERS (například **10.0.0.8** a **nw1-aers-frontend),** opakujte výše uvedené kroky( například 10.0.0.8 a nw1-aers-frontend)
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **nw1-backend)**
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte dříve vytvořenou sadu dostupností.
      1. Výběr virtuálních počítačů clusteru (A)SCS
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **nw1-ascs-hp)**
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**02** pro ASCS ERS
         * Opakujte výše uvedené kroky a vytvořte sondu stavu pro ERS (například 621**02** a **nw1-aers-hp)**
   1. Pravidla vyrovnávání zatížení
      1. 32**00** TCP pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb-3200**)
         1. Vyberte front-endovou IP adresu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-ascs-frontend)**
         1. Zachovat protokol **TCP**, zadat port **3200**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASCS
      1. Další porty pro ASCS ERS
         * Opakujte výše uvedené kroky pro porty 33**02**, 5**02**13, 5**02**14, 5**02**16 a TCP pro ASCS ERS

> [!Note]
> Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Vytvořit cluster kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server (A)SCS základní cluster pacemakeru.

### <a name="installation"></a>Instalace

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Instalace konektoru SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Známý problém s použitím pomlčky v názvech hostitelů je opraven verzí **3.1.1** balíčku **sap-suse-cluster-connector**. Ujistěte se, že používáte alespoň verzi 3.1.1 balíčku sap-suse-cluster-connector, pokud používáte uzly clusteru s pomlčkou v názvu hostitele. V opačném případě nebude cluster fungovat. 

   Ujistěte se, že jste nainstalovali novou verzi konektoru clusteru SAP SUSE. Ten starý byl nazýván sap_suse_cluster_connector a nový se nazývá **sap-suse-cluster-connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** Aktualizace agentů prostředků SAP  
   
   Oprava pro balíček agenti prostředků je nutné použít novou konfiguraci, která je popsána v tomto článku. Můžete zkontrolovat, zda je oprava již nainstalována pomocí následujícího příkazu

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Výstup by měl být podobný

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Pokud příkaz grep nenajde parametr IS_ERS, je třeba nainstalovat opravu uvedenou na [stránce stahování SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci sap netweaveru

1. **[A]** Vytvoření sdílených adresářů

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Konfigurace autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvoření souboru pomocí

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Restartujte autofs pro připojení nových sdílených složek

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Konfigurace souboru SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Chcete-li změnu aktivovat, restartujte agenta.

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/ERS

1. **[1]** Vytvoření virtuálního prostředku IP a sondy stavu pro instanci ASCS

   > [!IMPORTANT]
   > Nedávné testování odhalilo situace, kdy netcat přestane reagovat na požadavky z důvodu nevyřízených položek a jeho omezení zpracování pouze jednoho připojení. Prostředek netcat přestane naslouchat požadavkům azure balancer a plovoucí IP přestane být k dispozici.  
   > Pro stávající pacemaker clustery, doporučujeme v minulosti nahradit netcat socat. V současné době doporučujeme používat agenta prostředků azure-lb, který je součástí agenty prostředků balíčku, s následujícími požadavky na verzi balíčku:
   > - Pro SLES 12 SP4/SP5 verze musí být alespoň agenty prostředků-4.3.018.a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 verze musí být alespoň agenty prostředků-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Všimněte si, že změna bude vyžadovat krátké prostoje.  
   > Pro existující clustery Pacemaker, pokud konfigurace již byla změněna na použití socat, jak je popsáno v [Azure Load Balancer detekce posílení ,](https://www.suse.com/support/kb/doc/?id=7024128)není nutné okamžitě přepnout na agenta prostředků azure-lb.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalace systému ASCS aplikace SAP NetWeaver  

   Nainstalujte SAP NetWeaver ASCS jako root na první uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro ASCS, například <b>nw1-ascs</b>, <b>10.0.0.7</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**NW1**/ASCS**00**, zkuste nastavit vlastníka a skupinu složky ASCS**00** a opakujte akci.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Vytvoření virtuálního IP prostředku a sondy stavu pro instanci ERS

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalace SAP NetWeaver ERS

   Nainstalujte SAP NetWeaver ERS jako root na druhý uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endové konfigurace nástroje pro vyrovnávání zatížení pro ERS, například <b>nw1-aers</b>, <b>10.0.0.8</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastavují oprávnění správně a instalace se nezdaří.

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**NW1**/ERS**02**, zkuste nastavit vlastníka a skupinu složky ERS**02** a opakujte akci.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Přizpůsobení profilů instancí ASCS/SCS a ERS
 
   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * PROFIL ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Konfigurace keep alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím softwarového nástroje pro vyrovnávání zatížení. Systém vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém uzláčase. Chcete-li tomu zabránit, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS a změnit nastavení systému Linux. Další informace [načtete v poznámce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive byl již přidán v posledním kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Konfigurace uživatelů SAP po instalaci

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Přidání služeb ASCS a ERS SAP do souboru sapservice

   Přidejte položku služby ASCS do druhého uzlu a zkopírujte položku služby ERS do prvního uzlu.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Vytvoření prostředků clusteru SAP

Pokud používáte architekturu enqueue server 1 (ENSA1), definujte prostředky takto:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  SAP zavedl podporu pro enqueue server 2, včetně replikace, od SAP NW 7.52. Počínaje platformou ABAP 1809 je ve výchozím nastavení nainstalován server 2 fronty. Viz SAP poznámka [2630416](https://launchpad.support.sap.com/#/notes/2630416) pro zařazení do fronty server 2 podporu.
Pokud používáte architekturu enqueue server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky takto:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Pokud upgradujete ze starší verze a přepájíte na server fronty 2, přečtěte si poznámku SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

Některé databáze vyžadují, aby byla instalace instance databáze spuštěna na aplikačním serveru. Připravte virtuální počítače aplikačního serveru, aby je bylo možné v těchto případech používat.

Níže uvedené kroky předpokládají, že nainstalujete aplikační server na server odlišný od serverů ASCS/SCS a HANA. V opačném případě nejsou některé z následujících kroků (například konfigurace překladu názvů hostitele) potřeba.

1. Konfigurace operačního systému

   Zmenšete velikost nečisté mezipaměti. Další informace naleznete [v tématu Nízký výkon zápisu na serverech SLES 11/12 s velkou paměti RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Nastavení překladu názvů hostitele

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   ```bash
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Vytvoření adresáře sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurace autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvoření nového souboru

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Restartujte autofs pro připojení nových sdílených složek

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurace souboru SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Chcete-li změnu aktivovat, restartujte agenta.

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalován na SAP HANA. Pro tuto instalaci můžete použít všechny podporované databáze. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure (VMs)][sap-hana-ha]. Seznam podporovaných databází naleznete v [poznámce SAP 1928533][1928533].

1. Spuštění instalace instance databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako root pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro databázi například <b>nw1-db</b> a <b>10.0.0.13</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Chcete-li nainstalovat aplikační server SAP, postupujte takto.

1. Příprava aplikačního serveru

   Postupujte podle výše uvedených kroků v kapitole [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) a připravte si aplikační server.

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další aplikační server SAP NetWeaver.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte zabezpečené úložiště SAP HANA tak, aby ukazovalo na virtuální název nastavení systémové replikace SAP HANA.

   Spuštěním následujícího příkazu zobrazíte seznam položek
   <pre><code>hdbuserstore List
   </code></pre>

   To by mělo vypsat všechny položky a mělo by vypadat podobně jako
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položky směřuje na virtuální počítač a ne na IP adresu nástroje pro vyrovnávání zatížení. Tuto položku je třeba změnit tak, aby přecóklála na virtuální název hostitele nástroje pro vyrovnávání zatížení. Ujistěte se, že používáte stejný port (**30313** ve výstupu výše) a název databáze (**HN1** ve výstupu výše)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

Následující testy jsou kopií testovacích případů v doporučených postupech příručky SUSE. Jsou kopírovány pro vaše pohodlí. Vždy si také přečtěte průvodce doporučenými postupy a proveďte všechny další testy, které mohly být přidány.

1. Test HAGetFailoverConfig, HACheckConfig a HACheckFailoverConfig

   Spusťte následující \<příkazy jako sapsid>adm na uzlu, kde je aktuálně spuštěna instance ASCS. Pokud příkazy nezdaří s FAIL: Nedostatek paměti, může být způsobeno pomlčky v názvu hostitele. Jedná se o známý problém a bude opraven suse v sap-suse-cluster-connector balíčku.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Ruční migrace instance ASCS

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako root pro migraci instance ASCS.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test HAFailoverToNode

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující \<příkazy jako sapsid>adm pro migraci instance ASCS.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simulovat selhání uzlu

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spuštění následujícího příkazu jako kořenového adresáře v uzlu, ve kterém je spuštěna instance ASCS

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pokud používáte SBD, kardiostimulátor by neměl automaticky spustit na uzlu usmrcené. Stav po spuštění uzlu by měl vypadat takto.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Pomocí následujících příkazů můžete spustit kardiostimulátor na uzlu usmrcené, vyčistit zprávy SBD a vyčistit neúspěšné prostředky.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Ruční restartování instance ASCS

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Vytvořte zámek fronty, například upravit uživatele v transakci su01. Spusťte následující \<příkazy jako sapsid>adm na uzlu, kde je spuštěna instance ASCS. Příkazy zastaví instanci ASCS a spustí ji znovu. Pokud používáte architekturu enqueue server 1, očekává se, že zámek fronty bude v tomto testu ztracen. Pokud používáte architekturu enqueue server 2, bude zařazena do fronty zachována. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Instance ASCS by nyní měla být v kardiostimulátoru zakázána.

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Spusťte instanci ASCS znovu na stejném uzlu.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Uzamčení fronty transakce su01 by měly být ztraceny a back-end by měly být resetovány. Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Zastavte proces serveru zpráv

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkazy jako root k identifikaci procesu serveru zpráv a jeho usmrcení.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Pokud zabijete server zpráv pouze jednou, bude restartován sapstart. Pokud jej zabijete dostatečně často, Pacemaker nakonec přesune instanci ASCS do jiného uzlu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Proces serveru zastavování zařazení do fronty

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako root v uzlu, kde je spuštěna instance ASCS, aby se zničil server fronty.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Instance ASCS by měla okamžitě přepojit převzetí služeb při selhání do jiného uzlu. Instance ERS by také měla po spuštění instance ASCS převezměte služby při selhání. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Proces serveru replikace zařazení do fronty

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkaz jako root v uzlu, kde je spuštěna instance ERS, abyste zničili proces replikačního serveru fronty.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Pokud spustíte příkaz pouze jednou, sapstart restartuje proces. Pokud jej spustíte dostatečně často, sapstart nerestartuje proces a prostředek bude v zastaveném stavu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředku instance ERS.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Zabít proces sapstartsrv do fronty

   Stav prostředku před zahájením testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkazy jako root v uzlu, kde je spuštěnascs.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Proces sapstartsrv by měl být vždy restartován agentem prostředků Pacemaker. Stav zdroje po zkoušce:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Ha pro SAP NW na virtuálních počítačích Azure na SLES pro sap aplikace multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
