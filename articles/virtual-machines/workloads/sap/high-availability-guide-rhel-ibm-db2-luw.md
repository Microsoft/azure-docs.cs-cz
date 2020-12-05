---
title: Nastavení HADR IBM Db2 na virtuálních počítačích Azure (VM) na RHEL | Microsoft Docs
description: Navažte vysokou dostupnost IBM Db2 LUW na virtuálních počítačích Azure RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 85f268990ac9e0c04cba1b9c409a232a24ce0d61
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608630"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Vysoká dostupnost IBM DB2 LUW ve virtuálních počítačích Azure na linuxovém serveru Red Hat Enterprise

IBM Db2 pro Linux, UNIX a Windows (LUW) v [konfiguraci vysoké dostupnosti a zotavení po havárii (hadr)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) se skládá z jednoho uzlu, na kterém běží primární instance databáze, a aspoň jednoho uzlu, na kterém běží sekundární instance databáze. Změny primární instance databáze jsou replikovány do sekundární instance databáze synchronně nebo asynchronně v závislosti na konfiguraci. 

> [!NOTE]
> Tento článek obsahuje odkazy na *Hlavní* a *podřízené* výrazy, které Microsoft už nepoužívá. Po odebrání těchto podmínek ze softwaru je odebereme z tohoto článku.

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
| [2002167] | Red Hat Enterprise Linux 7. x: instalace a upgrade |
| [2694118] | Red Hat Enterprise Linux Add-On HA v Azure |
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
| [Přehled Add-On vysoké dostupnosti pro Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Správa Add-On vysoké dostupnosti][rhel-ha-admin] |
| [Referenční informace o Add-On vysoké dostupnosti][rhel-ha-ref] |
| [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru][rhel-azr-supp]
| [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure][rhel-azr-inst]
| [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR 10,5][db2-hadr-10.5] |
| [Zásady podpory pro clustery s vysokou dostupností RHEL – Správa IBM Db2 pro Linux, UNIX a Windows v clusteru][rhel-db2-supp]



## <a name="overview"></a>Přehled
Pro zajištění vysoké dostupnosti se IBM Db2 LUW s HADR nainstaluje aspoň na dva virtuální počítače Azure, které jsou nasazené v rámci [skupiny dostupnosti Azure](../../windows/tutorial-availability-sets.md) nebo napříč [zóny dostupnosti Azure](./sap-ha-availability-zones.md). 

Následující obrázek zobrazí nastavení dvou virtuálních počítačů Azure databázového serveru. Virtuální počítače Azure databázového serveru mají připojené vlastní úložiště a jsou spuštěné v provozu. V HADR má jedna instance databáze na jednom z virtuálních počítačů Azure roli primární instance. Všichni klienti jsou připojení k primární instanci. Všechny změny v transakcích databáze jsou trvale uložené v transakčním protokolu Db2. Jelikož jsou záznamy transakčního protokolu trvale trvalé, jsou záznamy přenášeny prostřednictvím protokolu TCP/IP do instance databáze na druhém databázovém serveru, v pohotovostním serveru nebo v pohotovostní instanci. Instance pohotovostní aktualizace aktualizuje místní databázi tak, že přepošle převedené záznamy transakčního protokolu. Tímto způsobem je pohotovostní server udržován v synchronizaci s primárním serverem.

HADR je jenom funkce replikace. Nezjistila se žádná možnost automatického převzetí služeb při selhání nebo převzetí služeb při selhání. Převzetí nebo přenos na pohotovostní server je nutné iniciovat ručně správcem databáze. K zajištění automatického převzetí a detekce selhání můžete použít funkci clusteringu Pacemaker pro Linux. Pacemaker sleduje dvě instance databázového serveru. Když instance primárního databázového serveru selže, Pacemaker inicializuje *Automatické* převzetí hadr pohotovostním serverem. Pacemaker také zajistí, že se virtuální IP adresa přiřadí novému primárnímu serveru.

