---
title: SAP Business jeden na Azure Virtual Machines | Dokumentace Microsoftu
description: SAP Business jeden na Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8bd5ddab4553807f59b7afdf32fbfc1703e3d75
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949521"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business jeden na Azure Virtual Machines
Tento dokument obsahuje pokyny pro nasazení SAP Business One ve službě Azure Virtual Machines. V dokumentaci k není náhradou za dokumentaci k instalaci podnikání, jeden pro SAP. Základní pokyny pro plánování a nasazení pro infrastrukturu Azure a spustit jednu obchodní aplikace v by měly pokrývat v dokumentaci.

Obchodní jeden podporuje dvě různé databáze:
- SQL Server – viz [SAP Poznámka #928839 – plánování vydání verze pro Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA – pro přesné SAP Business One matice podpory pro SAP HANA, a rezervovat [přehled dostupnosti SAP produktu](https://support.sap.com/pam)

Jak je uvedeno v týkající se SQL Server, na základě aspektů základní nasazení [nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) platí. pro SAP HANA jsou uvedené důležité informace v tomto dokumentu.

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti o následující komponenty Azure:

- [Virtuální počítače Azure ve Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Virtuální počítače Azure v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure, sítě a virtuální sítí správa pomocí Powershellu](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Sítě Azure a virtuální sítě pomocí rozhraní příkazového řádku ](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Správa disků v Azure pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

I v případě, že máte zájem business jeden pouze, dokument [Azure Virtual Machines plánování a implementace SAP Netweaveru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) může být dobrým zdrojem informací.

Předpokladem je, že se můžete jako instance nasazení SAP Business One:

- Zkušenosti s instalace SAP HANA na dané infrastruktury, jako jsou virtuální počítač
- Známý instalaci aplikace SAP Business One na infrastrukturu, jako jsou virtuální počítače Azure
- Zkušenosti s provozováním SAP Business One a zvolit systém DBMS
- Zkušenosti s nasazením infrastruktury v Azure

Všechny tyto oblasti nesmí být zahrnuté v tomto dokumentu.

Kromě dokumentace ke službě Azure byste měli znát hlavní poznámky SAP, který odkazuje na jednu obchodní nebo které jsou centrální poznámky SAP pro firmy, jeden:

- [528296 – Poznámka: Přehled obecné pro SAP Business jeden verze a související produkty](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 – poznámky k verzi aktualizace pro SAP Business jeden 9.2, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - centrální Poznámka k SAP Business jeden 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 – vydání aktualizace Poznámka pro SAP Business jeden 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - kolektivní Poznámka k SAP Business jeden 9.3 obecnými problémy souvisejícími s](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - kolektivní konzultační Poznámka pro SAP HANA-Related témata SAP Business One, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Architektura jednoho firmy
Obchodní z nich je aplikace, která má dvě úrovně:

- Klientská vrstva s klientem "fat.
- Databázová vrstva, která obsahuje schéma databáze pro tenanta

Lepší přehled, které komponenty jsou spuštěné v klientské části a které části jsou spuštěny v části server je popsána v [SAP Business jeden Příručka pro správce](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Protože náročné latence důležité interakce mezi klientská vrstva a vrstva DBMS, třeba obou úrovní nacházet v Azure, při nasazení v Azure. je běžné, že uživatelé pak vzdálené plochy na jeden nebo více virtuálních počítačů spuštěných vzdálené plochy služby pro Business One součásti klienta.

### <a name="sizing-vms-for-sap-business-one"></a>Změna velikosti virtuálních počítačů pro SAP Business jeden

Pokud jde o velikosti virtuálních počítačů klienta, požadavky na prostředky jsou popsány SAP v dokumentu [SAP Business jeden Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Pro Azure budete muset zaměřit a dobře se počítá s požadavky uvedenými v kapitole 2.4 dokumentu.

Jako virtuální počítače Azure pro hostování Business One klientské komponenty a hostitele DBMS jsou povoleny pouze virtuální počítače, které jsou podporovány SAP NetWeaver. Seznam SAP NetWeaver podporovaných virtuálních počítačích Azure najdete v článku [SAP 1928533 # Poznámka](https://launchpad.support.sap.com/#/notes/1928533).

Spuštění SAP HANA jako back-end systém DBMS pro Business One, jenom virtuální počítače, které jsou uvedeny pro firmy v HANA v [seznamu platformy IaaS certifeid HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) jsou podporovány pro HANA. Business One klientské součásti nejsou ovlivněny toto silnější omezení pro SAP HANA jako systém DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Verze operačního systému pro SAP Business One

V zásadě je vždy vhodné použít nejnovější verze operačního systému. Zejména v prostoru Linux nové funkce Azure zavedl různé novější vedlejší verze operačního systému Suse a Red Hat. Na straně Windows Windows Server 2016 s vysokou doporučuje se použít.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Nasazení infrastruktury v Azure pro SAP Business One
V dalším několik kapitolách dílky infrastruktury tomuto účelu nasazení SAP.

### <a name="azure-network-infrastructure"></a>Azure síťovou infrastrukturu
Síťové infrastruktury, které potřebujete k nasazení v Azure, závisí na, jestli nasazujete jednu Business One systém sami. Nebo, jestli se hostitel, který hostuje desítky Business One systémy pro zákazníky. Může existovat i drobné změny v návrhu na tom, jestli způsob připojení k Azure. Projít různé možnosti, jeden návrh kde máte připojení k síti VPN do Azure a ve kterém můžete rozšířit služby Active Directory prostřednictvím [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) do Azure.

![Jednoduchá síť konfigurace s Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Zjednodušená konfigurace zobrazí zavádí několik instancí zabezpečení, které umožňují ke správě a omezení směrování. Začíná 

- Směrovače a brány firewall na straně zákazníka místní.
- Další instance nastane o [skupiny zabezpečení sítě Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) můžete zavést pravidla směrování a zabezpečení pro virtuální síť Azure, kterou provozujete v jedné konfiguraci SAP Business.
- Pokud se chcete vyhnout, které uživatelé Business One klienta můžete zobrazit i serveru, na kterém běží obchodní jeden server, který se spouští v databázi, byste měli oddělit virtuálního počítače hostující obchodní jednoho klienta a business jeden server ve dvou různých podsítích virtuální sítě.
- Pokud chcete omezit přístup k jednomu serveru obchodní by znovu použít přiřazené ke dvěma různým podsítím skupiny zabezpečení sítě Azure.

Složitější verze konfigurace sítě Azure je založená na Azure [popsané doporučené postupy hvězdicové architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Vzor architektury hvězdicové změní první zjednodušená konfigurace na jednu takto:


![Rozbočovač a uvedenou konfiguraci s Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Pro případy, kdy se uživatelé připojují prostřednictvím Internetu bez jakéhokoli privátního připojení do Azure, návrhu sítě v Azure by měl být v souladu s principy zdokumentované v Azure referenční architektury pro [DMZ mezi Azure a Internetem](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Business jeden databázový server
Typ databáze systému SQL Server a SAP HANA jsou k dispozici. Bez ohledu na správce databáze, byste si měli přečíst dokument [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) zobrazíte obecné principy nasazení DBMS v azure virtuální počítače a související sítě a úložiště témata.

I když už oznámil v konkrétní a obecné databáze dokumentů, je třeba sami znát:

- [Správa dostupnosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a [Správa dostupnosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Smlouva SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Tyto dokumenty by vám při rozhodování o výběru typů úložiště a konfigurace vysoké dostupnosti.

V zásadě byste měli:

- Použití [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) přes [Azure Storage úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- Použití Azure Managed disks za nespravované disky
- Ujistěte se, že máte dostatečnou propustnost vstupně-výstupních operací a vstupně-výstupních operací nakonfigurovanou vaše konfigurace disků
- Abyste měli náklady na konfiguraci efektivní úložiště kombinovat/hana nebo data a /hana/log svazku


#### <a name="sql-server-as-dbms"></a>SQL Server jako DBMS
Pro nasazení systému SQL Server jako DBMS pro Business One, přejděte na dokument [nasazení SQL serveru Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Jsou odhady přibližnou velikosti pro stranu DBMS pro SQL Server:

| Počet uživatelů | vCPU | Memory (Paměť) | Příklad typy virtuálních počítačů |
| --- | --- | --- | --- |
| až 20 | 4 | 16 GB | D4s_v3 E4s_v3 |
| až 40 | 8 | 32 GB | D8s_v3 E8s_v3 |
| až 80 | 16 | 64 GB | D16s_v3 E16s_v3 |
| až 150 | 32 | 128 GB | D32s_v3 E32s_v3 |

Nastavení velikosti uvedené výše by měl získat představu tam, kde začít. Může být, že budete potřebovat méně nebo více prostředků, v takovém případě je snadné přizpůsobení v azure. Změna mezi typy virtuálních počítačů je možné s restartování virtuálního počítače.


#### <a name="sap-hana-as-dbms"></a>SAP HANA jako DBMS
Použití SAP HANA DBMS následujících částech vám by měly dodržovat důležité informace o dokumentu [SAP HANA v Azure Provozní příručka](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Pro vysokou dostupnost a konfiguracích zotavení po havárii kolem SAP HANA jako databázi pro Business One v Azure, přečtěte si dokumentaci [vysoké dostupnosti SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) a dokumentace na které z tohoto dokumentu.

Pro SAP HANA zálohování a obnovení strategie, byste si měli přečíst dokument [Průvodci zálohováním pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) a dokumentaci na který je odkazováno z tohoto dokumentu.

 
### <a name="business-one-client-server"></a>Obchodní na jeden server
Tyto součásti aspekty volby úložiště nejsou prvořadým zájmem. Nicméně budete chtít mít spolehlivé platformě. Pro tento virtuální počítač, proto měli používat Azure Storage úrovně Premium i pro základní virtuální pevný disk. Změna velikosti virtuálního počítače, s daty v [SAP Business jeden Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Pro Azure budete muset zaměřit a dobře se počítá s požadavky uvedenými v kapitole 2.4 dokumentu. Můžete vypočítat požadavky, je potřeba porovnání následující dokumenty, kde najít ideální virtuálního počítače:

- [Velikosti virtuálních počítačů Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Poznámka SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Porovnejte počet procesorů a paměti, které jsou potřebné k popisu společností Microsoft. Mějte také propustnost sítě při výběru virtuální počítače.








