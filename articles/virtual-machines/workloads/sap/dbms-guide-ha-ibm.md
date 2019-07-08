---
title: Nastavit IBM Db2 HADR na Azure virtual machines (VMs) | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti IBM Db2 LUW na Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 7464ea481d4c95856b78a83a875f2cd24c00705b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503331"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Pacemaker

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
| [1984787] | SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
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
| [SUSE Linux Enterprise Server pro SAP aplikace 12 SP3 osvědčené postupy vodítka][sles-for-sap-bp] |
| [SUSE Linux Enterprise vysokou dostupnost rozšíření 12 SP3][sles-ha-guide] |
| [Nasazení databázového systému IBM Db2 Azure Virtual Machines pro úlohy SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Přehled
Abyste dosáhli vysoké dostupnosti, IBM Db2 LUW s HADR instalován alespoň dva virtuální počítače Azure, které jsou nasazené v [dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) nebo napříč [zóny dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Na následujících obrázcích zobrazit nastavení serveru databáze dva virtuální počítače Azure. Jak databázový server virtuální počítače Azure mají své vlastní úložiště připojené a jsou spuštěné. Jedna instance databáze v jednom z virtuálních počítačů Azure v HADR, má roli z primární instance. Všichni klienti připojeni k této primární instance. Všechny změny v databázové transakce jsou trvalé místně do Db2 transakčního protokolu. Jako záznamy protokolu transakce jsou trvalé místně, záznamy jsou přeneseny prostřednictvím protokolu TCP/IP na instanci databáze na druhý server databáze, pohotovostní server nebo pohotovostní instancí. Pohotovostní instancí aktualizuje místní databázi Posunutí vpřed převedené transakce záznamy protokolu. Tímto způsobem že pohotovostní server je udržovat synchronizované s primárním serverem.

HADR je pouze funkce replikace. Nemá žádné detekce chyb a automatické zařízení převzetí nebo převzetí služeb při selhání. Převzetí nebo převod na pohotovostní server musí ručně zahájit správce databáze. K dosažení detekce chyb a služby automatického převzetí, můžete použít funkci clusteringu Linux Pacemaker. Pacemaker monitoruje instance serveru dvě databáze. Když primární databázovému serveru dojde k chybě, inicializuje Pacemaker *automatické* HADR převzetí pohotovostní server. Pacemaker také zajišťuje, že je virtuální IP adresa přiřazená novým primárním serverem.

![Přehled vysoké dostupnosti IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Pokud chcete mít SAP aplikační servery připojit k primární databázi, budete potřebovat název virtuálního hostitele a virtuální IP adresu. V případě selhání se připojí aplikační servery SAP do nové instance primární databáze. V prostředí Azure [nástroji Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) musí používat virtuální IP adresu způsobem, který je potřeba pro HADR IBM Db2. 

Na vám pomohou plně poznat jak IBM Db2 LUW HADR a Pacemaker zapadá do vysoce dostupného nastavení systému SAP, na následujícím obrázku se seznámíte s vysoce dostupné instalace systému SAP podle databáze IBM Db2. Tento článek se týká pouze IBM Db2, ale poskytuje odkazy na další články o tom, jak nastavit další součásti systému SAP.

![Přehled úplné prostředí pro vysokou dostupnost IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Přehled požadovaných kroků
Chcete-li nasadit konfiguraci služby IBM Db2, postupujte podle těchto kroků:

  + Plánování prostředí.
  + Nasazení virtuálních počítačů.
  + Aktualizace operačního systému SUSE Linux a konfiguraci systémů souborů.
  + Nainstalujte a nakonfigurujte Pacemaker.
  + Nainstalujte [s vysokou dostupností systému souborů NFS][nfs-ha].
  + Nainstalujte [ASCS/Lajících v jiném clusteru][ascs-ha].
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
| Monitorování geografických zón Azure | Azure monitorování geografických zón nebo monitorování geografických zón SBD (doporučeno). Metoda, abyste vyloučili situace brain rozdělení je zabráněno. |
| SBD VIRTUÁLNÍHO POČÍTAČE | Velikost virtuálního počítače SBD, úložiště, sítě. |
| Nástroj pro vyrovnávání zatížení Azure | Využití Basic nebo Standard (doporučeno), port pro databázi Db2 (v našem doporučení 62500) testu **portu sondy**. |
| Překlad adres| Jak funguje překlad v prostředí. Služba DNS je důrazně doporučujeme. Můžete použít soubor místních hostitelů. |
    
Další informace o Linux Pacemaker v Azure najdete v tématu [Pacemaker na SUSE Linux Enterprise Server v Azure nastavit](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Nasazení v SUSE Linuxu

Prostředek agenta pro IBM Db2 LUW je součástí operačního systému SUSE Linux Enterprise Server pro aplikace SAP. Pro instalační program, který je popsaný v tomto dokumentu musíte použít operačním systémem SUSE Linux Server pro aplikace SAP. Na webu Azure Marketplace obsahuje bitovou kopii pro Server systému SUSE Enterprise pro SAP aplikace 12, který můžete použít k nasazení nových virtuálních počítačů Azure. Mějte na paměti různých vzorů podpory nebo služby, které při výběru image virtuálního počítače na virtuální počítač Azure Marketplace nabízí SUSE prostřednictvím Azure Marketplace. 

### <a name="hosts-dns-updates"></a>Hostitelé: Aktualizace služby DNS
Vytvořit seznam všech názvů hostitelů, včetně názvy virtuálních hostitelů a aktualizovat vaše servery DNS umožňuje správnou IP adresu k rozlišení názvu hostitele. Pokud DNS server neexistuje nebo nelze aktualizovat a vytvořit záznamy DNS, budete muset použít místního hostitele soubory jednotlivých virtuálních počítačů, které jsou součástí tohoto scénáře. Pokud používáte soubory záznamy hostitele, ujistěte se, že položky se použijí na všechny virtuální počítače v prostředí systému SAP. Doporučujeme však, že používáte službu DNS, který v ideálním případě by se rozšíří do Azure


### <a name="manual-deployment"></a>Ruční nasazení

Ujistěte se, že je pro IBM Db2 LUW IBM a SAP podporuje vybraný operační systém. Seznam podporovaných verzí operačního systému pro virtuální počítače Azure a Db2 verze je k dispozici v Poznámka SAP [1928533]. Seznam verzí operačního systému podle jednotlivých Db2 verze je k dispozici v matici Dostupnost produktů SAP. Důrazně doporučujeme minimální SLES 12 SP3 z důvodu vylepšení výkonu souvisejících s Azure v této nebo novější verze operačního systému SUSE Linux.

1. Vytvořte nebo vyberte skupinu prostředků.
1. Vytvořte nebo vyberte virtuální síť a podsíť.
1. Vytvoření skupiny dostupnosti Azure nebo nasadit zónu dostupnosti.
    + Pro skupinu dostupnosti nastavte maximální aktualizačních domén na 2.
1. Vytvoření virtuálního počítače 1.
    + Použijte SLES pro SAP image v Tržišti Azure Marketplace.
    + Vyberte sadu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo zónu dostupnosti.
1.  Vytvoření virtuálního počítače 2.
    + Použijte SLES pro SAP image v Tržišti Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure ve vytvořené v kroku 3, nebo vyberte zóně dostupnosti (ne stejné zóně jako v kroku 3).
1. Použití datových disků k virtuálním počítačům a zkontrolujte doporučení nastavení systému souborů v článku [nasazení databázového systému IBM Db2 Azure Virtual Machines pro úlohy SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker
    
Chcete-li vytvořit základní Pacemaker clusteru pro tento server IBM Db2, naleznete v tématu [Pacemaker na SUSE Linux Enterprise Server v Azure nastavit][sles-pacemaker]. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Pomocné parametry instalace pro nastavení IBM Db2 LUW s HADR

Nastavení primární instance databáze IBM Db2 LUW:

- Pomocí možnosti distribuované nebo vysokou dostupnost.
- Nainstalujte instanci SAP ASCS/Lajících a databáze.
- Proveďte zálohu databáze nově nainstalovaný.


> [!IMPORTANT] 
> Zapište si "databáze komunikační port", který je nastaven při instalaci. Musí být stejné číslo portu pro obě instance databáze

Pokud chcete nastavit server databáze úsporného režimu s použitím procedury kopírování homogenní systému SAP, proveďte tyto kroky:

1. Vyberte **kopie systému** možnost > **cílové systémy** > **distribuované** > **instance databáze**.
1. Jako metodu kopírování, vyberte **homogenní systému** tak, aby zálohování můžete použít k obnovení zálohy na instanci pohotovostní server.
1. Při dosažení ukončení kroku k obnovení databáze pro kopie homogenní systému ukončíte instalační program. Obnovte databázi ze zálohy primární hostitele. Všechny následné instalace fáze již byly provedeny na serveru primární databáze.
1. Nastavení HADR pro IBM Db2.

   > [!NOTE]
   > Pro instalaci a konfiguraci, která je specifická pro Azure a Pacemaker: Během procesu instalace pomocí Správce zřizování softwaru SAP je explicitní dotaz týkající se vysoké dostupnosti pro IBM Db2 LUW:
   >+ Nesmí být zvolen **IBM Db2 pureScale**.
   >+ Nesmí být zvolen **nainstalovat IBM Tivoli systému automatizace pro Multiplatforms**.
   >+ Nesmí být zvolen **generovat konfiguračních souborů clusteru**.

   Při použití zařízení s SBD pro Linux Pacemaker nastavte následující parametry Db2 HADR:
   + Doba trvání okna peer HADR (sekundy) (HADR_PEER_WINDOW) = 300  
   + Hodnota časového limitu HADR (HADR_TIMEOUT) = 60

   Při použití agenta monitorování geografických zón Azure Pacemaker nastavte následující parametry:
   + Doba trvání okna peer HADR (sekundy) (HADR_PEER_WINDOW) = 900  
   + Hodnota časového limitu HADR (HADR_TIMEOUT) = 60

Doporučujeme, abyste na základě počátečního testování převzetí služeb při selhání/převzetí předchozí parametry. Je povinné, test pro správné fungování převzetí služeb při selhání převzít s tímto nastavením parametru. Protože jednotlivých konfiguracích se může lišit, parametry mohou vyžadovat úpravy. 

> [!IMPORTANT]
> Určené k IBM Db2 s konfigurací HADR s normální spuštění: Instance databáze sekundární nebo pohotovostní musí být zprovoznění před zahájením instance primární databáze.

Pro demonstrační účely a postupů popsaných v tomto článku, identifikátor SID je databáze **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Kontrola IBM Db2 HADR
Po dokončení konfigurace HADR a je jeho stav v uzlech primárního a záložního partnera a připojení, proveďte následující kontroly:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfigurace Db2 Pacemaker

Pokud používáte Pacemaker pro automatické převzetí služeb při selhání v případě selhání uzlu, musíte nakonfigurovat Db2 instancí a Pacemaker odpovídajícím způsobem. Tato část popisuje tímto typem konfigurace.

Následující položky jsou s předponou buď:

- **[A]** : Platí pro všechny uzly
- **[1]** : Lze použít pouze na uzlu 1 
- **[2]** : Lze použít pouze na uzlu 2

**[A]**  Požadavky pro konfiguraci Pacemaker:
1. Vypněte oba servery databáze s uživatele db2\<sid > s db2stop.
1. Změňte prostředí pro db2\<sid > uživateli */bin/ksh*. Doporučujeme vám, že použijete nástroj Yast. 


### <a name="pacemaker-configuration"></a>Pacemaker konfigurace

**[1]**  Pacemaker IBM Db2 HADR specifické konfigurace:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Vytvořit IBM Db2 prostředky:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Start IBM Db2 prostředky:
* Vložte Pacemaker z režimu údržby.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Ujistěte se, že stav clusteru je OK a zda jsou spuštěny všechny prostředky. Není důležité, který uzel prostředky jsou spuštěné na.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 uzly konfigurované
# <a name="5-resources-configured"></a>5 prostředků, které jsou nakonfigurované

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Úplný seznam zdrojů:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>využitím techniky stonith sbd (stonith:external / sbd): Začínáme azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Skupina prostředků: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Začínáme azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: NIC):      Začínáme azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Model hlavní/podřízený se sada: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Hlavní servery: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Podřízené servery: [azibmdb01]
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
 
1. Přihlaste se k primárním aplikačním serveru instance J2EE a spusťte:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. V levém podokně zvolte **úložiště zabezpečení**.
1. V rámci správné zvolte klíčejdbc/fond/\<SAPSID > / adresa url.
1. Změňte název hostitele v adrese URL JDBC na název virtuálního hostitele.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Vyberte **Přidat**.
1. Změny uložíte kliknutím na ikonu disku v levém horním rohu.
1. Ukončete nástroj pro konfiguraci.
1. Restartujte instanci Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurace protokolu archivace HADR instalace
Ke konfiguraci do Db2 protokolu archivace HADR nastavení, doporučujeme nakonfigurovat primární a pohotovostní databázi, aby měla možnost automatického protokolování načítání ze všech umístění archivu protokolu. Primární a záložní databáze musí být schopen načíst archivní soubory protokolu ze všech umístění archivu protokolu buď jeden databáze může být instancí archivaci souborů protokolů. 

Archivace protokolu se provádí pouze pomocí primární databáze. Pokud změníte HADR rolí serverů databáze nebo pokud dojde k selhání, nové primární databáze je zodpovědná za archivaci protokolu. Pokud nastavíte více umístění archivu protokolu, může vaše protokoly archivovat dvakrát. V případě místní nebo vzdálené zachytávání budete také muset ručně zkopírovat archivované protokoly z původního primárního serveru na umístění aktivního protokolu novým primárním serverem.

Doporučujeme, abyste konfiguraci běžné sdílené složky NFS umístění, kam protokolů zapisován z obou uzlů. Sdílené složky NFS musí být vysoce dostupný. 

Existující vysoce dostupné sdílené složky NFS můžete použít pro přenosy nebo adresář profilu. Další informace naleznete v tématu:

- [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha] 
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise serveru s Azure Files NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Služba soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (pro vytvoření sdílených složek NFS)


## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

Tato část popisuje, jak otestovat nastavení Db2 HADR. *Každý test se předpokládá, že jste přihlášeni jako uživatel root* a IBM Db2 primární běží na *azibmdb01* virtuálního počítače.

Počáteční stav u všech testovacích případů je zde vysvětleno: (crm_mon stav - r nebo crm)

- **Stav CRM** je snímek stavu Pacemaker při běhu 
- **crm_mon - r** se souvislým výstupem Pacemaker stavu

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Původní stav v systému SAP jsou uvedené v transakci DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – náhled migrace](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Test převzetí IBM Db2


> [!IMPORTANT] 
> Než spustíte test, ujistěte se, že:
> * Pacemaker nemá žádné nezdařené akce (stav crm).
> * Neexistují žádná omezení umístění (zbývajícími testovací migrace)
> * IBM Db2 HADR synchronizace probíhá. Obraťte se na uživatele db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrace uzlu, na kterém běží primární databáze Db2 pomocí provádí následující příkaz:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po dokončení migrace bude vypadat jako výstup stavu crm:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Původní stav v systému SAP jsou uvedené v transakci DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit - po migraci](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migrace prostředků s "migrace prostředku crm" vytvoří omezení umístění. Omezení umístění je potřeba odstranit. Pokud nebudou odstraněny omezení umístění, prostředek nelze navrácení služeb po obnovení nebo zaznamenáte nežádoucí převzetí. 

Migrovat prostředek zpět *azibmdb01* a zrušte zaškrtnutí omezení umístění
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migrovat prostředek CRM \<res_name > <host>:** Vytvoří omezení umístění a může způsobit problémy s převzetí
- **Vymazat CRM prostředků \<res_name >** : Vymaže omezení umístění
- **vyčištění prostředků CRM \<res_name >** : Vymaže všechny chyby prostředku

### <a name="test-the-fencing-agent"></a>Testovací agent monitorování geografických zón

V takovém případě testujeme SBD upřednostňuje, který doporučujeme, abyste udělali při použití operačního systému SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Uzel clusteru *azibmdb01* by měl být restartován. Primární role HADR IBM Db2 bude přesunuta do *azibmdb02*. Když *azibmdb01* je zpět do režimu online, Db2, instance bude pro přesun role instance sekundární databáze. 

Pokud službu Pacemaker na primární restartovaná bývalé nespustí automaticky, je potřeba spustit ručně pomocí:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Ruční převzetí testování

Ruční převzetí můžete otestovat pomocí zastavení služby Pacemaker na *azibmdb01* uzlu:
<pre><code>service pacemaker stop</code></pre>

Stav *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Po převzetí služeb při selhání, můžete spustit službu znovu na *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Ukončení procesu Db2 na uzlu, na kterém běží HADR primární databáze

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 instance se to nezdaří a Pacemaker oznámí následující stav:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker restartuje instanci primární databázi Db2 na stejném uzlu, nebo ho bude převzetí služeb při selhání na uzlu, na kterém běží instance sekundární databáze a dojde k chybě.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Ukončení procesu Db2 na uzlu, na kterém běží instance sekundární databáze

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Uzel získá into se nezdařil, stanovených a nahlášena chyba
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

V sekundární roli, kterou měl přiřazenou před získá restartování instance Db2.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zastavit DB prostřednictvím db2stop platnost na uzlu, na kterém běží instance HADR primární databáze

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Jako uživatel db2\<sid > spustit příkaz db2stop platnost:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Byl zjištěn výpadek
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Instance sekundární databáze Db2 HADR získali přenesli do primární role
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Selhání virtuálního počítače s restartem na uzlu, na kterém běží instance HADR primární databáze

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker budou podporovat sekundární instance do primární instance role. Původní primární instanci se přesune do sekundární roli za virtuální počítač a všechny služby jsou plně obnoveny po restartování virtuálního počítače:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Virtuální počítač, na kterém běží instance primární databáze HADR s "zastavení" k chybě

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

V takovém případě Pacemaker zjistí, že uzel, na kterém běží instance primární databáze nereaguje.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

dalším krokem je kontrola *rozdělit brain* situaci. Po zbývající uzel bylo zjištěno, že uzel, který naposledy spustil instanci primární databáze je mimo provoz, je provést převzetí služeb při selhání prostředků.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Uzel v případě "zastavení" uzlu, musí se restartovat pomocí nástroje pro správu Azure (v na webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure CLI). Po selhání uzlu je zpátky do online režimu, se spustí Db2 instance do sekundární roli.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Další postup
- [Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Nastavit Pacemaker na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