![Přehled vysoké dostupnosti IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Aby se aplikační servery SAP připojovaly k primární databázi, potřebujete název virtuálního hostitele a virtuální IP adresu. V případě převzetí služeb při selhání se aplikační servery SAP připojí k nové primární instanci databáze. V prostředí Azure se [Služba Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) vyžaduje k použití virtuální IP adresy způsobem, který je potřeba pro hadr IBM Db2. 

Následující obrázek vám pomůže plně porozumět tomu, jak se IBM Db2 LUW s HADR a Pacemaker do instalace systému SAP s vysokou dostupností, na následujícím obrázku je přehled vysoce dostupného nastavení systému SAP založeného na databázi IBM Db2. Tento článek se zabývá pouze IBM Db2, ale obsahuje odkazy na další články o nastavení dalších součástí systému SAP.

![Přehled úplného prostředí IBM DB2 High Availability](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Podrobný přehled požadovaných kroků
Pokud chcete nasadit konfiguraci IBM Db2, musíte postupovat podle těchto kroků:

  + Plánování prostředí.
  + Nasaďte virtuální počítače.
  + Aktualizujte RHEL Linux a nakonfigurujte systémy souborů.
  + Nainstalujte a nakonfigurujte Pacemaker.
  + Nastavení [clusteru GlusterFS][glusterfs] nebo [Azure NetApp Files][anf-rhel]
  + Nainstalujte [ASCS/olajících do samostatného clusteru][ascs-ha-rhel].
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
| Oplocení Azure | Způsob, jak zabránit rozdělení situací v mozku, je zabránit. |
| Nástroj pro vyrovnávání zatížení Azure | Využití úrovně Basic nebo Standard (doporučeno), port testu pro databázi Db2 (náš doporučení 62500) **– port**. |
| Překlad adres| Jak řešení překladu názvů funguje v prostředí. Služba DNS se důrazně doporučuje. Je možné použít místní soubor hostitelů. |
    
Další informace o Pacemaker pro Linux v Azure najdete v tématu [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Nasazení na Red Hat Enterprise Linux

Agent prostředků pro IBM Db2 LUW je součástí doplňku Red Hat Enterprise Linux Server HA. Pro instalační program, který je popsaný v tomto dokumentu, byste měli použít Red Hat Enterprise Linux pro SAP. Azure Marketplace obsahuje image pro Red Hat Enterprise Linux 7,4 pro SAP nebo novější, kterou můžete použít k nasazení nových virtuálních počítačů Azure. Mějte na paměti, že různé modely podpory nebo služeb, které nabízí Red Hat prostřednictvím Azure Marketplace, když na Azure VM Marketplace zvolíte image virtuálního počítače.

### <a name="hosts-dns-updates"></a>Hostitelé: aktualizace DNS
Vytvořte seznam všech názvů hostitelů, včetně názvů virtuálních hostitelů, a aktualizujte servery DNS tak, aby umožňovaly správnou IP adresu pro překlad názvů hostitelů. Pokud server DNS neexistuje nebo pokud nemůžete aktualizovat a vytvářet položky DNS, musíte použít místní hostitelské soubory jednotlivých virtuálních počítačů, které se podílejí v tomto scénáři. Pokud používáte položky souborů hostitele, ujistěte se, že jsou položky aplikovány na všechny virtuální počítače v prostředí systému SAP. Doporučujeme ale použít službu DNS, která v ideálním případě rozšiřuje do Azure.


### <a name="manual-deployment"></a>Ruční nasazení

Ujistěte se, že je vybraný operační systém podporovaný IBM/SAP pro IBM Db2 LUW. Seznam podporovaných verzí operačního systému pro virtuální počítače Azure a verze Db2 je k dispozici v části SAP Note [1928533]. Seznam verzí operačního systému podle individuální verze Db2 je k dispozici v matici dostupnost produktu SAP. Důrazně doporučujeme minimálně Red Hat Enterprise Linux 7,4 pro SAP z důvodu vylepšení výkonu souvisejících s Azure v této nebo novějších Red Hat Enterprise Linuxch verzích.

1. Vytvořte nebo vyberte skupinu prostředků.
1. Vytvořte nebo vyberte virtuální síť a podsíť.
1. Vytvořte skupinu dostupnosti Azure nebo nasaďte zónu dostupnosti.
    + V poli Skupina dostupnosti nastavte maximální počet aktualizačních domén na 2.
1. Vytvořte virtuální počítač 1.
    + Použijte Red Hat Enterprise Linux pro Image SAP v Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte zónu dostupnosti.
1.  Vytvořte virtuální počítač 2.
    + Použijte Red Hat Enterprise Linux pro Image SAP v Azure Marketplace.
    + Vyberte skupinu dostupnosti Azure, kterou jste vytvořili v kroku 3, nebo vyberte zónu dostupnosti (nikoli stejnou zónu jako v kroku 3).
1. Přidejte datové disky k virtuálním počítačům a pak zkontrolujte doporučení k instalaci systému souborů v článku [IBM Db2 Azure Virtual Machines DBMS nasazení pro úlohy SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker
    
Pokud chcete pro tento server IBM Db2 vytvořit základní cluster Pacemaker, přečtěte si téma [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Pravidla firewallu Red Hat
Ve výchozím nastavení má Red Hat Enterprise Linux zapnutou bránu firewall. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Tipy k instalaci pro nastavení IBM Db2 LUW pomocí HADR

Nastavení primární instance databáze IBM Db2 LUW:

- Použijte možnost Vysoká dostupnost nebo distribuované.
- Nainstalujte SAP ASCS/OLAJÍCÍCH a instanci databáze.
- Proveďte zálohu nově nainstalované databáze.

> [!IMPORTANT] 
> Zapište port pro komunikaci databáze, který se nastaví během instalace. Musí se jednat o stejné číslo portu pro obě instance databáze.
>![Definice portu SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Nastavení HADR IBM Db2 pro Azure

   Když použijete agenta pro oplocení Azure Pacemaker, nastavte následující parametry:

   - Doba trvání partnerského okna HADR (sekundy) (HADR_PEER_WINDOW) = 240  
   - Hodnota časového limitu HADR (HADR_TIMEOUT) = 45

V závislosti na počátečním testování/převzetí služeb při selhání doporučujeme předchozí parametry. Je nutné, abyste otestovali správné funkce převzetí služeb při selhání a převzetí pomocí těchto nastavení parametrů. Vzhledem k tomu, že se jednotlivé konfigurace můžou lišit, můžou parametry vyžadovat úpravu. 

> [!NOTE]
> Specifické pro IBM Db2 s konfigurací HADR s normálním spuštěním: před spuštěním primární instance databáze musí být spuštěná a spuštěná instance sekundární databáze nebo databáze v pohotovostním režimu.

   
> [!NOTE]
> Pro instalaci a konfiguraci, které jsou specifické pro Azure a Pacemaker: během procesu instalace prostřednictvím nástroje SAP software Provisioning Manager existuje explicitní otázka vysoké dostupnosti pro IBM Db2 LUW:
>+ Nevybírejte možnost **IBM Db2 pureScale**.
>+ Nevybírejte **instalovat automatizaci systému IBM Tivoli pro více platforem**.
>+ Nevybírejte možnost **Generovat konfigurační soubory clusteru**.
>![Možnosti SAP SWPM – DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Pokud chcete nastavit pohotovostní databázový server pomocí procedury pro homogenní systémovou kopii SAP, proveďte tyto kroky:

1. Vyberte možnost **kopírování systému** > **cílová**  >  **Distributed**  >  **instance distribuované databáze**.
1. Jako metodu kopírování vyberte **homogenní systém** , abyste mohli obnovit zálohu na pohotovostní instanci serveru pomocí zálohování.
1. Až se dostanete k kroku konec obnovení databáze pro homogenní systémovou kopii, ukončete instalační program. Obnovte databázi ze zálohy primárního hostitele. Všechny následné fáze instalace už jsou spuštěné na primárním databázovém serveru.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Pravidla firewallu Red Hat pro DB2 HADR
Přidejte pravidla brány firewall, která umožní provoz do DB2 a mezi servery DB2 a HADR pracovat:
+ Port pro komunikaci databáze. Pokud používáte oddíly, přidejte tyto porty i vy.
+ HADR port (hodnota parametru DB2 HADR_LOCAL_SVC)
+ Port testu Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>HADR ověření IBM Db2
V případě demonstračních účelů a postupů popsaných v tomto článku je identifikátor SID databáze **ID 2**.

Po nakonfigurování HADR a stav je PEER a PŘIPOJENÝ k primárnímu a pohotovostnímu uzlu, proveďte následující kontrolu:

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



## <a name="db2-pacemaker-configuration"></a>Konfigurace Pacemaker Db2

Pokud v případě selhání uzlu používáte Pacemaker pro automatické převzetí služeb při selhání, budete muset nakonfigurovat instance Db2 a Pacemaker odpovídajícím způsobem. Tato část popisuje tento typ konfigurace.

Následující položky jsou s předponou buď:

- **[A]**: platí pro všechny uzly
- **[1]**: platí pouze pro uzel 1 
- **[2]**: platí pouze pro uzel 2

**[A]** předpoklad pro Pacemaker konfigurace:
1. Ukončete obě databázové servery s uživatelem DB2 \<sid> pomocí db2stop.
1. Změna prostředí prostředí pro uživatele DB2 \<sid> na */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Konfigurace Pacemaker

**[1]** IBM Db2 hadr – konfigurace Pacemaker pro konkrétní:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** vytvořit prostředky IBM Db2:
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

**[1]** spusťte prostředky IBM Db2:
* Přepněte Pacemaker z režimu údržby.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 AZ-idb02]

Úplný seznam prostředků:

 rsc_st_azure (stonith: fence_azure_arm): začátek AZ-idb01 Master/podřízená sada: Db2_HADR_ID2-Master [Db2_HADR_ID2] Masters: [az-idb01] Slaves: [az-idb02] Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (OCF:: prezenční signál: IPaddr2): Začínáme AZ-idb01 nc_db2id2_ID2 (OCF:: prezenční signál: Azure-dis): Začínáme AZ-idb01

Stav démona: Corosync: aktivní/zakázané Pacemaker: aktivní/zakázané pcsd: aktivní/povoleno
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

**[A]** přidejte pravidlo brány firewall pro port testu paměti:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Přihlaste se k primárnímu aplikačnímu serveru instance J2EE a spusťte:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. V levém rámci vyberte **úložiště zabezpečení**.
1. V pravém rámečku Vyberte klíč `jdbc/pool/\<SAPSID>/url` .
1. Změňte název hostitele v adrese URL JDBC na název virtuálního hostitele.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Vyberte **Přidat**.
1. Pokud chcete změny uložit, vyberte ikonu disku v levém horním rohu.
1. Zavřete konfigurační nástroj.
1. Restartujte instanci Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurace archivace protokolů pro instalaci HADR
Pokud chcete nakonfigurovat archivaci protokolu Db2 pro instalaci HADR, doporučujeme, abyste nakonfigurovali databázi primárního i pohotovostního režimu tak, aby ve všech umístěních archivu protokolů byla funkce automatického načítání protokolů. Primární i pohotovostní databáze musí být schopné načítat soubory archivu protokolu ze všech umístění archivu protokolů, do kterých může archivovat soubory protokolu buď jedna z těchto instancí databáze. 

Archivace protokolu je prováděna pouze v primární databázi. Pokud změníte role HADR databázových serverů nebo dojde k selhání, je nová primární databáze zodpovědná za archivaci protokolu. Pokud jste nastavili více umístění archivu protokolů, můžou být protokoly dvakrát archivovány. V případě místního nebo vzdáleného zachycení může být nutné ručně zkopírovat archivované protokoly ze starého primárního serveru do umístění aktivního protokolu nového primárního serveru.

Doporučujeme nakonfigurovat společné sdílené složky NFS nebo GlusterFS, ve kterých jsou protokoly napsané z obou uzlů. Sdílená složka nebo GlusterFS systému souborů NFS musí být vysoce dostupná. 

Pro přenosy nebo adresář profilu můžete použít stávající vysoce dostupné sdílené složky systému souborů NFS nebo GlusterFS. Další informace najdete tady:

- [GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver][glusterfs] 
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (pro vytváření sdílených složek NFS)

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Tato část popisuje, jak můžete otestovat instalaci Db2 HADR. Každý test předpokládá, že je na virtuálním počítači *AZ-idb01* spuštěná primární IBM Db2. Musí se použít uživatel s oprávněními sudo nebo rootem (nedoporučuje se).

Počáteční stav všech testovacích případů je vysvětlen zde: (crm_mon-r nebo stav počítače)

- **stav počítačů** je snímek stavu Pacemaker v době spuštění. 
- **crm_mon-r** je průběžný výstup stavu Pacemaker

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

Původní stav systému SAP je popsán v části > konfigurace služby Transaction DBACOCKPIT > přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – předběžná migrace](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Převzetí testu IBM Db2


> [!IMPORTANT] 
> Než začnete s testem, ujistěte se, že:
> * Pacemaker nemá žádné neúspěšné akce (stav počítačů).
> * Neexistují žádná omezení umístění (Leftovers test migrace).
> * Synchronizace IBM Db2 HADR funguje. Ověřit s uživatelem DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrujte uzel, na kterém je spuštěná primární databáze Db2, spuštěním následujícího příkazu:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Po dokončení migrace bude výstup stavu CRM vypadat takto:
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

Původní stav systému SAP je popsán v části > konfigurace služby Transaction DBACOCKPIT > přehled, jak je znázorněno na následujícím obrázku:

![DBACockpit – migrace po migraci](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Migrace prostředků s názvem "počítače pro přesun" vytvoří omezení umístění. Omezení umístění v tomto případě brání spuštění instance IBM Db2 na az-idb01. Pokud nejsou omezení umístění odstraněna, nelze prostředek navrátit navrácením služeb po obnovení.

Odebrat omezení umístění a pohotovostní uzel se spustí na az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
A stav clusteru se změní na:
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

![DBACockpit – omezení umístění se odebralo.](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrujte prostředek zpátky na *AZ-idb01* a vymažte omezení umístění.
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **přesuny prostředků počítačů \<res_name> <host> :** vytvoří omezení umístění a může způsobit problémy s převzetím.
- **prostředek počítače vymazat \<res_name>**: vymaže omezení umístění.
- **Vyčištění \<res_name> prostředků počítačů**: vymaže všechny chyby prostředku.

### <a name="test-a-manual-takeover"></a>Test ručního převzetí

Ruční převzetí můžete otestovat zastavením služby Pacemaker na uzlu *AZ-idb01* :
<pre><code>systemctl stop pacemaker</code></pre>

stav na *AZ-ibdb02*
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

Po převzetí služeb při selhání můžete službu znovu spustit na *AZ-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Ukončí proces Db2 na uzlu, na kterém běží primární databáze HADR.

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Instance Db2 bude neúspěšná a Pacemaker přesune hlavní uzel a nahlásí následující stav:

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

Pacemaker restartuje instanci primární databáze Db2 na stejném uzlu, nebo převezme služby při selhání na uzel, na kterém je spuštěná sekundární instance databáze, a nahlásí se chyba.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Ukončí proces Db2 na uzlu, na kterém je spuštěná instance sekundární databáze.

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Uzel se nepovedlo uvést a nahlásila se chyba.
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

Instance Db2 se v sekundární roli přiřadí znovu.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zastavení DB přes db2stop Force na uzlu, na kterém běží primární instance databáze HADR

Když uživatel DB2 \<sid> provede příkaz db2stop Force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Zjištěna chyba:

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

Instance sekundární databáze Db2 HADR byla povýšena do primární role.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Došlo k chybě virtuálního počítače, na kterém běží primární instance databáze HADR s názvem "zastavit".

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

V takovém případě Pacemaker zjistí, že uzel, na kterém běží primární instance databáze, nereaguje.

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

Dalším krokem je vyhledání situace *rozdělení mozku* . Poté, co zbývající uzel určí, že uzel, který naposledy spustil primární databázi, je vypnutý, dojde k převzetí služeb při selhání prostředků.

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


V případě nouzového uzlu se uzel, který selhal, přestará o agenta oplocení. Po návratu uzlu, který se nezdařil do režimu online, je nutné spustit cluster Pacemaker podle
<pre><code>sudo pcs cluster start</code></pre> spustí instanci Db2 do sekundární role.

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

## <a name="next-steps"></a>Další kroky
- [Architektura a scénáře s vysokou dostupností pro SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
