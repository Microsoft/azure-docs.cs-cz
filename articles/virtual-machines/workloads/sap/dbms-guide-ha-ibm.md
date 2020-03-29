---
title: Nastavení IBM Db2 HADR na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Nastolit vysokou dostupnost IBM Db2 LUW na virtuálních počítačích Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926750"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s kardiostimulátorem

IBM Db2 pro Linux, UNIX a Windows (LUW) v [konfiguraci vysoké dostupnosti a zotavení po havárii (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) se skládá z jednoho uzlu, který spouští primární databázovou instanci a alespoň jeden uzel, který spouští instanci sekundární databáze. Změny instance primární databáze jsou replikovány do instance sekundární databáze synchronně nebo asynchronně, v závislosti na konfiguraci. 

Tento článek popisuje, jak nasadit a nakonfigurovat virtuální počítače Azure (VM), nainstalovat architekturu clusteru a nainstalovat IBM Db2 LUW s konfigurací HADR. 

Tento článek se nezabývá instalací a konfigurací IBM Db2 LUW pomocí instalace softwaru HADR nebo SAP. Abychom vám pomohli tyto úkoly splnit, poskytujeme odkazy na instalační příručky SAP a IBM. Tento článek se zaměřuje na části, které jsou specifické pro prostředí Azure. 

Podporované verze IBM Db2 jsou 10.5 a novější, jak je popsáno v poznámce SAP [1928533].

Než začnete s instalací, přečtěte si následující poznámky a dokumentaci sap:

| Poznámka SAP | Popis |
| --- | --- |
| [1928533] | Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure |
| [2015553] | SAP v Azure: Požadavky podpory |
| [2178632] | Klíčové metriky monitorování pro SAP v Azure |
| [2191498] | SAP na Linuxu s Azure: Vylepšené monitorování |
| [2243692] | Virtuální počítač Linux u Azure (IaaS): Problémy s licencí SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [1999351] | Poradce při potížích s rozšířeným monitorováním Azure pro SAP |
| [2233094] | DB6: SAP aplikace v Azure, které používají IBM Db2 pro Linux, UNIX a Windows – další informace |
| [1612105] | DB6: Nejčastější dotazy na Db2 s HADR |


| Dokumentace | 
| --- |
| [SAP Společenství Wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Má všechny požadované SAP Poznámky pro Linux |
| [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide] |
| [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide] (tento článek) |
| [Nasazení systému správy databáze virtuálních počítačů Azure (DBMS) pro SAP na Linuxu][dbms-guide] |
| [Úlohy SAP v kontrolním seznamu plánování a nasazení Azure][azr-sap-plancheck] |
| [Příručky osvědčených postupů SUSE Linux Enterprise Server pro SAP Aplikace 12 SP4][sles-for-sap-bp] |
| [SUSE Linux Enterprise Rozšíření o vysokou dostupnost 12 SP4][sles-ha-guide] |
| [Nasazení DB2 Virtuálnípočítače IBM Db2 DBMS pro úlohy SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Přehled
Pro dosažení vysoké dostupnosti se IBM Db2 LUW with HADR namontuje alespoň na dva virtuální počítače Azure, které se nasazují v [sadě dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) nebo napříč [zónami dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Následující grafika zobrazuje nastavení dvou virtuálních počítačů Azure databázového serveru. Oba databázové servery Azure virtuální počítače mají své vlastní úložiště připojené a jsou v provozu. V HADR, jedna instance databáze v jednom z virtuálních počítačů Azure má roli primární instance. Všichni klienti jsou připojeni k této primární instanci. Všechny změny v databázových transakcích jsou místně trvalé v transakčním protokolu Db2. Jako záznamy protokolu transakcí jsou uloženy místně, záznamy jsou přeneseny prostřednictvím protokolu TCP/IP do instance databáze na druhém databázovém serveru, pohotovostní server nebo pohotovostní instance. Instance v pohotovostním režimu aktualizuje místní databázi posunutím předávání záznamů přeneseného transakčního protokolu. Tímto způsobem je pohotovostní server synchronizován s primárním serverem.

HADR je pouze funkce replikace. Nemá detekci selhání a žádné automatické převzetí nebo převzetí služeb při selhání zařízení. Převzetí nebo přenos na pohotovostní server musí být iniciován ručně správcem databáze. Chcete-li dosáhnout automatického převzetí a detekce selhání, můžete použít funkci clusteringu Linux Pacemaker. Kardiostimulátor monitoruje dvě instance databázového serveru. Když dojde k chybě instance primárního databázového serveru, kardiostimulátor zahájí *automatické* převzetí HADR pohotovostním serverem. Kardiostimulátor také zajišťuje, že virtuální IP adresa je přiřazena k novému primárnímu serveru.

![Přehled vysoké dostupnosti IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Chcete-li, aby se aplikační servery SAP připojouovaly k primární databázi, potřebujete název virtuálního hostitele a virtuální IP adresu. V případě převzetí služeb při selhání se aplikační servery SAP připojí k nové instanci primární databáze. V prostředí Azure je nástroj [pro vyrovnávání zatížení Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) vyžadován k použití virtuální IP adresy způsobem, který je vyžadován pro HADR IBM Db2. 

Abychom vám pomohli plně pochopit, jak IBM Db2 LUW s HADR a Pacemaker zapadá do vysoce dostupného nastavení systému SAP, následující obrázek představuje přehled vysoce dostupného nastavení systému SAP založeného na databázi IBM Db2. Tento článek se vztahuje pouze na IBM Db2, ale obsahuje odkazy na další články o tom, jak nastavit další součásti systému SAP.

![Kompletní přehled prostředí IBM DB2 s vysokou dostupností](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Přehled na vysoké úrovni o požadovaných krocích
Chcete-li nasadit konfiguraci IBM Db2, je třeba provést následující kroky:

  + Naplánujte si prostředí.
  + Nasaďte virtuální chod.
  + Aktualizujte SUSE Linux a nakonfigurujte souborové systémy.
  + Nainstalujte a nakonfigurujte kardiostimulátor.
  + Nainstalujte [vysoce dostupné nfs][nfs-ha].
  + Nainstalujte [ASCS/ERS do samostatného clusteru][ascs-ha].
  + Nainstalujte databázi IBM Db2 s možností Distribuované/Vysoké dostupnosti (SWPM).
  + Nainstalujte a vytvořte sekundární databázový uzel a instanci a nakonfigurujte HADR.
  + Potvrďte, že HADR funguje.
  + Použijte konfiguraci kardiostimulátoru k ovládání IBM Db2.
  + Konfigurace Azure Balancer.
  + Nainstalujte primární a dialogové aplikační servery.
  + Zkontrolujte a přizpůsobte konfiguraci aplikačních serverů SAP.
  + Proveďte testy převzetí služeb při selhání a převzetí.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Plánování infrastruktury Azure pro hostování IBM Db2 LUW s HADR

Před spuštěním nasazení dokončete proces plánování. Plánování vytváří základ pro nasazení konfigurace Db2 s HADR v Azure. Klíčové prvky, které musí být součástí plánování pro IMB Db2 LUW (databázová část prostředí SAP), jsou uvedeny v následující tabulce:

| Téma | Stručný popis |
| --- | --- |
| Definování skupin prostředků Azure | Skupiny prostředků, kde nasazujete virtuální počítač, virtuální síť, Azure Load Balancer a další prostředky. Může být existující nebo nové. |
| Definice virtuální sítě / podsítě | Kde se nasazují virtuální počítače pro IBM Db2 a Azure Load Balancer. Může být existující nebo nově vytvořené. |
| Virtuální počítače hostující IBM Db2 LUW | Velikost virtuálního počítače, úložiště, sítě, IP adresa. |
| Název virtuálního hostitele a virtuální IP adresa pro databázi IBM Db2| Název virtuální IP adresy nebo hostitele, který se používá pro připojení aplikačních serverů SAP. **db-virt-hostname**, **db-virt-ip**. |
| Oplocení azure | Oplocení azure nebo oplocení SBD (vysoce doporučeno). Metoda, jak se vyhnout rozděleným mozkovým situacím. |
| Virtuální měna SBD | SBD velikost virtuálního počítače, úložiště, síť. |
| Azure Load Balancer | Použití základní nebo standardní (doporučeno), sonda port pro db2 databáze (naše doporučení 62500) **sonda-port**. |
| Překlad adres| Jak funguje překlad názvů v prostředí. Služba DNS je vysoce doporučeno. Lze použít soubor místních hostitelů. |
    
Další informace o Linux pacemakeru v Azure najdete v tématu [Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Nasazení na SUSE Linuxu

Agent prostředků pro IBM Db2 LUW je součástí SUSE Linux Enterprise Server pro aplikace SAP. Pro nastavení, které je popsáno v tomto dokumentu, musíte použít SUSE Linux Server pro aplikace SAP. Azure Marketplace obsahuje image pro SUSE Enterprise Server pro sap aplikace 12, kterou můžete použít k nasazení nových virtuálních počítačů Azure. Mějte na paměti různé modely podpory nebo služeb, které nabízí SUSE prostřednictvím Azure Marketplace, když si vyberete image virtuálního počítače na webu Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hostitelé: Aktualizace DNS
Vytvořte seznam všech názvů hostitelů, včetně názvů virtuálních hostitelů, a aktualizujte servery DNS, aby bylo možné povolit překlad správné adresy IP pro překlad názvů hostitelů. Pokud server DNS neexistuje nebo nemůžete aktualizovat a vytvářet položky DNS, musíte použít místní hostitelské soubory jednotlivých virtuálních počítače, které se účastní tohoto scénáře. Pokud používáte položky souborů hostitele, ujistěte se, že položky jsou použity pro všechny virtuální uživatele v systémovém prostředí SAP. Doporučujeme však použít dns, který v ideálním případě zasahuje do Azure


### <a name="manual-deployment"></a>Ruční nasazení

Ujistěte se, že vybraný operační systém je podporován společností IBM/SAP pro IBM Db2 LUW. Seznam podporovaných verzí operačního systému pro virtuální počítače Azure a verze Db2 je k dispozici v poznámce SAP [1928533]. Seznam verzí operačního systému podle jednotlivých verzí Db2 je k dispozici v matici dostupnosti produktu SAP. Důrazně doporučujeme minimálně SLES 12 SP4 z důvodu vylepšení výkonu související s Azure v této nebo novější verze SUSE Linuxu.

1. Vytvořte nebo vyberte skupinu prostředků.
1. Vytvořte nebo vyberte virtuální síť a podsíť.
1. Vytvořte sadu dostupnosti Azure nebo nasaďte zónu dostupnosti.
    + Pro sadu dostupnosti nastavte maximální aktualizační domény na 2.
1. Vytvořit virtuální počítač 1.
    + Použijte SLES pro image SAP na Azure Marketplace.
    + Vyberte sadu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte Zóna dostupnosti.
1.  Vytvořit virtuální počítač 2.
    + Použijte SLES pro image SAP na Azure Marketplace.
    + Vyberte sadu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte zónu dostupnosti (ne stejnou zónu jako v kroku 3).
1. Přidejte datové disky do virtuálních počítačů a zkontrolujte doporučení nastavení systému souborů v článku [nasazení DB2 Azure Virtual Machines DBMS pro úlohy SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker
    
Pokud chcete vytvořit základní cluster Pacemakeru pro tento server IBM Db2, přečtěte si téma [Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalace prostředí IBM Db2 LUW a SAP

Než začnete s instalací prostředí SAP založeného na IBM Db2 LUW, přečtěte si následující dokumentaci:

+ Dokumentace azure
+ Dokumentace SAP
+ Dokumentace IBM

Odkazy na tuto dokumentaci jsou uvedeny v úvodní části tohoto článku.

Přečtěte si instalační příručky SAP o instalaci aplikací založených na aplikaci NetWeaver na IBM Db2 LUW.

Návody najdete na portálu nápovědy SAP pomocí [vyhledávače instalačních příruček SAP][sap-instfind].

Počet vodítek zobrazených na portálu můžete snížit nastavením následujících filtrů:

- Chci: "Nainstalujte nový systém"
- Moje databáze: "IBM Db2 pro Linux, Unix a Windows"
- Další filtry pro verze SAP NetWeaver, konfiguraci zásobníku nebo operační systém

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Tipy pro instalaci IBM Db2 LUW s HADR

Nastavení primární instance databáze IBM Db2 LUW:

- Použijte možnost vysoké dostupnosti nebo distribuované.
- Nainstalujte instanci SAP ASCS/ERS a databáze.
- Zálohujte nově nainstalovanou databázi.


> [!IMPORTANT] 
> Poznamenejte si "Databázový komunikační port", který je nastaven během instalace. Musí se jednat o stejné číslo portu pro obě instance databáze.

Chcete-li nastavit databázový server pohotovostního režimu pomocí postupu homogenní horečné kopie systému SAP, proveďte tyto kroky:

1. Vyberte možnost **Systémová kopie** >**instance****distribuované** > databáze **cílových systémů** > .
1. Jako metodu kopírování vyberte **možnost Homogenní systém,** abyste mohli pomocí zálohy obnovit zálohu v instanci pohotovostního serveru.
1. Po dosažení kroku ukončení obnovit databázi pro homogenní systémové kopie, ukončete instalační program. Obnovte databázi ze zálohy primárního hostitele. Všechny následující fáze instalace již byly provedeny na primárním databázovém serveru.
1. Nastavte HADR pro IBM Db2.

   > [!NOTE]
   > Pro instalaci a konfiguraci, která je specifická pro Azure a Pacemaker: Během postupu instalace prostřednictvím SAP Software Provisioning Manager, je explicitní otázka o vysoké dostupnosti pro IBM Db2 LUW:
   >+ Nevybírejte **IBM Db2 pureScale**.
   >+ Nevybírejte **možnost Nainstalovat automatizaci systému IBM Tivoli pro multiplatformy**.
   >+ **Nevybírejte generovat konfigurační soubory clusteru**.

   Při použití zařízení SBD pro Linux Pacemaker nastavte následující parametry Db2 HADR:
   + Trvání okna partnerského partnera HADR (v sekundách) (HADR_PEER_WINDOW) = 300  
   + Hodnota časového času HADR (HADR_TIMEOUT) = 60

   Při použití agenta oplocení Azure Pacemaker nastavte následující parametry:
   + Trvání okna partnerského partnera HADR (v sekundách) (HADR_PEER_WINDOW) = 900  
   + Hodnota časového času HADR (HADR_TIMEOUT) = 60

Doporučujeme předchozí parametry na základě počátečního převzetí služeb při selhání/převzetí testování. Je povinné, abyste testovali správnou funkcionalitu převzetí služeb při selhání a převzetí s těmito nastaveními parametrů. Vzhledem k tomu, že jednotlivé konfigurace se mohou lišit, mohou parametry vyžadovat úpravu. 

> [!IMPORTANT]
> Specifické pro IBM Db2 s konfigurací HADR při normálním spuštění: Před spuštěním instance primární databáze musí být spuštěna sekundární nebo pohotovostní instance databáze.

Pro demonstrační účely a postupy popsané v tomto článku je sid databáze **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Kontrola IBM Db2 HADR
Po konfiguraci HADR a stavu je PEER a CONNECTED na primární a pohotovostní uzly, proveďte následující kontrolu:

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



## <a name="db2-pacemaker-configuration"></a>Konfigurace kardiostimulátoru Db2

Při použití kardiostimulátoru pro automatické převzetí služeb při selhání v případě selhání uzlu, je třeba nakonfigurovat db2 instance a kardiostimulátor odpovídajícím způsobem. Tato část popisuje tento typ konfigurace.

Následující položky jsou předponou buď:

- **[A]**: Platí pro všechny uzly
- **[1]**: Platí pouze pro uzel 1 
- **[2]**: Platí pouze pro uzel 2

**[A]** Předpoklady pro konfiguraci kardiostimulátoru:
1. Vypněte oba databázové servery\<s uživatelem db2 sid> s db2stop.
1. Změňte prostředí prostředí\<pro db2 sid> uživatele na */bin/ksh*. Doporučujeme použít nástroj Yast. 


### <a name="pacemaker-configuration"></a>Konfigurace kardiostimulátoru

> [!IMPORTANT]
> Nedávné testování odhalilo situace, kdy netcat přestane reagovat na požadavky z důvodu nevyřízených položek a jeho omezení zpracování pouze jednoho připojení. Prostředek netcat přestane naslouchat požadavkům azure balancer a plovoucí IP přestane být k dispozici.  
> Pro stávající pacemaker clustery, doporučujeme v minulosti nahradit netcat socat. V současné době doporučujeme používat agenta prostředků azure-lb, který je součástí agenty prostředků balíčku, s následujícími požadavky na verzi balíčku:
> - Pro SLES 12 SP4/SP5 verze musí být alespoň agenty prostředků-4.3.018.a7fb5035-3.30.1.  
> - Pro SLES 15/15 SP1 verze musí být alespoň agenty prostředků-4.3.0184.6ee15eb2-4.13.1.  
>
> Všimněte si, že změna bude vyžadovat krátké prostoje.  
> Pro existující clustery Pacemaker, pokud konfigurace již byla změněna na použití socat, jak je popsáno v [Azure Load Balancer detekce posílení ,](https://www.suse.com/support/kb/doc/?id=7024128)není nutné okamžitě přepnout na agenta prostředků azure-lb.

**[1]** Konfigurace kardiostimulátoru specifická pro IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Vytvoření prostředků IBM Db2:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Spusťte prostředky IBM Db2:
* Vyřazuj kardiostimulátor z režimu údržby.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou prostředky spuštěny.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 uzly nakonfigurováno
# <a name="5-resources-configured"></a>5 nakonfigurovaných prostředků

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Úplný seznam zdrojů:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Spuštěno azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Skupina zdrojů: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Spuštěno azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Spuštěno azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Sada hlavních/slave/ slave: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mistři: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Otroci: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Je nutné spravovat clusterované instance Db2 pomocí nástrojů Pacemaker. Pokud používáte příkazy db2, jako je například db2stop, kardiostimulátor zjistí akci jako selhání prostředku. Pokud provádíte údržbu, můžete uzly nebo prostředky převést do režimu údržby. Kardiostimulátor pozastaví monitorování zdrojů a pak můžete použít normální db2 správy příkazy.


### <a name="configure-azure-load-balancer"></a>Konfigurace služby Azure Load Balancer
Chcete-li nakonfigurovat Azure Balancer, doporučujeme použít [Skladovou jednotku pro vyrovnávání zatížení Azure Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) a pak provést následující kroky;

> [!NOTE]
> Standardní skladová položka pro vyrovnávání zatížení má omezení přístupu k veřejným IP adresám z uzlů pod vykladacím programem pro vyrovnávání zatížení. Článek [Veřejné připojení koncového bodu pro virtuální počítače pomocí nástroje Pro vyrovnávání zatížení Azure Standard ve scénářích s vysokou dostupností SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) popisuje způsoby, jak těmto uzlům povolit přístup k veřejným IP adresám.

1. Vytvoření front-endového fondu IP adres:

   a. Na webu Azure Portal otevřete Azure Load Balancer, vyberte **front-endový fond IP adres**a pak vyberte **Přidat**.

   b. Zadejte název nového front-endového fondu IP (například **připojení Db2).**

   c. Nastavte **přiřazení** na **statický**a zadejte IP adresu **Virtual-IP** definovanou na začátku.

   d. Vyberte **OK**.

   e. Po vytvoření nového front-endového fondu IP adres si poznamenejte IP adresu fondu.

1. Vytvoření back-endového fondu:

   a. Na webu Azure Portal otevřete Azure Balancer, vyberte **back-endové fondy**a pak vyberte **Přidat**.

   b. Zadejte název nového back-endového fondu (například **Db2-backend).**

   c. Vyberte **Přidat virtuální počítač**.

   d. Vyberte sadu dostupnosti nebo virtuální počítače hostující databázi IBM Db2 vytvořenou v předchozím kroku.

   e. Vyberte virtuální počítače clusteru IBM Db2.

   f. Vyberte **OK**.

1. Vytvoření sondy stavu:

   a. Na webu Azure Portal otevřete Azure Balancer, vyberte **sondy stavu**a vyberte **Přidat**.

   b. Zadejte název nové sondy stavu (například **Db2-hp**).

   c. Jako protokol a port **62500**vyberte **protokol TCP** . Ponechte hodnotu **Interval** nastavenou na **hodnotu 5**a hodnotu **Nefunkční prahovou** hodnotu nastavte na **hodnotu 2**.

   d. Vyberte **OK**.

1. Vytvořte pravidla vyrovnávání zatížení:

   a. Na webu Azure Portal otevřete Azure Load Balancer, vyberte **Pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.

   b. Zadejte název nového pravidla pro vyrovnávání zatížení (například **Db2-SID**).

   c. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **db2-front-end).**

   d. Ponechte **protokol** nastavený na **protokol TCP**a zadejte *port Port Database Communication port*.

   e. Zvyšte **časový limit nečinnosti** na 30 minut.

   f. Ujistěte se, že **povolit plovoucí IP**.

   g. Vyberte **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Provádění změn v profilech SAP pro použití virtuální IP adresy pro připojení
Chcete-li se připojit k primární instanci konfigurace HADR, aplikační vrstva SAP musí použít virtuální IP adresu, kterou jste definovali a nakonfigurovali pro Nástroj pro vyrovnávání zatížení Azure. Jsou vyžadovány následující změny:

/sapmnt/\<SID>/profil/VÝCHOZÍ. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/globální/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalace primárních a dialogových aplikačních serverů

Při instalaci primárních a dialogových aplikačních serverů proti konfiguraci DB2 HADR použijte název virtuálního hostitele, který jste vybrali pro konfiguraci. 

Pokud jste provedli instalaci před vytvořením konfigurace DB2 HADR, proveďte změny, jak je popsáno v předchozí části a následujícím způsobem pro zásobníky SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Kontrola adres URL ABAP+Java nebo Java stack systems JDBC

Pomocí konfiguračního nástroje J2EE zkontrolujte nebo aktualizujte adresu URL JDBC. Vzhledem k tomu, že nástroj J2EE Config je grafický nástroj, musíte mít nainstalovaný server X:
 
1. Přihlaste se k primárnímu aplikačnímu serveru instance J2EE a spusťte:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. V levém rámečku zvolte **úložiště zabezpečení**.
1. V pravém rámečku zvolte klíč jdbc/pool/\<SAPSID>/url.
1. Změňte název hostitele v adrese URL JDBC na název virtuálního hostitele.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Vyberte **Přidat**.
1. Chcete-li změny uložit, vyberte ikonu disku v levém horním rohu.
1. Zavřete konfigurační nástroj.
1. Restartujte instanci Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurace archivace protokolů pro nastavení HADR
Chcete-li nakonfigurovat archivaci protokolu Db2 pro nastavení HADR, doporučujeme nakonfigurovat primární i pohotovostní databázi tak, aby měla možnost automatického načítání protokolu ze všech umístění archivu protokolů. Primární i pohotovostní databáze musí být schopna načíst soubory archivu protokolu ze všech umístění archivu protokolu, do kterých může archivovat soubory protokolu jedna z instancí databáze. 

Archivaci protokolu provádí pouze primární databáze. Pokud změníte role HADR databázových serverů nebo dojde-li k selhání, nová primární databáze je zodpovědná za archivaci protokolu. Pokud jste nastavili více umístění archivů protokolů, mohou být protokoly archivovány dvakrát. V případě místního nebo vzdáleného dohánění může být také nutné ručně zkopírovat archivované protokoly ze starého primárního serveru do aktivního umístění protokolu nového primárního serveru.

Doporučujeme konfigurovat společnou sdílenou složku služby NFS, kde jsou protokoly zapsány z obou uzlů. Podíl NFS musí být vysoce dostupný. 

Můžete použít existující vysoce dostupné sdílené složky služby Souborů NFS pro přenosy nebo adresář profilu. Další informace naleznete v tématu:

- [Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha] 
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server se soubory Azure NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (pro vytvoření sdílených složek systému souborů NFS)


## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

Tato část popisuje, jak můžete otestovat nastavení Db2 HADR. *Každý test předpokládá, že jste přihlášeni jako root uživatele* a primární IBM Db2 běží na virtuálním počítači *azibmdb01.*

Počáteční stav pro všechny testovací případy je vysvětlen zde: (crm_mon -r nebo crm stav)

- **Stav crm** je snímek stavu kardiostimulátoru v době spuštění 
- **crm_mon -r** je kontinuální výstup stavu kardiostimulátoru

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Původní stav v systému SAP je popsán v transakčním DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit - před migrací](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Zkušební převzetí IBM Db2


> [!IMPORTANT] 
> Před zahájením testu se ujistěte, že:
> * Kardiostimulátor nemá žádné neúspěšné akce (stav CRM).
> * Neexistují žádná omezení umístění (zbytky testu migrace)
> * Synchronizace IBM Db2 HADR funguje. Poraďte se\<s uživatelem db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrujte uzel, na který běží primární databáze Db2, spuštěním následujícího příkazu:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po dokončení migrace vypadá výstup stavu crm takto:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Původní stav v systému SAP je popsán v transakčním DBACOCKPIT > Konfigurace > Přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit - post migrace](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migrace prostředků s "crm resource migrate" vytváří omezení umístění. Omezení umístění by měla být odstraněna. Pokud omezení umístění nejsou odstraněny, prostředek nemůže zpět nouzi nebo může dojít k nežádoucí převzetí. 

Migrace prostředku zpět do *azibmdb01* a vymazání omezení umístění
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **zdroj CRM \< \<migruje res_name> hostitele>:** Vytvoří omezení umístění a může způsobit problémy s převzetím
- **Vymazat res_name \<>zdroje crm **: Vymaže omezení umístění
- **Vyčištění \<prostředků crm res_name>**: Vymaže všechny chyby prostředku

### <a name="test-the-fencing-agent"></a>Otestujte šermířského činidla

V tomto případě testujeme SBD oplocení, které doporučujeme při používání SUSE Linuxu.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Uzel *clusteru azibmdb01* by měl být restartován. Primární role HADR IBM Db2 bude přesunuta na *azibmdb02*. Když *azibmdb01* je zpět do režimu online, db2 instance se bude pohybovat v roli instance sekundární databáze. 

Pokud se služba Pacemaker nespustí automaticky na restartovaném dřívějším primárním zařízení, nezapomeňte ji spustit ručně pomocí:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Otestujte ruční převzetí

Ruční převzetí můžete otestovat zastavením služby Pacemaker na uzlu *azibmdb01:*
<pre><code>service pacemaker stop</code></pre>

stav na *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Po převzetí služeb při selhání můžete službu znovu spustit na *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Uřadit proces Db2 na uzlu, ve které je spuštěna primární databáze HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Instance Db2 se nezdaří a Pacemaker bude hlásit následující stav:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Kardiostimulátor restartuje primární databázi Db2 instance na stejném uzlu, nebo bude převzetí služeb při selhání do uzlu, který je spuštěn sekundární databázi instance a je hlášena chyba.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Ukončení procesu Db2 na uzlu, ve které je spuštěna instance sekundární databáze

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Uzel se dostane do selhání uvedeno a nahlášena chyba
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Instance Db2 se restartuje v sekundární roli, kterou předtím přiřadila.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zastavit DB přes db2stop force na uzlu, ve které běží instance primární databáze HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Jako uživatel\<db2 sid> spustit příkaz db2stop force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Byla zjištěna chyba.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Instance sekundární databáze Db2 HADR byla povýšena na primární roli
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Selhání virtuálního počítače s restartováním v uzlu, ve které běží instance primární databáze HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Kardiostimulátor bude podporovat sekundární instance na roli primární instance. Stará primární instance se přesune do sekundární role po virtuálním počítači a všechny služby jsou plně obnoveny po restartování virtuálního počítače:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Selhání virtuálního virtuálního serveru, který spouští instanci primární databáze HADR s "zastavit"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

V takovém případě Pacemaker zjistí, že uzel, který je spuštěn primární databáze instance neodpovídá.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Dalším krokem je kontrola situace *ve Splitu.* Poté, co přežívající uzel zjistí, že uzel, který naposledy spustil primární databázovou instanci, je vypnut, je spuštěno převzetí služeb při selhání prostředků.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


V případě "zastavení" uzlu je nutné restartovat uzel, který selhal, prostřednictvím nástrojů azure managementu (na webu Azure Portal, PowerShellu nebo v příkazovém příkazovém příkazovém příkazovém příkazu k řešení Azure). Po selhání uzlu je zpět do režimu online, spustí ddb2 instance do sekundární role.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Další kroky
- [Architektura a scénáře s vysokou dostupností pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Nastavení kardiostimulátoru na SUSE Linux Enterprise Serveru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

