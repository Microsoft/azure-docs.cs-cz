---
title: Nastavení HADR IBM Db2 na virtuálních počítačích Azure (virtuální počítače) | Microsoft Docs
description: Navažte vysokou dostupnost IBM Db2 LUW na virtuálních počítačích Azure (VM).
author: msjuergent
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 01f02efd36c51f3969ee53e9efc78fbe1664b187
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486534"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Pacemaker

IBM Db2 pro Linux, UNIX a Windows (LUW) v [konfiguraci vysoké dostupnosti a zotavení po havárii (hadr)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) se skládá z jednoho uzlu, na kterém běží primární instance databáze, a aspoň jednoho uzlu, na kterém běží sekundární instance databáze. Změny primární instance databáze jsou replikovány do sekundární instance databáze synchronně nebo asynchronně v závislosti na konfiguraci. 

Tento článek popisuje, jak nasadit a nakonfigurovat virtuální počítače Azure, nainstalovat architekturu clusteru a nainstalovat IBM Db2 LUW s konfigurací HADR. 

Tento článek nepopisuje, jak nainstalovat a nakonfigurovat IBM Db2 LUW s instalací softwaru HADR nebo SAP. Abychom vám pomohli dosáhnout těchto úkolů, poskytujeme odkazy na instalační příručky pro SAP a IBM. Tento článek se zaměřuje na části, které jsou specifické pro prostředí Azure. 

Podporované verze IBM Db2 jsou 10,5 a novější, jak je popsáno v části SAP Note [1928533].

Než začnete s instalací, přečtěte si následující poznámky a dokumentace SAP:

| Poznámka SAP | Popis |
| --- | --- |
| [1928533] | Aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure |
| [2015553] | SAP v Azure: požadavky na podporu |
| [2178632] | Klíčové metriky monitorování pro SAP v Azure |
| [2191498] | SAP v systému Linux s Azure: rozšířené monitorování |
| [2243692] | Virtuální počítač se systémem Linux v Azure (IaaS): problémy s licencí SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: poznámky k instalaci |
| [1999351] | Řešení potíží s vylepšeným monitorováním Azure pro SAP |
| [2233094] | DB6: aplikace SAP v Azure, které používají IBM Db2 pro Linux, UNIX a Windows – Další informace |
| [1612105] | DB6: nejčastější dotazy k Db2 s HADR |


