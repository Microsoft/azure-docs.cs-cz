---
title: Vysoká dostupnost virtuálních počítačích Azure pro SAP NW v RHEL se soubory Azure NetApp| Dokumenty společnosti Microsoft
description: Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351243"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux u souborů Azure NetApp pro aplikace SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7.50 pomocí [souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
V ukázkových konfiguracích, instalačních příkazech atd. Instance ASCS je číslo 00, instance ERS je číslo 01, instance primární aplikace (PAS) je 02 a instance aplikace (AAS) je 03. Používá se ID systému SAP QAS. 

Databázová vrstva není podrobně popsána v tomto článku.  

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace ke službě Azure NetApp Files][anf-azure-doc] 
* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [SAP Netweaver v clusteru kardiostimulátorů](https://access.redhat.com/articles/3150081)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/ERS pro SAP Netweaver se samostatnými prostředky v RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/ERS pomocí samostatného enqueue serveru 2 (ENSA2) v kardiostimulátoru na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplikace NetApp SAP v Microsoft Azure pomocí souborů Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Vysoká dostupnost (HA) pro centrální služby SAP Netweaver vyžaduje sdílené úložiště.
K dosažení tohoto cíle na Red Hat Linuxu bylo zatím nutné vybudovat samostatný vysoce dostupný cluster GlusterFS. 

Nyní je možné dosáhnout SAP Netweaver HA pomocí sdíleného úložiště, nasazené na Soubory Azure NetApp. Použití souborů Azure NetApp pro sdílené úložiště eliminuje potřebu dalšího [clusteru GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Kardiostimulátor je stále zapotřebí pro HA centrálních služeb SAP Netweaver (ASCS / SCS).

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. Doporučujeme používat [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení se samostatnými front-end IP adresy pro (A)SCS a ERS.

### <a name="ascs"></a>(A) Scs

* Front-endová konfigurace
  * IP adresa 192.168.14.9
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 36<strong>&lt;&gt; nr</strong> TCP
  * 39<strong>&lt;&gt; nr</strong> TCP
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Front-endová konfigurace
  * IP adresa 192.168.14.10
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 33<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* Back-endová konfigurace
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Souborů Azure NetApp 

SAP NetWeaver vyžaduje sdílené úložiště pro adresář přenosu a profilu.  Než budete pokračovat v nastavení infrastruktury souborů Azure NetApp, seznamte se s [dokumentací k souborům Azure NetApp][anf-azure-doc]. Zkontrolujte, jestli vybraná oblast Azure nabízí soubory Azure NetApp. Následující odkaz ukazuje dostupnost souborů Azure NetApp podle oblasti Azure: [Dostupnost souborů Azure NetApp podle oblasti Azure][anf-avail-matrix].

Soubory Azure NetApp jsou dostupné v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Před nasazením souborů Azure NetApp požádejte o připojení k souborům Azure NetApp podle [pokynů k registru souborů Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků souborů Azure NetApp  

Postup předpokládá, že jste už nasadili [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Prostředky Azure NetApp Files a virtuální počítače, kde se namontují prostředky Azure NetApp Files, musí být nasazené ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure.  

1. Pokud jste to ještě neudělali, požádejte o [připojení k souborům Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Vytvořte účet NetApp ve vybrané oblasti Azure podle [pokynů k vytvoření účtu NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Nastavte fond kapacity souborů Azure NetApp podle [pokynů, jak nastavit fond kapacity souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura SAP Netweaver prezentovaná v tomto článku používá jeden fond kapacity souborů Azure NetApp, premium sku. Doporučujeme Azure NetApp Files Premium SKU pro úlohy aplikací SAP Netweaver v Azure.  
4. Delegujte podsíť na soubory Azure NetApp, jak je popsáno v [pokynech Delegovat podsíť na soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Nasazení svazků souborů Azure NetApp podle [pokynů k vytvoření svazku pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Nasazení svazků v určené [podsíti](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Soubory Azure NetApp . Nezapomeňte, že prostředky Azure NetApp Files a virtuální počítače Azure musí být ve stejné virtuální síti Azure nebo ve virtuálních sítích Azure. V tomto příkladu používáme dva svazky Souborů Azure NetApp: sap<b>QAS</b> a transSAP. Cesty k souborům, které jsou připojeny k odpovídajícím přípojkovým bodům, jsou /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys atd.  

   1. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS)</b>
   2. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. objem transSAP (nfs://192.168.24.4/transSAP)
   6. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. objem sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
V tomto příkladu jsme použili soubory Azure NetApp pro všechny systémy souborů SAP Netweaver k předvedení, jak lze použít soubory Azure NetApp. Systémy souborů SAP, které není nutné připojit prostřednictvím systému souborů NFS, lze také nasadit jako [diskové úložiště Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . V tomto <b>příkladu a-e</b> musí být na Azure NetApp Files a <b>f-g</b> (to znamená/usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) může být nasazen jako úložiště disků Azure. 

### <a name="important-considerations"></a>Důležité informace

Při zvažování soubory Azure NetApp pro SAP Netweaver na architektuře SUSE s vysokou dostupností, uvědomte si následující důležité aspekty:

- Minimální kapacita bazénu je 4 TiB. Velikost fondu kapacity lze zvýšit v krocích po 1 TiB.
- Minimální objem je 100 GiB
- Soubory Azure NetApp a všechny virtuální počítače, kde se připojí svazky souborů Azure NetApp, musí být ve stejné virtuální síti Azure nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ve stejné oblasti. Azure NetApp Files přístup přes partnerský vztah virtuální sítě ve stejné oblasti je teď podporované. Přístup k Azure NetApp přes globální partnerský vztah ještě není podporován.
- Vybraná virtuální síť musí mít podsíť delegovanou na soubory Azure NetApp.
- Azure NetApp Files nabízí [zásady exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): můžete řídit povolené klienty, typ přístupu (čtení&zápis, jen pro čtení atd.). 
- Funkce Azure NetApp Files ještě není zónová. V současné době azure netapp soubory funkce není nasazenve všech zónách dostupnosti v oblasti Azure. Uvědomte si potenciální důsledky latence v některých oblastech Azure. 
- Svazky souborů Azure NetApp lze nasadit jako svazky NFSv3 nebo NFSv4.1. Oba protokoly jsou podporovány pro aplikační vrstvu SAP (ASCS/ERS, aplikační servery SAP). 

## <a name="setting-up-ascs"></a>Nastavení (A)SCS

V tomto příkladu byly prostředky nasazeny ručně prostřednictvím [portálu Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu přes portál Azure

Nejprve je třeba vytvořit svazky souborů Azure NetApp. Nasaďte virtuální chod. Poté vytvoříte nástroj pro vyrovnávání zatížení a použijete virtuální počítače v back-endovém fondu.

1. Vytvoření systému vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření front-endových IP adres
      1. IP adresa 192.168.14.9 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **front-end. Qas. ASCS**)
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **192.168.14.9**)
         1. Klikněte na OK.
      1. IP adresa 192.168.14.10 pro ASCS ERS
         * Chcete-li vytvořit IP adresu pro ERS (například **192.168.14.10** a frontend, opakujte výše uvedené kroky pod **"a". Qas. ERS**)
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte Virtuální počítač. 
      1. Vyberte virtuální počítače clusteru (A)SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **stav. Qas. ASCS**)
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**01** pro ASCS ERS
            * Opakujte výše uvedené kroky pod "c" a vytvořte sondu stavu pro ERS (například 621**01** a **zdraví. Qas. ERS**)
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla pro vyrovnávání zatížení (například **lb. Qas. ASCS**)
         1. Vyberte adresu IP front-end pro ASCS, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **front-end. Qas. ASCS**, **back-end. QAS** a **zdraví. Qas. ASCS**)
         1. Vybrat **porty HA**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
         * Opakováním výše uvedených kroků vytvořte pravidla vyrovnávání zatížení pro ERS (například **lb. Qas. ERS**)
1. Případně pokud váš scénář vyžaduje základní vyrovnávání zatížení (interní), postupujte takto:  
   1. Vytvoření front-endových IP adres
      1. IP adresa 192.168.14.9 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **front-end. Qas. ASCS**)
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **192.168.14.9**)
         1. Klikněte na OK.
      1. IP adresa 192.168.14.10 pro ASCS ERS
         * Chcete-li vytvořit IP adresu pro ERS (například **192.168.14.10** a frontend, opakujte výše uvedené kroky pod **"a". Qas. ERS**)
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte sadu dostupnosti, kterou jste vytvořili dříve pro ASCS. 
      1. Výběr virtuálních počítačů clusteru (A)SCS
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **stav. Qas. ASCS**)
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**01** pro ASCS ERS
            * Opakujte výše uvedené kroky pod "c" a vytvořte sondu stavu pro ERS (například 621**01** a **zdraví. Qas. ERS**)
   1. Pravidla vyrovnávání zatížení
      1. 32**00** TCP pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla pro vyrovnávání zatížení (například **lb. Qas. ASCS.3200**)
         1. Vyberte adresu IP front-end pro ASCS, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **front-end. Qas. ASCS**)
         1. Zachovat protokol **TCP**, zadat port **3200**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky pod písmeny "d" pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASCS
      1. Další porty pro ASCS ERS
         * Opakujte výše uvedené kroky pod "d" pro porty 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 a TCP pro ASCS ERS

      > [!Note]
      > Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

      > [!IMPORTANT]
      > Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Zakázat mapování ID (pokud používáte nfsv4.1)

Pokyny v této části jsou pouze použitelné, pokud používáte svazky souborů Azure NetApp s protokolem NFSv4.1. Proveďte konfiguraci na všech virtuálních počítačích, kde se připojí svazky Souborů Azure NetApp NFSv4.1.  

1. Ověřte nastavení domény systému nfs. Ujistěte se, že doména je nakonfigurovaná jako **`defaultv4iddomain.com`** výchozí doména Azure NetApp Files, **tj.**  

    > [!IMPORTANT]
    > Ujistěte se, že jste `/etc/idmapd.conf` nastavili doménu systému souborů NFS ve **`defaultv4iddomain.com`** virtuálním počítači tak, aby odpovídala výchozí konfiguraci domény v souborech Azure NetApp: . Pokud existuje neshoda mezi konfigurací domény v klientovi nfs (tj. v rámci virtuálního počítače) a serverem nfs, `nobody`tj.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Ověřit . Měl by být nastaven na **Y**. Chcete-li vytvořit `nfs4_disable_idmapping` adresářovou strukturu, kde je umístěn, spusťte příkaz připojení. Nebudete moci ručně vytvořit adresář pod /sys/modules, protože přístup je vyhrazen pro jádro / ovladače.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Další podrobnosti o `nfs4_disable_idmapping` tom, https://access.redhat.com/solutions/1749883jak změnit parametr, naleznete v tématu .

### <a name="create-pacemaker-cluster"></a>Vytvořit cluster kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na Red Hat Enterprise Linux u Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento (A)SCS server základní cluster kardiostimulátoru.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci sap netweaveru

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

    ```
    sudo vi /etc/hosts
    ```

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

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

1. **[1]** Vytvořte adresáře SAP ve svazku Soubory Azure NetApp.  
   Dočasně připojte svazek Souborů Azure NetApp na jednom z virtuálních počítačů a vytvořte adresáře SAP (cesty k souborům).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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
    ``` 

1. **[A]** Vytvoření sdílených adresářů

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Instalace klienta systému NFS a další požadavky

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Zkontrolujte verzi resource-agents-sap

   Ujistěte se, že verze nainstalovaného balíčku resource-agents-sap je alespoň 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Přidání položek připojení

   Při použití NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Při použití NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Ujistěte se, že odpovídá nfs verze protokolu svazků Azure NetApp soubory, při montáži svazků. Pokud jsou svazky souborů Azure NetApp vytvořeny jako svazky NFSv3, použijte odpovídající konfiguraci NFSv3. Pokud jsou svazky souborů Azure NetApp vytvořeny jako svazky NFSv4.1, podle pokynů zakažte mapování ID a ujistěte se, že používáte odpovídající konfiguraci NFSv4.1. V tomto příkladu byly svazky souborů Azure NetApp vytvořeny jako svazky NFSv3.  

   Připojení nových sdílených složek

   ```
   sudo mount -a  
   ```

1. **[A]** Konfigurace souboru SWAP

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Chcete-li změnu aktivovat, restartujte agenta.

   ```
   sudo service waagent restart
   ```

1. **[A]** Konfigurace RHEL

   Konfigurace rhel, jak je popsáno v poznámce SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/ERS

1. **[1]** Vytvoření virtuálního prostředku IP a sondy stavu pro instanci ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Instalace systému ASCS aplikace SAP NetWeaver  

   Nainstalujte SAP NetWeaver ASCS jako root na první uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro ASCS, například <b>anftstsapvh</b>, <b>192.168.14.9</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**QAS**/ASCS**00**, zkuste nastavit vlastníka a skupinu složky ASCS**00** a opakujte akci.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Vytvoření virtuálního IP prostředku a sondy stavu pro instanci ERS

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Instalace SAP NetWeaver ERS  

   Nainstalujte SAP NetWeaver ERS jako root na druhém uzlu pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endové konfigurace nástroje pro vyrovnávání zatížení pro ERS, například <b>anftstsapers</b>, <b>192.168.14.10</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>01</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**QAS**/ERS**01**, zkuste nastavit vlastníka a skupinu složky ERS**01** a opakujte akci.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Přizpůsobení profilů instancí ASCS/SCS a ERS

   * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * PROFIL ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Konfigurace keep alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím softwarového nástroje pro vyrovnávání zatížení. Systém vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém uzláčase. Chcete-li tomu zabránit, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS a změnit nastavení systému Linux. Další informace [načtete v poznámce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive byl již přidán v posledním kroku.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Aktualizace souboru /usr/sap/sapservices

   Chcete-li zabránit spuštění instancí skriptem sapinit spuštění, všechny instance spravované Pacemaker musí být komentoval ze souboru /usr/sap/sapservices. Nezakomentujte instanci SAP HANA, pokud bude použita s HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Vytvoření prostředků clusteru SAP

   Pokud používáte architekturu enqueue server 1 (ENSA1), definujte prostředky takto:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP zavedl podporu pro enqueue server 2, včetně replikace, od SAP NW 7.52. Počínaje platformou ABAP 1809 je ve výchozím nastavení nainstalován server 2 fronty. Viz SAP poznámka [2630416](https://launchpad.support.sap.com/#/notes/2630416) pro zařazení do fronty server 2 podporu.
   Pokud používáte architekturu enqueue server 2[(ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), nainstalujte agenta prostředků prostředku-agent-sap-4.1.1-12.el7.x86_64 nebo novější a definujte prostředky takto:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Pokud upgradujete ze starší verze a přepájíte na server fronty 2, přečtěte si poznámku SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Časové topoukázky ve výše uvedené konfiguraci jsou pouze příklady a může být nutné přizpůsobit konkrétní nastavení SAP. 

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Přidání pravidel brány firewall pro ASCS a ERS na obou uzlech Přidejte pravidla brány firewall pro ASCS a ERS na obou uzlech.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

   Některé databáze vyžadují, aby byla instalace instance databáze spuštěna na aplikačním serveru. Připravte virtuální počítače aplikačního serveru, aby je bylo možné v těchto případech používat.  

   Níže uvedené kroky předpokládají, že nainstalujete aplikační server na server odlišný od serverů ASCS/SCS a HANA. V opačném případě nejsou některé z následujících kroků (například konfigurace překladu názvů hostitele) potřeba.  

   Následující položky jsou předponou buď **[A]** - použitelné jak pro PAS a AAS, **[P]** - platí pouze pro PAS nebo **[S]** - použitelné pouze pro AAS.  

1. **[A]** Nastavení překladu názvů hostitele: Můžete buď použít server DNS, nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech:  

   ```
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do /etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Vytvoření adresáře sapmnt Vytvořte adresář sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Instalace klienta systému NFS a další požadavky  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Přidání položek připojení  
   Při použití NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Při použití NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Připojení nových sdílených složek

   ```
   sudo mount -a
   ```

1. **[P]** Vytvoření a připojení adresáře PAS  
   Při použití NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Při použití NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Vytvoření a připojení adresáře aas  
   Při použití NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Při použití NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Konfigurace souboru SWAP
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Chcete-li změnu aktivovat, restartujte agenta.

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalován na SAP HANA. Pro tuto instalaci můžete použít všechny podporované databáze. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v [tématu Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Spuštění instalace instance databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako kořen pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro databázi.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Chcete-li nainstalovat aplikační server SAP, postupujte takto.

1. Příprava aplikačního serveru

   Postupujte podle výše uvedených kroků v kapitole [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) a připravte si aplikační server.

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další aplikační server SAP NetWeaver.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte zabezpečené úložiště SAP HANA tak, aby ukazovalo na virtuální název nastavení systémové replikace SAP HANA.

   Spuštěním následujícího příkazu \<zobrazíte seznam položek jako sapsid>adm

   ```
   hdbuserstore List
   ```

   To by mělo vypsat všechny položky a mělo by vypadat podobně jako
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Výstup ukazuje, že IP adresa výchozí položky směřuje na virtuální počítač a ne na IP adresu nástroje pro vyrovnávání zatížení. Tuto položku je třeba změnit tak, aby přecóklála na virtuální název hostitele nástroje pro vyrovnávání zatížení. Ujistěte se, že používáte stejný port **(30313** ve výstupu výše) a název databáze **(QAS** ve výstupu výše)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

1. Ruční migrace instance ASCS

   Stav prostředku před zahájením testu:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Spusťte následující příkazy jako root pro migraci instance ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulovat selhání uzlu

   Stav prostředku před zahájením testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Spuštění následujícího příkazu jako kořenového adresáře v uzlu, ve kterém je spuštěna instance ASCS

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Stav po spuštění uzlu by měl vypadat takto.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   K vyčištění neúspěšných prostředků použijte následující příkaz.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Zastavte proces serveru zpráv

   Stav prostředku před zahájením testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Spusťte následující příkazy jako root k identifikaci procesu serveru zpráv a jeho usmrcení.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Pokud server zpráv zabijete pouze jednou, bude `sapstart`restartován společností . Pokud jej zabijete dostatečně často, Pacemaker nakonec přesune instanci ASCS do jiného uzlu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Proces serveru zastavování zařazení do fronty

   Stav prostředku před zahájením testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Spusťte následující příkazy jako root v uzlu, kde je spuštěna instance ASCS, aby se zničil server fronty.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Instance ASCS by měla okamžitě přepojit převzetí služeb při selhání do jiného uzlu. Instance ERS by také měla po spuštění instance ASCS převezměte služby při selhání. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Proces serveru replikace zařazení do fronty

   Stav prostředku před zahájením testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Spusťte následující příkaz jako root v uzlu, kde je spuštěna instance ERS, abyste zničili proces replikačního serveru fronty.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Pokud spustíte příkaz pouze `sapstart` jednou, restartuje proces. Pokud jej spustíte `sapstart` dostatečně často, proces nerestartujete a prostředek bude v zastaveném stavu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředku instance ERS.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Zabít proces sapstartsrv do fronty

   Stav prostředku před zahájením testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Spusťte následující příkazy jako root v uzlu, kde je spuštěnascs.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Sapstartsrv proces by měl být vždy restartován agentem prostředků Pacemaker jako součást monitorování. Stav zdroje po zkoušce:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Další kroky

* [Ha pro SAP NW na virtuálních počítačích Azure na RHEL pro sap aplikace multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
