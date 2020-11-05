---
title: SAP Business One na Azure Virtual Machines | Microsoft Docs
description: SAP Business One v Azure.
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: b076045f6a106f77717159d3095ffd5973ab27aa
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359880"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One ve službě Azure Virtual Machines
Tento dokument poskytuje pokyny k nasazení SAP Business One na Azure Virtual Machines. Dokumentace není náhradou pro instalaci aplikace Business One for SAP. Dokumentace by se měla týkat základních pokynů pro plánování a nasazení infrastruktury Azure pro provozování obchodních aplikací v jednom.

Podnik One podporuje dvě různé databáze:
- SQL Server – viz téma [SAP Note #928839 – plánování vydání pro Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA – pro SAP HANA je k dispozici pouze jedna matice podpory SAP Business One, rezervuje se [matice dostupnosti produktu SAP](https://support.sap.com/pam) .

V případě SQL Server platí základní požadavky na nasazení, jak je popsáno v [nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](./dbms_guide_general.md) . v případě SAP HANA se v tomto dokumentu zmiňují doporučení.

## <a name="prerequisites"></a>Požadavky
K použití tohoto průvodce potřebujete základní znalosti následujících součástí Azure:

- [Virtuální počítače Azure ve Windows](../../windows/tutorial-manage-vm.md)
- [Virtuální počítače Azure v systému Linux](../../linux/tutorial-manage-vm.md)
- [Správa sítí a virtuálních sítí Azure pomocí PowerShellu](../../windows/tutorial-virtual-network.md)
- [Sítě a virtuální sítě Azure s rozhraním CLI](../../linux/tutorial-virtual-network.md)
- [Správa disků v Azure pomocí Azure CLI](../../linux/tutorial-manage-disks.md)

I v případě, že máte zájem o podnikání jenom jednou, dokument [plánování a implementace Azure Virtual Machines pro SAP NetWeaver](./planning-guide.md) může být dobrým zdrojem informací.

Předpokladem je, že jako instance nasazujete SAP Business One:

- Seznámení s instalací SAP HANA v dané infrastruktuře, jako je třeba virtuální počítač
- Známou instalaci aplikace SAP Business One na infrastrukturu, jako jsou virtuální počítače Azure
- Seznamte se s operačním systémem SAP Business One a vybraným systémem DBMS.
- Seznámení s nasazením infrastruktury v Azure

Všechny tyto oblasti nebudou zahrnuty v tomto dokumentu.

Kromě dokumentace k Azure byste měli znát hlavní poznámky ke službě SAP, které odkazují na firmu One nebo které jsou centrálními poznámkami od SAP pro firmy One:

- [528296 – obecná Poznámka k přehledu SAP Business One Release a souvisejících produktů](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 – aktualizace vydané verze poznámky pro SAP Business One 9,2, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 – střední Poznámka pro SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 – aktualizace vydané verze poznámky pro SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 – kolektivní Poznámka pro SAP Business One 9,3 Obecné problémy](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 – kolektivní poznámková Poznámka pro SAP HANA-Related témata týkající se SAP Business One, verze pro SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Podniková jedna architektura
Obchodní jedna je aplikace, která má dvě úrovně:

- Vrstva klienta s klientem systému souborů FAT
- Databázová vrstva, která obsahuje schéma databáze pro tenanta

Lepší přehled o tom, které součásti jsou spuštěny v části klienta a které části jsou spuštěny v části serveru, jsou popsány v článku [SAP Business One – Příručka správce](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) . 

Vzhledem k tomu, že mezi vrstvou klienta a vrstvou systému DBMS dochází k vysoké latenci, je potřeba, aby při nasazení v Azure byly obě úrovně v Azure umístěné v Azure. obvykle je to, že uživatelé pak mají k jednomu nebo několika virtuálním počítačům, na kterých je spuštěná služba RDS, jednu z komponent klienta.

### <a name="sizing-vms-for-sap-business-one"></a>Změna velikosti virtuálních počítačů pro SAP Business One

V souvislosti s určením velikosti virtuálních počítačů klientů jsou požadavky na prostředky zdokumentovány v tématu SAP v dokumentu [SAP Business One hardware – Průvodce požadavky](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). V případě Azure se musíte zaměřit a počítat s požadavky uvedenými v kapitole 2,4 dokumentu.

Jako virtuální počítače Azure pro hostování firemních komponent a hostitele DBMS jsou povolené jenom virtuální počítače, které jsou podporované SAP NetWeaver. Pokud chcete najít seznam podporovaných virtuálních počítačů Azure NetWeaver, přečtěte [si poznámku SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Provozování SAP HANA jako systému DBMS back-endu pro firmy – pro HANA se podporují jenom virtuální počítače, které jsou uvedené pro firmy v HANA v [seznamu platforem certifikovaných IaaS Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) . Toto silnější omezení pro SAP HANA jako systém DBMS nemá vliv na obchodní komponenty na jeden klient.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Verze operačních systémů, které se mají použít pro SAP Business One

V zásadě je vždy nejvhodnější použít nejnovější verze operačních systémů. Zejména v prostoru Linux se nové funkce Azure představily s různými novějšími podverzemi SUSE a Red Hat. Na straně Windows se důrazně doporučuje použití Windows serveru 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Nasazení infrastruktury v Azure pro SAP Business One
V několika dalších kapitolách se infrastruktura týkají nasazení SAP.

### <a name="azure-network-infrastructure"></a>Síťová infrastruktura Azure
Síťová infrastruktura, kterou potřebujete nasadit v Azure, závisí na tom, jestli pro sebe nasadíte jeden podnikový systém. Nebo bez ohledu na to, jestli jste hostitel, který hostuje spoustu obchodních systémů pro zákazníky. V návrhu se taky můžou mírně měnit informace o tom, jestli se k Azure připojujete. Procházejí různé možnosti, jeden návrh, kde máte připojení VPN do Azure a kde rozšiřujete službu Active Directory prostřednictvím [sítě VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../../../expressroute/expressroute-introduction.md) do Azure.

![Jednoduchá konfigurace sítě s jedním podnikem](./media/business-one-azure/simple-network-with-VPN.PNG)

Uvedená zjednodušená konfigurace zavádí několik instancí zabezpečení, které umožňují řídit a omezit směrování. Začíná na 

- Směrovač nebo brána firewall na straně zákazníka na místním zařízení.
- Další instancí je [Skupina zabezpečení sítě Azure](../../../virtual-network/network-security-groups-overview.md) , kterou můžete použít k zavedení pravidel směrování a zabezpečení pro virtuální síť Azure, na které spouštíte podnikovou konfiguraci SAP v nástroji.
- Aby se zabránilo tomu, že uživatelé firmy můžou také vidět Server, na kterém běží podnikový jeden server, na kterém běží databáze, měli byste oddělit virtuální počítač, který je hostitelem jednoho klienta, a podnikový jeden server ve dvou různých podsítích v rámci virtuální sítě.
- Pro omezení přístupu k podnikovému jednomu serveru byste měli znovu použít službu Azure NSG přiřazenou ke dvěma různým podsítím.

Propracovanější verze konfigurace sítě Azure je založená na osvědčených postupech Azure v rámci [architektury hub a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Vzor architektury hub a paprsek by změnil první zjednodušenou konfiguraci tak, aby vypadala takto:


![Konfigurace hub a paprsků s podnikovým jedním](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

V případě, že se uživatelé připojují přes Internet bez privátního připojení do Azure, musí být návrh sítě v Azure v souladu se zásadami popsanými v referenční architektuře Azure pro [DMZ mezi Azure a internetem](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Podnikový jeden databázový server
Pro typ databáze jsou k dispozici SQL Server a SAP HANA. Nezávisle na systému DBMS, měli byste si přečíst dokumentaci dokumentu [k nasazení azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md) a získat obecné informace o nasazeních DBMS ve virtuálních počítačích Azure a souvisejících tématech o sítích a úložištích.

I když se v konkrétních a obecných databázových dokumentech již zvýrazňuje, měli byste se seznámit s těmito kroky:

- [Správa dostupnosti virtuálních počítačů s Windows v Azure](../../manage-availability.md) a [Správa dostupnosti virtuálních počítačů se systémem Linux v Azure](../../manage-availability.md)
- [SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Tyto dokumenty by vám měly pomáhat při rozhodování o výběru typů úložiště a konfiguraci vysoké dostupnosti.

V zásadě byste měli:

- Použijte Premium SSD oproti standardu HDD. Další informace o dostupných typech disků najdete v našem článku [Výběr typu disku](../../disks-types.md) .
- Použití Azure Managed disks na nespravovaných discích
- Ujistěte se, že máte dostatečný počet IOPS a propustnost vstupně-výstupních operací nakonfigurovaných s konfigurací disku.
- Pokud chcete mít cenově výhodnější konfiguraci úložiště, zkombinujte/Hana/data a/Hana/log svazek.


#### <a name="sql-server-as-dbms"></a>SQL Server jako DBMS
Pokud chcete nasadit SQL Server jako DBMS pro firmy, přečtěte si dokument [SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](./dbms_guide_sqlserver.md). 

Přibližné odhady velikosti pro SQL Server pro systém DBMS jsou následující:

| Počet uživatelů | Virtuální procesory | Memory (Paměť) | Příklady typů virtuálních počítačů |
| --- | --- | --- | --- |
| až 20 | 4 | 16 GB | D4s_v3 E4s_v3 |
| až 40 | 8 | 32 GB | D8s_v3 E8s_v3 |
| až 80 | 16 | 64 GB | D16s_v3 E16s_v3 |
| až 150 | 32 | 128 GB | D32s_v3 E32s_v3 |

Výše uvedená velikost by měla poskytnout představu o tom, kde začít s. Je možné, že potřebujete méně nebo více prostředků. v takovém případě je to snadné. Změna mezi typy virtuálních počítačů je možná pouhým restartováním virtuálního počítače.

#### <a name="sap-hana-as-dbms"></a>SAP HANA jako DBMS
Použití SAP HANA jako DBMS v následujících částech byste měli postupovat podle pokynů v dokumentu [SAP HANA v provozní příručce Azure](./hana-vm-operations.md).

V případě konfigurace pro vysokou dostupnost a zotavení po havárii v rámci služby SAP HANA jako databáze pro firmy v Azure byste si měli přečíst dokumentaci [SAP HANA vysoké dostupnosti pro virtuální počítače Azure](./sap-hana-availability-overview.md) a dokumentaci, na kterou se odkazuje z tohoto dokumentu.

Pokud SAP HANA strategie zálohování a obnovení, měli byste si přečíst [příručku k zálohování dokumentů pro SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md) a dokumentaci, na kterou se odkazuje z tohoto dokumentu.

 
### <a name="business-one-client-server"></a>Podnikový jeden klientský server
Pro tyto součásti se nejedná o zásadní obavy týkající se úložiště. Nicméně chcete mít spolehlivou platformu. Proto byste pro tento virtuální počítač měli použít Azure Premium Storage, a to i pro základní virtuální pevný disk. Velikost virtuálního počítače nastavujte pomocí dat uvedených v tématu [Průvodce požadavky SAP podniku na hardware](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). V případě Azure se musíte zaměřit a počítat s požadavky uvedenými v kapitole 2,4 dokumentu. Při výpočtu požadavků je třeba je porovnat s následujícími dokumenty a najít ideální virtuální počítač pro vás:

- [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md)
- [Poznámka ke SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Porovnejte počet procesorů a paměti, které je třeba zdokumentovat od Microsoftu. Při volbě virtuálních počítačů si taky Pamatujte na propustnost sítě.
