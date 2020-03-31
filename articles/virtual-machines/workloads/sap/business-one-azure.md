---
title: SAP Business One na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: SAP Business One v Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101416"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One ve službě Azure Virtual Machines
Tento dokument obsahuje pokyny k nasazení SAP Business One na virtuálních počítačích Azure. Dokumentace není náhradou za instalační dokumentaci business one pro SAP. Dokumentace by měla zahrnovat základní pokyny pro plánování a nasazení pro infrastrukturu Azure pro spuštění aplikací Business One.

Business One podporuje dvě různé databáze:
- SQL Server – viz [SAP Note #928839 – plánování vydání pro Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - pro přesnou matici podpory SAP Business One pro SAP HANA, pokladna [SAP Product Availability Matrix](https://support.sap.com/pam)

Pokud jde o SQL Server, platí základní aspekty nasazení, jak jsou popsány v [nasazení DBMS virtuálních počítačů Azure pro SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) pro SAP HANA jsou v tomto dokumentu uvedeny úvahy.

## <a name="prerequisites"></a>Požadavky
Chcete-li použít tuto příručku, potřebujete základní znalosti následujících součástí Azure:

- [Virtuální počítače Azure ve Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Virtuální počítače Azure na Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Správa sítí a virtuálních sítí Azure pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Sítě Azure a virtuální sítě s cli](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Správa disků v Azure pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

I v případě, že máte zájem o podnikání pouze jeden, dokument [Azure Virtuální počítače plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) může být dobrým zdrojem informací.

Předpokladem je, že jako instance nasazení SAP Business One jsou:

- Znalost instalace SAP HANA na danou infrastrukturu, jako je virtuální počítač
- Důvěrně známé instalace aplikace SAP Business One na infrastrukturu, jako jsou virtuální počítače Azure
- Seznámeno s obsluhou SAP Business One a zvoleným systémem DBMS
- Seznámení s nasazením infrastruktury v Azure

Všechny tyto oblasti nebudou v tomto dokumentu zahrnuty.

Kromě dokumentace Azure byste si měli být vědomi hlavních SAP Notes, které odkazují na Business One nebo které jsou centrální poznámky od SAP pro podnikání One:

- [528296 - Poznámka k obecnému přehledu pro vydání SAP Business One a související produkty](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Aktualizace vydání Poznámka pro SAP Business One 9.2, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Centrální poznámka pro SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Aktualizace vydání Poznámka pro SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Kolektivní poznámka pro SAP Business One 9.3 Obecné problémy](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Souhrnná poradenská poznámka pro SAP HANA-Related Témata SAP Business One, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Architektura Business One
Business One je aplikace, která má dvě úrovně:

- Klientská úroveň s "tlustým" klientem
- Databázová vrstva, která obsahuje schéma databáze pro klienta

Lepší přehled, které součásti jsou spuštěny v klientské části a které součásti jsou spuštěny v části serveru, je popsán v [průvodci správcem SAP Business One](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Vzhledem k tomu, že existuje silná latence kritické interakce mezi klientskou vrstvou a vrstvou DBMS, musí být obě vrstvy umístěny v Azure při nasazování v Azure. Je obvyklé, že uživatelé pak RDS do jednoho nebo více virtuálních počítačů se službou RDS pro součásti klienta Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Dimenzování virtuálních měn pro SAP Business One

Pokud jde o velikost klientských virtuálních zařízení, požadavky na prostředky jsou dokumentovány SAP v dokumentu [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Pro Azure je potřeba se zaměřit a vypočítat s požadavky uvedenými v kapitole 2.4 dokumentu.

Jako virtuální počítače Azure pro hostování klientských komponent Business One a hostitele DBMS jsou povoleny pouze virtuální počítače, které jsou podporované SAP NetWeaver. Seznam virtuálních počítačů Azure podporovaných sapem NetWeaverem najdete v poznámce [SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Spuštění SAP HANA jako back-end DBMS pro Business One, jsou podporovány pouze virtuální počítačky, které jsou uvedeny pro podnikání na HANA v [seznamu platformy HANA certifeid IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) jsou podporovány pro HANA. Součásti klienta Business One nejsou ovlivněny tímto silnějším omezením pro systém SAP HANA jako systém DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Verze operačního systému pro SAP Business One

V zásadě je vždy nejlepší použít nejnovější verze operačního systému. Zejména v linuxovém prostoru byly zavedeny nové funkce Azure s různými novějšími menšími verzemi Suse a Red Hat. Na straně Windows je vysoce doporučeno používat Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Nasazení infrastruktury v Azure pro SAP Business One
V několika dalších kapitolách jsou části infrastruktury, které jsou důležité pro nasazení SAP.

### <a name="azure-network-infrastructure"></a>Síťová infrastruktura Azure
Síťová infrastruktura, kterou potřebujete nasadit v Azure, závisí na tom, jestli nasadíte jeden systém Business One pro sebe. Nebo zda jste hostitel, který hostí desítky systémů Business One pro zákazníky. Může také dojít k drobným změnám v návrhu o tom, zda se připojíte k Azure. Procházení různých možností, jeden návrh, kde máte připojení VPN do Azure a kde rozšířit svůj Active Directory prostřednictvím [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) do Azure.

![Jednoduchá konfigurace sítě s Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Zjednodušená konfigurace, která je prezentována, zavádí několik instancí zabezpečení, které umožňují řídit a omezovat směrování. Začíná to 

- Směrovač/brána firewall na místní straně zákazníka.
- Další instanci je [skupina zabezpečení sítě Azure,](https://docs.microsoft.com/azure/virtual-network/security-overview) kterou můžete použít k zavedení směrovacích a bezpečnostních pravidel pro virtuální síť Azure, ve které spustíte konfiguraci SAP Business one.
- Aby se zabránilo, že uživatelé klienta Business One můžete také zobrazit server, který běží server Business One, který spouští databázi, měli byste oddělit virtuální ho virtuálního počítačů hostujícího klienta Business one a obchodní jeden server ve dvou různých podsítích v rámci virtuální sítě.
- Pomocí služby Azure NSG přiřazené ke dvěma různým podsítím byste znovu použili, abyste omezili přístup k serveru Business one.

Sofistikovanější verze konfigurace sítě Azure je založená na [osvědčených postupech Azure architektury rozbočovače a paprsku](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Architektura vzor rozbočovače a paprsku by změnit první zjednodušenou konfiguraci na jeden, jako je tento:


![Konfigurace hubu a paprsku s Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

V případech, kdy se uživatelé připojují přes internet bez privátního připojení do Azure, by měl být návrh sítě v Azure v souladu se zásadami zdokumentovanými v referenční architektuře Azure pro [DMZ mezi Azure a internetem](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Databázový server Business One
Pro typ databáze sql server a SAP HANA jsou k dispozici. Nezávisle na DBMS byste si měli přečíst dokument [Aspekty nasazení DBMS virtuálních počítačů Azure pro úlohy SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) abyste získali obecné znalosti o nasazeníCH DBMS v azure virtuálních počítačích a souvisejících síťových a úložných tématech.

I když je zdůrazněno v konkrétních a obecných databázových dokumentech, měli byste se seznámit s:

- [Správa dostupnosti virtuálních počítačů SWindows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a [Správa dostupnosti virtuálních počítačů S IP v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Tyto dokumenty by vám měly pomoci při rozhodování o výběru typů úložišť a konfiguraci s vysokou dostupností.

V zásadě byste měli:

- Používejte prémiové disky SSD přes standardní pevné disky. Další informace o dostupných typech disků najdete v článku [Výběr typu disku](../../windows/disks-types.md)
- Použití spravovaných disků Azure přes nespravované disky
- Ujistěte se, že máte dostatečnou propustnost VOPS a V/O nakonfigurovanou s konfigurací disku.
- Zkombinujte svazek /hana/data a /hana/log, abyste měli nákladově efektivní konfiguraci úložiště


#### <a name="sql-server-as-dbms"></a>SQL Server jako DBMS
Pokud nasazujete SQL Server jako DBMS pro Business One, přejděte k dokumentu [NASAZENÍ DBMS virtuálních počítačů SQL Server Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Odhady hrubých velikostí pro stranu DBMS pro SQL Server jsou:

| Počet uživatelů | Virtuální procesory | Memory (Paměť) | Příklad typů virtuálních počítačů |
| --- | --- | --- | --- |
| až 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| až 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| až 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| až 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Velikost i výše uvedená velikost by měla poskytnout představu, kde začít. Je možné, že budete potřebovat méně nebo více prostředků, v takovém případě je snadné přizpůsobení na azure. Změna mezi typy virtuálních počítačů je možné pouze restartování virtuálního počítače.

#### <a name="sap-hana-as-dbms"></a>SAP HANA jako DBMS
Pomocí SAP HANA jako DBMS následující části byste měli postupovat podle úvah dokumentu [SAP HANA na provozní průvodce Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Pro vysokou dostupnost a zotavení po havárii konfigurace kolem SAP HANA jako databáze pro Business One v Azure, měli byste si přečíst dokumentaci [SAP HANA vysokou dostupnost pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) a dokumentaci ukázal z tohoto dokumentu.

Pro sap hana zálohování a obnovení strategie, měli byste si přečíst [dokument Zálohování průvodce PRO SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) a dokumentace poukázal z tohoto dokumentu.

 
### <a name="business-one-client-server"></a>Klientský server Business One
Pro tyto součásti aspekty úložiště nejsou primární problém. nicméně, chcete mít spolehlivou platformu. Proto byste měli použít Azure Premium Storage pro tento virtuální počítač, i pro základní virtuální pevný disk. Dimenzování virtuálního virtuálního provozu s daty uvedenými v [průvodci SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Pro Azure je potřeba se zaměřit a vypočítat s požadavky uvedenými v kapitole 2.4 dokumentu. As you calculate the requirements, you need to compare them against the following documents to find the ideal VM for you:

- [Velikosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Sap Poznámka #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Porovnejte počet procesorů a paměti potřebné k tomu, co je dokumentováno společností Microsoft. Při výběru virtuálních připojení mějte také na paměti propustnost sítě.