| Dokumentace | 
| --- |
| [Komunitní wikiweb SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): má všechny požadované poznámky SAP pro Linux |
| Průvodce [plánováním a implementací Azure Virtual Machines pro SAP v systému Linux][planning-guide] |
| [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide] (Tento článek) |
| Průvodce [nasazením systému správy databází Azure Virtual Machines pro SAP v Linux][dbms-guide] |
| [Úlohy SAP v kontrolním seznamu pro plánování a nasazení Azure][azr-sap-plancheck] |
| [Příručky k osvědčeným postupům pro aplikace SAP s 12 SP4 pro SUSE Linux Enterprise Server][sles-for-sap-bp] |
| [SUSE Linux Enterprise High Availability Extension 12 SP4][sles-ha-guide] |
| [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Přehled
Pro zajištění vysoké dostupnosti se IBM Db2 LUW s HADR nainstaluje aspoň na dva virtuální počítače Azure, které jsou nasazené v rámci [skupiny dostupnosti Azure](../../windows/tutorial-availability-sets.md) nebo napříč [zóny dostupnosti Azure](./sap-ha-availability-zones.md). 

Následující obrázek zobrazí nastavení dvou virtuálních počítačů Azure databázového serveru. Virtuální počítače Azure databázového serveru mají připojené vlastní úložiště a jsou spuštěné v provozu. V HADR má jedna instance databáze na jednom z virtuálních počítačů Azure roli primární instance. Všichni klienti jsou připojení k této primární instanci. Všechny změny v transakcích databáze jsou trvale uložené v transakčním protokolu Db2. Jelikož jsou záznamy transakčního protokolu trvale trvalé, jsou záznamy přenášeny prostřednictvím protokolu TCP/IP do instance databáze na druhém databázovém serveru, v pohotovostním serveru nebo v pohotovostní instanci. Instance pohotovostní aktualizace aktualizuje místní databázi tak, že přepošle převedené záznamy transakčního protokolu. Tímto způsobem je pohotovostní server udržován v synchronizaci s primárním serverem.

HADR je jenom funkce replikace. Nezjistila se žádná možnost automatického převzetí služeb při selhání nebo převzetí služeb při selhání. Převzetí nebo přenos na pohotovostní server je nutné iniciovat ručně správcem databáze. K zajištění automatického převzetí a detekce selhání můžete použít funkci clusteringu Pacemaker pro Linux. Pacemaker sleduje dvě instance databázového serveru. Když instance primárního databázového serveru selže, Pacemaker inicializuje *Automatické* převzetí hadr pohotovostním serverem. Pacemaker také zajistí, že se virtuální IP adresa přiřadí novému primárnímu serveru.

![Přehled vysoké dostupnosti IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Aby se aplikační servery SAP připojovaly k primární databázi, potřebujete název virtuálního hostitele a virtuální IP adresu. V případě převzetí služeb při selhání se aplikační servery SAP připojí k nové primární instanci databáze. V prostředí Azure se [Služba Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) vyžaduje k použití virtuální IP adresy způsobem, který je potřeba pro hadr IBM Db2. 

Následující obrázek vám pomůže plně porozumět tomu, jak se IBM Db2 LUW s HADR a Pacemaker do instalace systému SAP s vysokou dostupností, na následujícím obrázku je přehled vysoce dostupného nastavení systému SAP založeného na databázi IBM Db2. Tento článek se zabývá pouze IBM Db2, ale obsahuje odkazy na další články o nastavení dalších součástí systému SAP.

![Přehled úplného prostředí IBM DB2 High Availability](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Podrobný přehled požadovaných kroků
Pokud chcete nasadit konfiguraci IBM Db2, musíte postupovat podle těchto kroků:

  + Plánování prostředí.
  + Nasaďte virtuální počítače.
  + Aktualizujte SUSE Linux a nakonfigurujte systémy souborů.
  + Nainstalujte a nakonfigurujte Pacemaker.
  + Nainstalujte [vysoce dostupný systém souborů NFS][nfs-ha].
  + Nainstalujte [ASCS/olajících do samostatného clusteru][ascs-ha].
  + Nainstalujte databázi IBM Db2 s možností distribuované/vysoké dostupnosti (SWPM).
  + Nainstalujte a vytvořte sekundární uzel databáze a instanci a nakonfigurujte HADR.
  + Potvrďte, že HADR funguje.
  + Použijte konfiguraci Pacemaker k řízení IBM Db2.
  + Nakonfigurujte Azure Load Balancer.
  + Instalace primárních aplikačních serverů a serverů dialogu.
  + Ověřte a přizpůsobte konfiguraci aplikačních serverů SAP.
  + Proveďte testy převzetí služeb při selhání a převzetí.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Plánování infrastruktury Azure pro hostování IBM Db2 LUW pomocí HADR

Před spuštěním nasazení dokončete proces plánování. Plánování staví základ pro nasazení konfigurace Db2 s HADR v Azure. Klíčové prvky, které musí být součástí plánování pro IMB Db2 LUW (část databáze prostředí SAP), jsou uvedené v následující tabulce:

| Téma | Krátký popis |
| --- | --- |
| Definování skupin prostředků Azure | Skupiny prostředků, ve kterých nasadíte virtuální počítač, virtuální síť, Azure Load Balancer a další prostředky. Může být existující nebo nový. |
| Definice virtuální sítě/podsítě | Kde se nasazují virtuální počítače pro IBM Db2 a Azure Load Balancer. Může být existující nebo nově vytvořená. |
| Virtuální počítače hostující IBM Db2 LUW | Velikost virtuálního počítače, úložiště, sítě, IP adresa. |
| Název a virtuální IP adresa virtuálního hostitele pro databázi IBM Db2| Virtuální IP adresa nebo název hostitele, který se používá pro připojení aplikačních serverů SAP. **DB-Virt-hostname**, **DB-Virt-IP**. |
| Oplocení Azure | Služby Azure pro monitorování a oplocení SBD (důrazně doporučeno). Metoda, která neumožňuje rozdělit situace mozku. |
| VIRTUÁLNÍ POČÍTAČ SBD | Velikost virtuálního počítače SBD, úložiště, síť. |
| Azure Load Balancer | Využití úrovně Basic nebo Standard (doporučeno), port testu pro databázi Db2 (náš doporučení 62500) **– port**. |
| Překlad adres| Jak řešení překladu názvů funguje v prostředí. Služba DNS se důrazně doporučuje. Je možné použít místní soubor hostitelů. |
    
Další informace o Pacemaker pro Linux v Azure najdete v tématu [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Nasazení na SUSE Linux

Agent prostředků pro IBM Db2 LUW je součástí SUSE Linux Enterprise Server pro aplikace SAP. Pro instalační program, který je popsaný v tomto dokumentu, je nutné použít server SUSE Linux pro aplikace SAP. Azure Marketplace obsahuje image pro SUSE Enterprise Server for SAP Applications 12, kterou můžete použít k nasazení nových virtuálních počítačů Azure. Mějte na paměti, že různé modely podpory nebo služeb, které jsou nabízené SUSE prostřednictvím Azure Marketplace, když zvolíte image virtuálního počítače na webu Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hostitelé: aktualizace DNS
Vytvořte seznam všech názvů hostitelů, včetně názvů virtuálních hostitelů, a aktualizujte servery DNS tak, aby umožňovaly správnou IP adresu pro překlad názvů hostitelů. Pokud server DNS neexistuje nebo pokud nemůžete aktualizovat a vytvářet položky DNS, musíte použít místní hostitelské soubory jednotlivých virtuálních počítačů, které se podílejí v tomto scénáři. Pokud používáte položky souborů hostitele, ujistěte se, že jsou položky aplikovány na všechny virtuální počítače v prostředí systému SAP. Doporučujeme ale použít službu DNS, která v ideálním případě rozšiřuje do Azure.


### <a name="manual-deployment"></a>Ruční nasazení

Ujistěte se, že je vybraný operační systém podporovaný IBM/SAP pro IBM Db2 LUW. Seznam podporovaných verzí operačního systému pro virtuální počítače Azure a verze Db2 je k dispozici v části SAP Note [1928533]. Seznam verzí operačního systému podle individuální verze Db2 je k dispozici v matici dostupnost produktu SAP. Důrazně doporučujeme minimálně SLES 12 SP4 z důvodu vylepšení výkonu souvisejících s Azure v této nebo novějších verzích systému SUSE Linux.

1. Vytvořte nebo vyberte skupinu prostředků.
1. Vytvořte nebo vyberte virtuální síť a podsíť.
1. Vytvořte skupinu dostupnosti Azure nebo nasaďte zónu dostupnosti.
    + V poli Skupina dostupnosti nastavte maximální počet aktualizačních domén na 2.
1. Vytvořte virtuální počítač 1.
    + Použijte SLES pro Image SAP v Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte zónu dostupnosti.
1.  Vytvořte virtuální počítač 2.
    + Použijte SLES pro Image SAP v Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte zónu dostupnosti (nikoli stejnou zónu jako v kroku 3).
1. Přidejte datové disky k virtuálním počítačům a pak zkontrolujte doporučení k instalaci systému souborů v článku [IBM Db2 Azure Virtual Machines DBMS nasazení pro úlohy SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker
    
Pokud chcete pro tento server IBM Db2 vytvořit základní cluster Pacemaker, přečtěte si téma [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalace prostředí IBM Db2 LUW a SAP

Než začnete s instalací prostředí SAP založeného na IBM Db2 LUW, přečtěte si následující dokumentaci:

+ Dokumentace k Azure
+ Dokumentace SAP
+ Dokumentace k IBM

Odkazy na tuto dokumentaci jsou uvedené v úvodní části tohoto článku.

Přečtěte si příručky k instalaci SAP týkající se instalace aplikací založených na NetWeaver v IBM Db2 LUW.

Příručky k portálu pro nápovědu SAP najdete pomocí [příručky pro instalaci SAP][sap-instfind].

Počet vodítek zobrazených na portálu můžete snížit nastavením následujících filtrů:

- Chci: "nainstalovat nový systém"
- Moje databáze: "IBM Db2 pro Linux, UNIX a Windows"
- Další filtry pro verze SAP NetWeaver, konfiguraci zásobníku nebo operační systém

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Tipy k instalaci pro nastavení IBM Db2 LUW pomocí HADR

Nastavení primární instance databáze IBM Db2 LUW:

- Použijte možnost Vysoká dostupnost nebo distribuované.
- Nainstalujte SAP ASCS/OLAJÍCÍCH a instanci databáze.
- Proveďte zálohu nově nainstalované databáze.


> [!IMPORTANT] 
> Zapište port pro komunikaci databáze, který se nastaví během instalace. Musí se jednat o stejné číslo portu pro obě instance databáze.

Pokud chcete nastavit pohotovostní databázový server pomocí procedury pro homogenní systémovou kopii SAP, proveďte tyto kroky:

1. Vyberte možnost **kopírování systému** > **cílová**  >  **Distributed**  >  **instance distribuované databáze**.
1. Jako metodu kopírování vyberte **homogenní systém** , abyste mohli obnovit zálohu na pohotovostní instanci serveru pomocí zálohování.
1. Až se dostanete k kroku konec obnovení databáze pro homogenní systémovou kopii, ukončete instalační program. Obnovte databázi ze zálohy primárního hostitele. Všechny následné fáze instalace už jsou spuštěné na primárním databázovém serveru.
1. Nastavte HADR pro IBM Db2.

   > [!NOTE]
   > Pro instalaci a konfiguraci, které jsou specifické pro Azure a Pacemaker: během procesu instalace prostřednictvím nástroje SAP software Provisioning Manager existuje explicitní otázka vysoké dostupnosti pro IBM Db2 LUW:
   >+ Nevybírejte možnost **IBM Db2 pureScale**.
   >+ Nevybírejte **instalovat automatizaci systému IBM Tivoli pro více platforem**.
   >+ Nevybírejte možnost **Generovat konfigurační soubory clusteru**.

   Pokud pro Linux Pacemaker používáte zařízení SBD, nastavte následující parametry Db2 HADR:
   + Doba trvání partnerského okna HADR (sekundy) (HADR_PEER_WINDOW) = 300  
   + Hodnota časového limitu HADR (HADR_TIMEOUT) = 60

   Když použijete agenta pro oplocení Azure Pacemaker, nastavte následující parametry:
   + Doba trvání partnerského okna HADR (sekundy) (HADR_PEER_WINDOW) = 900  
   + Hodnota časového limitu HADR (HADR_TIMEOUT) = 60

V závislosti na počátečním testování/převzetí služeb při selhání doporučujeme předchozí parametry. Je nutné, abyste otestovali správné funkce převzetí služeb při selhání a převzetí pomocí těchto nastavení parametrů. Vzhledem k tomu, že se jednotlivé konfigurace můžou lišit, můžou parametry vyžadovat úpravu. 

> [!IMPORTANT]
> Specifické pro IBM Db2 s konfigurací HADR s normálním spuštěním: před spuštěním primární instance databáze musí být spuštěná a spuštěná instance sekundární databáze nebo databáze v pohotovostním režimu.

Pro demonstrační účely a postupy popsané v tomto článku je identifikátor SID databáze **PTR**.

#### <a name="ibm-db2-hadr-check"></a>HADR ověření IBM Db2
Po nakonfigurování HADR a stav je PEER a PŘIPOJENÝ k primárnímu a pohotovostnímu uzlu, proveďte následující kontrolu:

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



## <a name="db2-pacemaker-configuration"></a>Konfigurace Pacemaker Db2

Pokud v případě selhání uzlu používáte Pacemaker pro automatické převzetí služeb při selhání, budete muset nakonfigurovat instance Db2 a Pacemaker odpovídajícím způsobem. Tato část popisuje tento typ konfigurace.

Následující položky jsou s předponou buď:

- **[A]**: platí pro všechny uzly
- **[1]**: platí pouze pro uzel 1 
- **[2]**: platí pouze pro uzel 2

**[A]** předpoklady pro konfiguraci Pacemaker:
1. Ukončete obě databázové servery s uživatelem DB2 \<sid> pomocí db2stop.
1. Změňte prostředí prostředí pro uživatele DB2 \<sid> na */bin/ksh*. Doporučujeme použít nástroj YaST. 


### <a name="pacemaker-configuration"></a>Konfigurace Pacemaker

> [!IMPORTANT]
> Nedávné testování odhalilo situace, kde NetCat přestane reagovat na požadavky z důvodu nevyřízených položek a omezení zpracování pouze jednoho připojení. Prostředek NetCat přestane naslouchat požadavkům nástroje pro vyrovnávání zatížení Azure a plovoucí IP adresa přestane být k dispozici.  
> Pro existující clustery Pacemaker doporučujeme v minulosti nahradit NetCat pomocí Socat. V současné době doporučujeme použít agenta prostředků Azure-, který je součástí prostředků balíčku – agenti s následujícími požadavky na verzi balíčku:
> - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
> - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
>
> Všimněte si, že tato změna bude vyžadovat krátké výpadky.  
> U existujících clusterů Pacemaker se v případě, že konfigurace již změnila tak, aby používala socat, jak je popsáno v tématu [posílení zabezpečení azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), neexistuje žádný požadavek na přepnutí přímo do agenta prostředků Azure-No.

**[1]** IBM Db2 hadr – konfigurace Pacemaker pro konkrétní:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** vytvořit prostředky IBM Db2:
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

**[1]** spusťte prostředky IBM Db2:
* Přepněte Pacemaker z režimu údržby.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 uzly nakonfigurované
# <a name="5-resources-configured"></a>nakonfigurované 5 prostředků

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Úplný seznam prostředků:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: external/SBD): spuštěno azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Skupina prostředků: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: prezenční signál: IPaddr2): spuštěno azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: prezenční signál: Azure-9,1): spuštěno azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Sada hlavních/podřízených: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Hlavní servery: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Podřízené: [azibmdb01]
</pre>

> [!IMPORTANT]
> Pomocí nástrojů Pacemaker musíte spravovat instanci Pacemaker clusterovaných instancí Db2. Pokud použijete příkazy DB2, jako je db2stop, Pacemaker detekuje akci jako selhání prostředku. Pokud provádíte údržbu, můžete uzly nebo prostředky umístit do režimu údržby. Pacemaker pozastaví monitorování prostředků a pak můžete použít normální příkazy pro správu DB2.


### <a name="configure-azure-load-balancer"></a>Konfigurace služby Azure Load Balancer
Pokud chcete nakonfigurovat Azure Load Balancer, doporučujeme použít službu [Azure Standard Load BALANCER SKU](../../../load-balancer/load-balancer-overview.md) a pak provést následující úkony:

> [!NOTE]
> SKU Standard Load Balancer má omezení přístupu k veřejným IP adresám z uzlů pod Load Balancer. Článek [připojení ke veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) popisuje způsoby, jak tyto uzly povolit přístup k veřejným IP adresám.

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

1. Vytvořte front-end fond IP adres:

   a. V Azure Portal otevřete Azure Load Balancer, vyberte **front-end IP fond** a pak vyberte **Přidat**.

   b. Zadejte název nového fondu front-end IP adres (například **Db2 připojení**).

   c. Nastavte **přiřazení** na hodnotu **static** a zadejte na začátku IP adresu **virtuální IP** adresa.

   d. Vyberte **OK**.

   e. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.

1. Vytvořte fond back-end:

   a. V Azure Portal otevřete Azure Load Balancer, vyberte **back-end fondy** a pak vyberte **Přidat**.

   b. Zadejte název nového fondu back-end (například **Db2-back-end**).

   c. Vyberte **Přidat virtuální počítač**.

   d. Vyberte skupinu dostupnosti nebo virtuální počítače hostující databázi IBM Db2 vytvořenou v předchozím kroku.

   e. Vyberte virtuální počítače clusteru IBM Db2.

   f. Vyberte **OK**.

1. Vytvořte sondu stavu:

   a. V Azure Portal otevřete Azure Load Balancer, vyberte **sondy stavu** a vyberte **Přidat**.

   b. Zadejte název nové sondy stavu (například **Db2-HP**).

   c. Jako protokol a port **62500** vyberte **TCP** . Hodnotu **intervalu** nastavte na **5** a v poli prahová hodnota není v **pořádku** nastavte hodnotu **2**.

   d. Vyberte **OK**.

1. Vytvořte pravidla vyrovnávání zatížení:

   a. V Azure Portal otevřete Azure Load Balancer, vyberte **pravidla vyrovnávání zatížení** a pak vyberte **Přidat**.

   b. Zadejte název nového pravidla Load Balancer (například **Db2-SID**).

   c. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Db2-front-endu**).

   d. Zachovejte **protokol** nastaven na **TCP** a zadejte port pro *komunikaci databáze* portů.

   e. Zvyšte **časový limit nečinnosti** na 30 minut.

   f. Ujistěte se, že jste **povolili plovoucí IP adresu**.

   například Vyberte **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Provedení změn v profilech SAP pro použití virtuální IP adresy pro připojení
Aby bylo možné připojit se k primární instanci konfigurace HADR, musí aplikační vrstva SAP používat virtuální IP adresu, kterou jste definovali a nakonfigurovali pro Azure Load Balancer. Jsou vyžadovány tyto změny:

/sapmnt/ \<SID> /Profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalace serverových serverů primární a dialogových aplikací

Při instalaci primárních a dialogových aplikačních serverů s konfigurací Db2 HADR použijte název virtuálního hostitele, který jste vybrali pro konfiguraci. 

Pokud jste instalaci provedli předtím, než jste vytvořili konfiguraci Db2 HADR, proveďte změny, jak je popsáno v předchozí části, a následujícím způsobem pro zásobníky SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java nebo Java Stack Systems JDBC kontrolu adresy URL

Použijte konfigurační nástroj J2EE ke kontrole nebo aktualizaci adresy URL JDBC. Vzhledem k tomu, že nástroj J2EE Configuration Tool je grafický nástroj, je nutné mít nainstalovaný X Server:
 
1. Přihlaste se k primárnímu aplikačnímu serveru instance J2EE a spusťte:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. V levém rámci vyberte **úložiště zabezpečení**.
1. V pravém rámečku Vyberte klíč JDBC/Pool/ \<SAPSID> /URL..
1. Změňte název hostitele v adrese URL JDBC na název virtuálního hostitele.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Vyberte **Přidat**.
1. Pokud chcete změny uložit, vyberte ikonu disku v levém horním rohu.
1. Zavřete konfigurační nástroj.
1. Restartujte instanci Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurace archivace protokolů pro instalaci HADR
Pokud chcete nakonfigurovat archivaci protokolu Db2 pro instalaci HADR, doporučujeme, abyste nakonfigurovali databázi primárního i pohotovostního režimu tak, aby ve všech umístěních archivu protokolů byla funkce automatického načítání protokolů. Primární i pohotovostní databáze musí být schopné načítat soubory archivu protokolu ze všech umístění archivu protokolů, do kterých může archivovat soubory protokolu buď jedna z těchto instancí databáze. 

Archivace protokolu je prováděna pouze v primární databázi. Pokud změníte role HADR databázových serverů nebo dojde k selhání, je nová primární databáze zodpovědná za archivaci protokolu. Pokud jste nastavili více umístění archivu protokolů, můžou být protokoly dvakrát archivovány. V případě místního nebo vzdáleného zachycení může být nutné ručně zkopírovat archivované protokoly ze starého primárního serveru do umístění aktivního protokolu nového primárního serveru.

Doporučujeme nakonfigurovat společnou sdílenou složku NFS, do které se zapisují protokoly z obou uzlů. Sdílená složka systému souborů NFS musí být vysoce dostupná. 

Pro přenosy nebo adresář profilu můžete použít existující sdílené složky systému souborů NFS s vysokou dostupností. Další informace naleznete v tématu:

- [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha] 
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (pro vytváření sdílených složek NFS)


## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Tato část popisuje, jak můžete otestovat instalaci Db2 HADR. *Každý test předpokládá, že jste přihlášeni jako kořenový uživatel* a že je na virtuálním počítači *azibmdb01* spuštěná primární IBM Db2.

Počáteční stav všech testovacích případů je vysvětlen zde: (crm_mon-r nebo CRM status)

- **stav CRM** je snímek stavu Pacemaker v době spuštění. 
- **crm_mon-r** je průběžný výstup stavu Pacemaker

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

Původní stav systému SAP je popsán v části > konfigurace služby Transaction DBACOCKPIT > přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – předběžná migrace](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Převzetí testu IBM Db2


> [!IMPORTANT] 
> Než začnete s testem, ujistěte se, že:
> * Pacemaker nemá žádné neúspěšné akce (stav CRM).
> * Neexistují žádná omezení umístění (Leftovers test migrace).
> * Synchronizace IBM Db2 HADR funguje. Ověřit s uživatelem DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrujte uzel, na kterém je spuštěná primární databáze Db2, spuštěním následujícího příkazu:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po dokončení migrace bude výstup stavu CRM vypadat takto:
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

Původní stav systému SAP je popsán v části > konfigurace služby Transaction DBACOCKPIT > přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – migrace po migraci](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migrace prostředků pomocí možnosti "prostředek migrace prostředku CRM" vytvoří omezení umístění. Omezení umístění by se měla odstranit. Pokud nejsou omezení umístění odstraněna, prostředek nelze navrátit navrácením služeb po obnovení nebo se můžete setkat s nechtěným převzetím. 

Migrujte prostředek zpátky do *azibmdb01* a vymažte omezení umístění.
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migrace prostředků CRM \<res_name> \<host> :** vytvoří omezení umístění a může způsobit problémy s převzetím.
- **prostředek CRM Clear \<res_name>**: vymaže omezení umístění.
- **Vyčištění \<res_name> prostředků CRM**: vymaže všechny chyby prostředku.

### <a name="test-the-fencing-agent"></a>Testování agenta pro oplocení

V tomto případě testujeme SBD oplocení, což vám doporučujeme při použití SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

*Azibmdb01* uzlu clusteru by se mělo restartovat. Role primárního HADR IBM Db2 se přesune na *azibmdb02*. Když je *azibmdb01* zpátky online, instance Db2 se přesune do role sekundární instance databáze. 

Pokud se služba Pacemaker nespustí automaticky při restartování bývalé primární služby, nezapomeňte ji spustit ručně s:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Test ručního převzetí

Ruční převzetí můžete otestovat zastavením služby Pacemaker v uzlu *azibmdb01* :
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

Po převzetí služeb při selhání můžete službu spustit znovu v *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Ukončí proces Db2 na uzlu, na kterém běží primární databáze HADR.

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Instance Db2 bude neúspěšná a Pacemaker bude hlásit následující stav:

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

Pacemaker restartuje instanci primární databáze Db2 na stejném uzlu, nebo převezme služby při selhání na uzel, na kterém je spuštěná sekundární instance databáze, a nahlásí se chyba.

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


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Ukončí proces Db2 na uzlu, na kterém je spuštěná instance sekundární databáze.

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Uzel se nepovedlo uvést a nahlásila se chyba.
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

Instance Db2 se v sekundární roli přiřadí znovu.

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



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zastavení DB přes db2stop Force na uzlu, na kterém běží primární instance databáze HADR

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

Když uživatel DB2 \<sid> provede příkaz db2stop Force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Zjištěna chyba
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

Instance sekundární databáze Db2 HADR se zvýšila na primární roli.
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


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Virtuální počítač pro havárii s restartováním na uzlu, na kterém je spuštěná primární instance databáze HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker zvýší sekundární instanci na roli role primární instance. Stará primární instance se po restartování virtuálního počítače přesune do sekundární role a všechny služby se po restartování virtuálního počítače úplně obnoví:

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



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Došlo k chybě virtuálního počítače, na kterém běží primární instance databáze HADR s názvem "zastavit".

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

V takovém případě Pacemaker zjistí, že uzel, na kterém běží primární instance databáze, nereaguje.

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

Dalším krokem je vyhledání situace *rozdělení mozku* . Poté, co zbývající uzel určí, že uzel, který naposledy spustil primární databázi, je vypnutý, dojde k převzetí služeb při selhání prostředků.
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


V případě "zastavení" uzlu se neúspěšný uzel musí restartovat prostřednictvím nástrojů pro správu Azure (v Azure Portal, PowerShellu nebo Azure CLI). Po návratu uzlu, který se nezdařil do režimu online, spustí instanci Db2 do sekundární role.

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
- [Architektura a scénáře s vysokou dostupností pro SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md)

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