---
title: Integrita infrastruktury Azure
description: Tento článek řeší integritu infrastruktury Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727192"
---
# <a name="azure-infrastructure-integrity"></a>Integrita infrastruktury Azure

## <a name="software-installation"></a>Instalace softwaru
Všechny součásti v zásobníku softwaru, které jsou nainstalované v prostředí Azure, jsou vlastní vytvořené podle procesu životního cyklu vývoje zabezpečení microsoftu (SDL). Všechny softwarové součásti, včetně bitových kopií operačního systému (OS) a databáze SQL, jsou nasazeny jako součást procesu správy změn a správy verzí. Operační systém spuštěný na všech uzlech je přizpůsobená verze systému Windows Server 2008 nebo Windows Server 2012. Přesná verze je zvolena řadičem tkaniny (FC) podle role, kterou má pro operační systém hrát. Kromě toho hostitelský operační systém neumožňuje instalaci neautorizovaných softwarových součástí.

Některé součásti Azure se nasazují jako zákazníci Azure na hostovaném virtuálním počítači spuštěném na hostovaném osu.

## <a name="virus-scans-on-builds"></a>Virové skeny na sestaveních
Sestavení softwarové součásti Azure (včetně operačního systému) musí podstoupit antivirovou ochranu proti virům. Každá kontrola virů vytvoří protokol v přidruženém adresáři sestavení s podrobnostmi o tom, co bylo zkontrolováno, a o výsledcích prohledávání. Kontrola virů je součástí zdrojového kódu sestavení pro každou komponentu v rámci Azure. Kód není přesunut do produkčního prostředí bez čistého a úspěšného vyhledávání virů. Pokud jsou zaznamenány nějaké problémy, sestavení je zmrazena a pak přejde do týmu zabezpečení v rámci zabezpečení microsoft zjistit, kde "rogue" kód zadal sestavení.

## <a name="closed-and-locked-environment"></a>Uzavřené a uzamčené prostředí
Ve výchozím nastavení uzly infrastruktury Azure a hostované virtuální počítače nemají uživatelské účty vytvořené na nich. Kromě toho jsou zakázány také výchozí účty správce systému Windows. Správci z podpory Azure live se můžou při správném ověřování přihlásit do těchto počítačů a spravovat produkční síť Azure pro nouzové opravy.

## <a name="azure-sql-database-authentication"></a>Ověřování Azure SQL Database
Stejně jako u jakékoli implementace SQL Server, správa uživatelských účtů musí být přísně kontrolovány. Azure SQL Database podporuje jenom ověřování serveru SQL Server. Jako doplněk modelu zabezpečení dat zákazníka by měly být použity také uživatelské účty se silnými hesly a nakonfigurovanými konkrétními právy.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Seznamy AV a brány firewall mezi podnikovou sítí Microsoftu a clusterem Azure
Seznamy řízení přístupu (ACL) a brány firewall mezi platformou služby a podnikovou sítí společnosti Microsoft chrání instance databáze SQL před neoprávněným přístupem zasvěcených osob. Ke koncovému bodu správy platformy Windows Fabric mají přístup pouze uživatelé z ip adres z podnikové sítě Microsoft.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Seznamy ALOK a brány firewall mezi uzly v clusteru databáze SQL
Jako další ochranu jako součást strategie hloubky ochrany byly implementovány seznamy ACA a brána firewall mezi uzly v clusteru databáze SQL. Veškerá komunikace uvnitř clusteru platformy Windows Fabric, stejně jako veškerý spuštěný kód, je důvěryhodná.

## <a name="custom-monitoring-agents"></a>Vlastní monitorovací agenti
SQL Database využívá vlastní agenti monitorování (MAs), také volal watchdogs, ke sledování stavu clusteru databáze SQL.

## <a name="web-protocols"></a>Webové protokoly

### <a name="role-instance-monitoring-and-restart"></a>Monitorování a restartování instance role
Azure zajišťuje, že všechny nasazené, spuštěné role (webové nebo back-endové role pracovních procesů) podléhají trvalému monitorování stavu, aby bylo zajištěno, že efektivně a efektivně poskytují služby, pro které byly zřízeny. Pokud se role stane nefunkční, buď kritickou chybou v aplikaci, která je hostována, nebo problémem základní konfigurace v rámci samotné instance role, fc zjistí problém v rámci instance role a iniciuje stav nápravy.

### <a name="compute-connectivity"></a>Výpočetní připojení
Azure zajišťuje, že nasazená aplikace nebo služba je dostupná prostřednictvím standardních webových protokolů. Virtuální instance internetových webových rolí mají externí připojení k internetu a jsou dostupné přímo uživatelům webu. K ochraně citlivosti a integrity operací, které role pracovního procesu provádějí jménem veřejně přístupných virtuálních instancí webových rolí, mají virtuální instance rolí pracovníků back-end zpracování externí připojení k Internetu, ale nelze je přístup přímo externími uživateli webových stránek.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
