---
title: Nastavit IBM Db2 HADR na Azure virtual machines (VM) v RHEL | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti IBM Db2 LUW na Azure virtual machines (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812126"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na Red Hat Enterprise Linux Server

IBM Db2 pro Linux, UNIX a Windows (LUW) v [vysokou dostupnost a obnovení (HADR) konfigurace](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) se skládá z jednoho uzlu, na kterém běží instance primární databáze a nejméně jeden uzel, na kterém běží instance sekundární databáze. Změny instance primární databází se replikují do sekundární databáze instance synchronně nebo asynchronně, v závislosti na vaší konfiguraci. 

Tento článek popisuje, jak nasadit a nakonfigurovat službu Azure virtual machines (VM), nainstalujte rozhraní framework clusteru a nainstalujte IBM Db2 LUW HADR konfigurací. 

Tento článek nepopisuje jak nainstalovat a nakonfigurovat IBM Db2 LUW HADR nebo SAP instalace softwaru. Abyste mohli provádět tyto úlohy, poskytujeme odkazy na SAP a IBM instalační příručky. Tento článek se zaměřuje na části, které jsou specifické pro prostředí Azure. 

Jsou podporované verze IBM Db2 10.5 a novější, jak je uvedeno v Poznámka SAP [1928533].

Před zahájením instalace, viz následující poznámky SAP a dokumentace:

| Poznámka SAP | Popis |
| --- | --- |
| [1928533] | Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure |
| [2015553] | SAP v Azure: Požadavky pro podporu |
| [2178632] | Klíč monitorování metrik pro SAP v Azure |
| [2191498] | SAP v Linuxu se službou Azure: Rozšířené monitorování |
| [2243692] | Linux v Azure (IaaS) virtuálního počítače: Problémy licence SAP |
| [2002167] | Red Hat Enterprise Linux 7.x: Instalace a Upgrade |
| [2694118] | Red Hat Enterprise Linux doplněk pro vysokou dostupnost v Azure |
| [1999351] | Řešení potíží s rozšířené monitorování Azure, které pro SAP |
| [2233094] | DB6: Aplikace SAP v Azure, které používají pro Linux, UNIX a Windows – Další informace o IBM Db2 |
| [1612105] | DB6: Nejčastější dotazy k Db2 s HADR |


| Dokumentace | 
| --- |
| [Wiki komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Obsahuje všechny požadované poznámky SAP pro Linux |
| [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide] Průvodce |
| [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux][deployment-guide] (Tento článek) |
| [Nasazení správy system(DBMS) pro SAP na platformě Linux databáze Azure Virtual Machines][dbms-guide] Průvodce |
| [Úloh SAP v Azure kontrolní seznam plánování a nasazení][azr-sap-plancheck] |
| [Přehled doplňků vysoké dostupnosti pro Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Správa doplňku vysoké dostupnosti][rhel-ha-admin] |
| [Odkaz na doplněk vysoké dostupnosti][rhel-ha-ref] |
| [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru][rhel-azr-supp]
| [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure][rhel-azr-inst]
| [Nasazení databázového systému IBM Db2 Azure Virtual Machines pro úlohy SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Zásady podpory pro RHEL clustery s vysokou dostupností – Správa IBM Db2 pro Linux, Unix a Windows v clusteru][rhel-db2-supp]



## <a name="overview"></a>Přehled
Abyste dosáhli vysoké dostupnosti, IBM Db2 LUW s HADR instalován alespoň dva virtuální počítače Azure, které jsou nasazené v [dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) nebo napříč [zóny dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Na následujících obrázcích zobrazit nastavení serveru databáze dva virtuální počítače Azure. Jak databázový server virtuální počítače Azure mají své vlastní úložiště připojené a jsou spuštěné. Jedna instance databáze v jednom z virtuálních počítačů Azure v HADR, má roli z primární instance. Všichni klienti jsou připojené k primární instance. Všechny změny v databázové transakce jsou trvalé místně do Db2 transakčního protokolu. Jako záznamy protokolu transakce jsou trvalé místně, záznamy jsou přeneseny prostřednictvím protokolu TCP/IP na instanci databáze na druhý server databáze, pohotovostní server nebo pohotovostní instancí. Pohotovostní instancí aktualizuje místní databázi Posunutí vpřed převedené transakce záznamy protokolu. Tímto způsobem že pohotovostní server je udržovat synchronizované s primárním serverem.

HADR je pouze funkce replikace. Nemá žádné detekce chyb a automatické zařízení převzetí nebo převzetí služeb při selhání. Převzetí nebo převod na pohotovostní server musí ručně zahájit správce databáze. K dosažení detekce chyb a služby automatického převzetí, můžete použít funkci clusteringu Linux Pacemaker. Pacemaker monitoruje instance serveru dvě databáze. Když primární databázovému serveru dojde k chybě, inicializuje Pacemaker *automatické* HADR převzetí pohotovostní server. Pacemaker také zajišťuje, že je virtuální IP adresa přiřazená novým primárním serverem.

![Přehled vysoké dostupnosti IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Pokud chcete mít SAP aplikační servery připojit k primární databázi, budete potřebovat název virtuálního hostitele a virtuální IP adresu. V případě selhání se připojí aplikační servery SAP do nové instance primární databáze. V prostředí Azure [nástroji Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) musí používat virtuální IP adresu způsobem, který je potřeba pro HADR IBM Db2. 

Na vám pomohou plně poznat jak IBM Db2 LUW HADR a Pacemaker zapadá do vysoce dostupného nastavení systému SAP, na následujícím obrázku se seznámíte s vysoce dostupné instalace systému SAP podle databáze IBM Db2. Tento článek se týká pouze IBM Db2, ale poskytuje odkazy na další články o tom, jak nastavit další součásti systému SAP.

![Přehled úplné prostředí pro vysokou dostupnost IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Přehled požadovaných kroků
Chcete-li nasadit konfiguraci služby IBM Db2, postupujte podle těchto kroků:

  + Plánování prostředí.
  + Nasazení virtuálních počítačů.
  + Aktualizace systému Linux RHEL a konfiguraci systémů souborů.
  + Nainstalujte a nakonfigurujte Pacemaker.
  + Instalační program [glusterfs clusteru][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Nainstalujte [ASCS/Lajících v jiném clusteru][ascs-ha-rhel].
  + Databáze IBM Db2 instalaci s možností distribuované/vysoká dostupnost (SWPM).
  + Instalace a vytvoření uzel sekundární databáze a instance a konfigurace HADR.
  + Potvrďte, že HADR funguje.
  + Použijte konfiguraci Pacemaker na ovládací prvek IBM Db2.
  + Konfigurace nástroje pro vyrovnávání zatížení Azure.
  + Instalace primární a dialogové okno aplikačních serverů.
  + Kontrola a adaptace konfigurace aplikační servery SAP.
  + Proveďte převzetí služeb při selhání a testy převzetí.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Plánování infrastruktury Azure pro hostování IBM Db2 LUW s HADR

Dokončení procesu plánování před spuštěním nasazení. Plánování sestavení základem pro nasazení konfigurace Db2 s HADR v Azure. V následující tabulce jsou uvedeny klíčové prvky, které musí být součástí plánování prozatímním Vojenským Db2 LUW (databáze součástí prostředí SAP):

| Téma | Krátký popis |
| --- | --- |
| Definování skupin prostředků Azure | Pokud nasadíte virtuální počítač, virtuální síť, nástroj pro vyrovnávání zatížení Azure a další prostředky skupiny prostředků. Může být existující nebo nové. |
| Virtuální síť / podsíť definice | Pokud se nasazení virtuálních počítačů pro IBM Db2 a nástroje pro vyrovnávání zatížení Azure. Může být existující nebo nově vytvořený. |
| Virtuální počítače hostující IBM Db2 LUW | Velikost virtuálního počítače, úložiště, sítě, IP adresu. |
| Název virtuálního hostitele a virtuální IP adresy pro databázi IBM Db2| Virtuální IP nebo název hostitele, který se používá pro připojení aplikační servery SAP. **DB-virt-název hostitele**, **db-virt-ip**. |
| Monitorování geografických zón Azure | Metoda, abyste vyloučili situace brain rozdělení je zabráněno. |
| Nástroj pro vyrovnávání zatížení Azure | Využití Basic nebo Standard (doporučeno), port pro databázi Db2 (v našem doporučení 62500) testu **portu sondy**. |
| Překlad adres| Jak funguje překlad v prostředí. Služba DNS je důrazně doporučujeme. Můžete použít soubor místních hostitelů. |
    
Další informace o Linux Pacemaker v Azure najdete v tématu [nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Nasazení v systému Red Hat Enterprise Linuxu

Prostředek agenta pro IBM Db2 LUW je součástí Red Hat Enterprise Linux Server HA Addon. Pro instalační program, který je popsaný v tomto dokumentu měli byste použít Red Hat Enterprise Linux pro SAP. Na webu Azure Marketplace obsahuje bitovou kopii pro Red Hat Enterprise Linux 7.4 pro SAP nebo vyšší, můžete použít k nasazení nových virtuálních počítačů Azure. Mějte na paměti různých vzorů podpory nebo služby, které by Red Hat v Tržišti Azure Marketplace nabízí při výběru image virtuálního počítače na virtuální počítač Azure Marketplace.

### <a name="hosts-dns-updates"></a>Hostitelé: Aktualizace služby DNS
Vytvořit seznam všech názvů hostitelů, včetně názvy virtuálních hostitelů a aktualizovat vaše servery DNS umožňuje správnou IP adresu k rozlišení názvu hostitele. Pokud DNS server neexistuje nebo nelze aktualizovat a vytvořit záznamy DNS, budete muset použít místního hostitele soubory jednotlivých virtuálních počítačů, které jsou součástí tohoto scénáře. Pokud používáte soubory záznamy hostitele, ujistěte se, že položky se použijí na všechny virtuální počítače v prostředí systému SAP. Doporučujeme však, že používáte službu DNS, který v ideálním případě by se rozšíří do Azure


### <a name="manual-deployment"></a>Ruční nasazení

Ujistěte se, že je pro IBM Db2 LUW IBM a SAP podporuje vybraný operační systém. Seznam podporovaných verzí operačního systému pro virtuální počítače Azure a Db2 verze je k dispozici v Poznámka SAP [1928533]. Seznam verzí operačního systému podle jednotlivých Db2 verze je k dispozici v matici Dostupnost produktů SAP. Důrazně doporučujeme alespoň Red Hat Enterprise Linux 7.4 pro SAP z důvodu vylepšení výkonu souvisejících s Azure v této nebo novější verze Red Hat Enterprise Linuxu.

1. Vytvořte nebo vyberte skupinu prostředků.
1. Vytvořte nebo vyberte virtuální síť a podsíť.
1. Vytvoření skupiny dostupnosti Azure nebo nasadit zónu dostupnosti.
    + Pro skupinu dostupnosti nastavte maximální aktualizačních domén na 2.
1. Vytvoření virtuálního počítače 1.
    + Použijte Red Hat Enterprise Linux pro SAP image v Tržišti Azure Marketplace.
    + Vyberte sadu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo zónu dostupnosti.
1.  Vytvoření virtuálního počítače 2.
    + Použijte Red Hat Enterprise Linux pro SAP image v Tržišti Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure ve vytvořené v kroku 3, nebo vyberte zóně dostupnosti (ne stejné zóně jako v kroku 3).
1. Použití datových disků k virtuálním počítačům a zkontrolujte doporučení nastavení systému souborů v článku [nasazení databázového systému IBM Db2 Azure Virtual Machines pro úlohy SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker
    
Chcete-li vytvořit základní Pacemaker clusteru pro tento server IBM Db2, naleznete v tématu [nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Nainstalujte prostředí IBM Db2 LUW a SAP

Před zahájením instalace prostředí SAP, IBM Db2 LUW podle, projděte si následující dokumentaci:

+ Dokumentace k Azure
+ Dokumentaci k SAPU
+ Dokumentace ke službě IBM

Odkazy na tato dokumentace jsou uvedeny v úvodní části tohoto článku.

Zkontrolujte příručky instalace SAP o instalaci aplikace založené na systému NetWeaver IBM Db2 LUW.
Vodítka na portálu pro SAP nápovědy můžete najít pomocí [SAP Instalační příručka Finder][sap-instfind].

Můžete snížit počet vodítka se zobrazí na portálu tak, že nastavíte následující filtry:
- Chci: "Instalace nového systému"
- Databáze: "IBM Db2 pro Windows, Linux a Unix"
- Další filtry pro operační systém, configuration zásobníku nebo verze SAP NetWeaver

#### <a name="red-hat-firewall-rules"></a>Pravidla brány firewall na Red Hat
Red Hat Enterprise Linux je brána firewall ve výchozím nastavení povolená. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Pomocné parametry instalace pro nastavení IBM Db2 LUW s HADR

Nastavení primární instance databáze IBM Db2 LUW:

- Pomocí možnosti distribuované nebo vysokou dostupnost.
- Nainstalujte instanci SAP ASCS/Lajících a databáze.
- Proveďte zálohu databáze nově nainstalovaný.

> [!IMPORTANT] 
> Zapište si "databáze komunikační port", který je nastaven při instalaci. Musí být stejné číslo portu pro obě instance databáze.
>![Definice SAP SWPM Port](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR nastavení pro Azure

   Při použití agenta monitorování geografických zón Azure Pacemaker nastavte následující parametry:

   - Doba trvání okna peer HADR (sekundy) (HADR_PEER_WINDOW) = 240  
   - Hodnota časového limitu HADR (HADR_TIMEOUT) = 45

Doporučujeme, abyste na základě počátečního testování převzetí služeb při selhání/převzetí předchozí parametry. Je povinné, test pro správné fungování převzetí služeb při selhání převzít s tímto nastavením parametru. Protože jednotlivých konfiguracích se může lišit, parametry mohou vyžadovat úpravy. 

> [!NOTE]
> Určené k IBM Db2 s konfigurací HADR s normální spuštění: Instance databáze sekundární nebo pohotovostní musí být zprovoznění před zahájením instance primární databáze.

   
> [!NOTE]
> Pro instalaci a konfiguraci, která je specifická pro Azure a Pacemaker: Během procesu instalace pomocí Správce zřizování softwaru SAP je explicitní dotaz týkající se vysoké dostupnosti pro IBM Db2 LUW:
>+ Nesmí být zvolen **IBM Db2 pureScale**.
>+ Nesmí být zvolen **nainstalovat IBM Tivoli systému automatizace pro Multiplatforms**.
>+ Nesmí být zvolen **generovat konfiguračních souborů clusteru**.
>![SAP SWPM – možnosti DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Pokud chcete nastavit server databáze úsporného režimu s použitím procedury kopírování homogenní systému SAP, proveďte tyto kroky:

1. Vyberte **kopie systému** možnost > **cílové systémy** > **distribuované** > **instance databáze**.
1. Jako metodu kopírování, vyberte **homogenní systému** tak, aby zálohování můžete použít k obnovení zálohy na instanci pohotovostní server.
1. Při dosažení ukončení kroku k obnovení databáze pro kopie homogenní systému ukončíte instalační program. Obnovte databázi ze zálohy primární hostitele. Všechny následné instalace fáze již byly provedeny na serveru primární databáze.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat pravidla brány firewall pro DB2 HADR
Přidání pravidla brány firewall pro povolení provozu do DB2 a mezi DB2 pro HADR pracovat:
+ Komunikační port databáze. Pokud používáte oddílů, příliš přidáte tyto porty.
+ Port HADR (hodnota parametru DB2 HADR_LOCAL_SVC)
+ Port testu Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Kontrola IBM Db2 HADR
Pro demonstrační účely a postupů popsaných v tomto článku, identifikátor SID je databáze **ID2**.

Po dokončení konfigurace HADR a je jeho stav v uzlech primárního a záložního partnera a připojení, proveďte následující kontroly:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfigurace Db2 Pacemaker

Pokud používáte Pacemaker pro automatické převzetí služeb při selhání v případě selhání uzlu, musíte nakonfigurovat Db2 instancí a Pacemaker odpovídajícím způsobem. Tato část popisuje tímto typem konfigurace.

Následující položky jsou s předponou buď:

- **[A]** : Platí pro všechny uzly
- **[1]** : Lze použít pouze na uzlu 1 
- **[2]** : Lze použít pouze na uzlu 2

**[A]**  Požadavky pro konfiguraci Pacemaker:
1. Vypněte oba servery databáze s uživatele db2\<sid > s db2stop.
1. Změňte prostředí pro db2\<sid > uživateli */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker konfigurace

**[1]**  Pacemaker IBM Db2 HADR specifické konfigurace:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Vytvořit IBM Db2 prostředky:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]**  Start IBM Db2 prostředky:
* Vložte Pacemaker z režimu údržby.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Ujistěte se, že stav clusteru je OK a zda jsou spuštěny všechny prostředky. Není důležité, který uzel prostředky jsou spuštěné na.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az-idb02]

Úplný seznam zdrojů:

 rsc_st_azure   (stonith:fence_azure_arm):      Začínáme az-idb01 model hlavní/podřízený se sada: Hlavních serverů Db2_HADR_ID2-master [Db2_HADR_ID2]: podřízené servery [az-idb01]: [az-idb02] skupina prostředků: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):       Started az-idb01 nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Začínáme az-idb01

Proces démon stav: corosync: pacemaker aktivní/zakázáno: pcsd aktivní/zakázáno: aktivní/povoleno
</pre>

> [!IMPORTANT]
> Je třeba spravovat Pacemaker clusterovaná instance Db2 pomocí Pacemaker nástrojů. Pokud používáte db2 příkazech, jako je db2stop, zjistí Pacemaker akce jako selhání prostředku. Při provádění údržby, můžete umístit na uzlech nebo prostředky v režimu údržby. Pacemaker pozastaví monitorování prostředků, a pak můžete použít příkazy pro správu normální db2.


### <a name="configure-azure-load-balancer"></a>Konfigurace služby Azure Load Balancer
Pokud chcete nakonfigurovat nástroj pro vyrovnávání zatížení Azure, doporučujeme použít [Azure standardní SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) a potom postupujte takto:

1. Vytvořte front-endový fond IP adres:

   a. Na webu Azure Portal, otevřete nástroj pro vyrovnávání zatížení Azure, vyberte **front-endový fond IP**a pak vyberte **přidat**.

   b. Zadejte název nové front-endového fondu IP adres (například **Db2 připojení**).

   c. Nastavte **přiřazení** k **statické**a zadejte IP adresu **virtuální IP** definované na začátku.

   d. Vyberte **OK**.

   e. Až se vytvoří nová front-endového fondu IP adres, zapište si IP adresu fondu.

1. Vytvořte fond back-end:

   a. Na webu Azure Portal, otevřete nástroj pro vyrovnávání zatížení Azure, vyberte **back-endové fondy**a pak vyberte **přidat**.

   b. Zadejte název nového back endového fondu (například **Db2 back-endu**).

   c. Vyberte **přidat virtuální počítač**.

   d. Vyberte skupinu dostupnosti nebo virtuální počítače hostující databázi IBM Db2, které jste vytvořili v předchozím kroku.

   e. Vyberte virtuální počítače clusteru IBM Db2.

   f. Vyberte **OK**.

1. Vytvořte sondu stavu:

   a. Na webu Azure Portal, otevřete nástroj pro vyrovnávání zatížení Azure, vyberte **sond stavu**a vyberte **přidat**.

   b. Zadejte název nového stavu testu (například **Db2 hp**).

   c. Vyberte **TCP** jako protokol a port **62500**. Zachovat **Interval** nastavena na hodnotu **5**a udržovat **prahová hodnota špatného stavu** nastavena na hodnotu **2**.

   d. Vyberte **OK**.

1. Vytvoření pravidel Vyrovnávání zatížení:

   a. Na webu Azure Portal, otevřete nástroj pro vyrovnávání zatížení Azure, vyberte **pravidla Vyrovnávání zatížení**a pak vyberte **přidat**.

   b. Zadejte název nové pravidlo nástroje pro vyrovnávání zatížení (například **Db2 SID**).

   c. Vyberte IP adresu front-endu, back endového fondu a sondu stavu, který jste vytvořili dříve (například **Db2 front-endu**).

   d. Zachovat **protokol** nastavena na **TCP**a zadejte port *databáze komunikační port*.

   e. Zvětšit **časový limit nečinnosti** až 30 minut.

   f. Ujistěte se, že k **povolte plovoucí IP adresy**.

   g. Vyberte **OK**.

**[A]**  Přidat pravidlo brány firewall pro port testu:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Proveďte změny v profilech SAP určený virtuální IP adresu pro připojení
Aplikační vrstva pro připojení k primární instance HADR konfiguraci systému SAP musí používat virtuální IP adresu, definice a nakonfigurovat nástroj pro vyrovnávání zatížení Azure. Vyžadují se následující změny:

/sapmnt/\<SID >/profil/výchozí. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID > /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalace primární a aplikační servery dialogového okna

Při instalaci primární a aplikační servery dialogového okna pro konfiguraci služby Db2 HADR, použijte virtuální hostitel název, který jste vybrali pro konfiguraci. 

Pokud jste provedli instalaci teprve vytvořili Db2 HADR konfiguraci, proveďte požadované změny, jak je popsáno v předchozí části a následujícím způsobem pro zásobníky Javy SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Zkontrolujte ABAP + Java nebo Java systémy stack JDBC URL

Pomocí nástroje Konfigurace J2EE zkontrolovat nebo aktualizovat adresu URL JDBC. Protože nástroj J2EE Config je grafický nástroj, je potřeba mít X nainstalovaný server:
 
1. Přihlaste se k primárním aplikačním serveru instance J2EE a spusťte:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. V levém podokně zvolte **úložiště zabezpečení**.
1. V rámci správné zvolte klíče jdbc/fond / \ <SAPSID> /URL.
1. Změňte název hostitele v adrese URL JDBC na název virtuálního hostitele.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Vyberte **přidat**.
1. Změny uložíte kliknutím na ikonu disku v levém horním rohu.
1. Ukončete nástroj pro konfiguraci.
1. Restartujte instanci Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurace protokolu archivace HADR instalace
Ke konfiguraci do Db2 protokolu archivace HADR nastavení, doporučujeme nakonfigurovat primární a pohotovostní databázi, aby měla možnost automatického protokolování načítání ze všech umístění archivu protokolu. Primární a záložní databáze musí být schopen načíst archivní soubory protokolu ze všech umístění archivu protokolu buď jeden databáze může být instancí archivaci souborů protokolů. 

Archivace protokolu se provádí pouze pomocí primární databáze. Pokud změníte HADR rolí serverů databáze nebo pokud dojde k selhání, nové primární databáze je zodpovědná za archivaci protokolu. Pokud nastavíte více umístění archivu protokolu, může vaše protokoly archivovat dvakrát. V případě místní nebo vzdálené zachytávání budete také muset ručně zkopírovat archivované protokoly z původního primárního serveru na umístění aktivního protokolu novým primárním serverem.

Doporučujeme, abyste konfiguraci běžné sdílené složky systému souborů NFS nebo GlusterFS, umístění, kam protokolů zapisován z obou uzlů. Sdílené složky systému souborů NFS nebo GlusterFS musí být vysoce dostupný. 

Můžete použít existující vysoce dostupné sdílené složky systému souborů NFS nebo GlusterFS pro přenosy nebo adresář profilu. Další informace naleznete v tématu:

- [GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver][glusterfs] 
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na Red Hat Enterprise Linux s Azure Files NetApp pro aplikace SAP][anf-rhel]
- [Služba soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (pro vytvoření sdílených složek NFS)

## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

Tato část popisuje, jak otestovat nastavení Db2 HADR. Každý test předpokládá IBM Db2 primární běží na *az idb01* virtuálního počítače. Uživatel s oprávněními sudo nebo kořenového adresáře (nedoporučuje se) musí být použita.

Počáteční stav u všech testovacích případů je zde vysvětleno: (crm_mon stav - r nebo počítače)

- **stav počítače** je snímek stavu Pacemaker při běhu 
- **crm_mon - r** se souvislým výstupem Pacemaker stavu

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Původní stav v systému SAP jsou uvedené v transakci DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – náhled migrace](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Test převzetí IBM Db2


> [!IMPORTANT] 
> Než spustíte test, ujistěte se, že:
> * Pacemaker nemá žádné nezdařené akce (stav počítače).
> * Neexistují žádná omezení umístění (zbývajícími testovací migrace)
> * IBM Db2 HADR synchronizace probíhá. Obraťte se na uživatele db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrace uzlu, na kterém běží primární databáze Db2 pomocí provádí následující příkaz:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Po dokončení migrace bude vypadat jako výstup stavu crm:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Původní stav v systému SAP jsou uvedené v transakci DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit - po migraci](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Migrace prostředků s "přesunutí prostředku počítače" vytvoří omezení umístění. Omezení umístění v tomto případě brání spuštěnou instanci IBM Db2 na az idb01. Pokud nebudou odstraněny omezení umístění, prostředek nelze navrácení služeb po obnovení.

Odebrat omezení umístění a pohotovostní uzel bude spuštěn na az idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
A stav clusteru změní na:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![Omezení umístění odebrané DBACockpit-](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrovat prostředek zpět *az idb01* a zrušte zaškrtnutí omezení umístění
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **Přesunutí prostředku počítačů \<res_name > <host>:** Vytvoří omezení umístění a může způsobit problémy s převzetí
- **Vymazat prostředků počítače \<res_name >** : Vymaže omezení umístění
- **vyčištění prostředků počítače \<res_name >** : Vymaže všechny chyby prostředku

### <a name="test-a-manual-takeover"></a>Ruční převzetí testování

Ruční převzetí můžete otestovat pomocí zastavení služby Pacemaker na *az idb01* uzlu:
<pre><code>systemctl stop pacemaker</code></pre>

Stav *az ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Po převzetí služeb při selhání, můžete spustit službu znovu na *az idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Ukončení procesu Db2 na uzlu, na kterém běží HADR primární databáze

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 instance se to nezdaří a Pacemaker se přesuňte hlavní uzel a sestava následující stav:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker restartuje instanci primární databázi Db2 na stejném uzlu, nebo ho bude převzetí služeb při selhání na uzlu, na kterém běží instance sekundární databáze a dojde k chybě.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Ukončení procesu Db2 na uzlu, na kterém běží instance sekundární databáze

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Uzel získá into se nezdařil, stanovených a nahlášena chyba
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

V sekundární roli, kterou měl přiřazenou před získá restartování instance Db2.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zastavit DB prostřednictvím db2stop platnost na uzlu, na kterém běží instance HADR primární databáze

Jako uživatel db2\<sid > spustit příkaz db2stop platnost:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Byl zjištěn výpadek:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Instance sekundární databáze Db2 HADR získali přenesli do primární role.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Virtuální počítač, na kterém běží instance primární databáze HADR s "zastavení" k chybě

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

V takovém případě Pacemaker zjistí, že uzel, na kterém běží instance primární databáze nereaguje.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

dalším krokem je kontrola *rozdělit brain* situaci. Po zbývající uzel bylo zjištěno, že uzel, který naposledy spustil instanci primární databáze je mimo provoz, je provést převzetí služeb při selhání prostředků.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


V případě poplachu jádra bude mít uzel restared pomocí agenta monitorování geografických zón. Po selhání uzlu je zpátky do online režimu, je nutné spustit podle pacemaker cluster
<pre><code>sudo pcs cluster start</code></pre> spuštění instance Db2 do sekundární roli.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Další postup
- [Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr]
