---
title: Integrita infrastruktury Azure
description: Přečtěte si o integritě infrastruktury Azure a krocích, které Microsoft provede k zabezpečení, jako je třeba Kontrola virů na sestaveních softwarových komponent.
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
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543635"
---
# <a name="azure-infrastructure-integrity"></a>Integrita infrastruktury Azure

## <a name="software-installation"></a>Instalace softwaru
Všechny součásti v zásobníku softwaru, které jsou nainstalované v prostředí Azure, jsou vlastními sestavenými podle Microsoft Security Development Lifecycle (SDL)ho procesu. Všechny softwarové součásti, včetně bitových kopií operačního systému (OS) a SQL Database, se nasazují jako součást procesu správy změn a vydaných verzí. Operační systém, který běží na všech uzlech, je přizpůsobená verze Windows serveru 2008 nebo Windows Serveru 2012. Přesná verze je zvolena řadičem prostředků infrastruktury (FC) podle role, kterou zamýšlí spustit operační systém. Kromě toho hostitelský operační systém nepovoluje instalaci jakýchkoli neautorizovaných softwarových komponent.

Některé součásti Azure se nasazují jako zákazníci Azure na hostovaném VIRTUÁLNÍm počítači, který běží na hostovaném operačním systému.

## <a name="virus-scans-on-builds"></a>Kontroly virů na sestaveních
Sestavení softwarové součásti Azure (včetně operačního systému) musí projít kontrolou virů, která používá nástroj Endpoint Protection Anti-Antivirus. Každé vyhledávání virů vytvoří protokol v rámci přidruženého adresáře buildu, který podrobně vyhledal a výsledky kontroly. Vyhledávání virů je součástí zdrojového kódu sestavení pro každou součást v rámci Azure. Kód není přesunut do produkčního prostředí, aniž by byla provedena čistá a úspěšná kontrola virů. Pokud se vyskytnou nějaké problémy, sestavení se zablokuje a pak přejde do bezpečnostních týmů v rámci zabezpečení Microsoftu a určí, kde se do sestavení zadal neautorizovaný kód.

## <a name="closed-and-locked-environment"></a>Uzavřené a uzamčené prostředí
Ve výchozím nastavení se na uzlech infrastruktury Azure a virtuálních počítačích hosta nevytvořily uživatelské účty. Kromě toho jsou také zakázány výchozí účty správců systému Windows. Správci z Azure Live Support se můžou s řádným ověřováním přihlašovat do těchto počítačů a spravovat produkční síť Azure pro nouzové opravy.

## <a name="azure-sql-database-authentication"></a>Ověřování Azure SQL Database
Stejně jako u libovolné implementace SQL Server musí být Správa uživatelských účtů úzce řízená. Azure SQL Database podporuje pouze ověřování SQL Server. Aby bylo možné doplnit model zabezpečení dat zákazníka, je nutné použít také uživatelské účty se silnými hesly a nakonfigurovaná s konkrétními právy.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Seznamy ACL a brány firewall mezi podnikovou sítí Microsoft a clusterem Azure
Seznamy řízení přístupu (ACL) a brány firewall mezi platformou služby a podnikovou sítí Microsoftu chrání SQL Database instance před neautorizovaným přístupem Insider. Dále budou mít přístup k koncovému bodu správy platformy Windows Fabric jenom uživatelé z rozsahů IP adres z podnikové sítě Microsoftu.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Seznamy ACL a brány firewall mezi uzly v clusteru SQL Database
Jako dodatečná ochrana je v rámci strategie důkladné ochrany, seznamů ACL a brány firewall implementované mezi uzly v SQL Databasem clusteru. Veškerá komunikace uvnitř clusteru Windows Fabric Platform a veškerého běžícího kódu je důvěryhodná.

## <a name="custom-monitoring-agents"></a>Vlastní agenti monitorování
SQL Database využívá vlastní agenty monitorování (MAs), označované také jako sledovací zařízení, aby mohl monitorovat stav clusteru SQL Database.

## <a name="web-protocols"></a>Webové protokoly

### <a name="role-instance-monitoring-and-restart"></a>Monitorování instance role a restartování
Azure zajišťuje, aby všechny nasazené, spuštěné role (role pracovních procesů pro Internet nebo back-end) byly předmětem udržování monitorování stavu, aby bylo zajištěno, že budou efektivně a efektivně doručovat služby, pro které byly zřízeny. Pokud role nebude v pořádku, buď kritická chyba v aplikaci, která je hostována, nebo problém související s konfigurací v rámci samotné instance role, funkce FC detekuje problém v rámci instance role a zahájí opravný stav.

### <a name="compute-connectivity"></a>Připojení služby COMPUTE
Azure zajišťuje dostupnost nasazené aplikace nebo služby prostřednictvím standardních webových protokolů. Virtuální instance webových rolí s přístupem k Internetu mají externí připojení k Internetu a jsou dostupné přímo webovými uživateli. K ochraně citlivosti a integrity operací, které role pracovního procesu provádějí jménem veřejně přístupných virtuálních instancí webové role, mají virtuální instance rolí pracovních procesů back-endu externí připojení k Internetu, ale nemohou být přístupné přímo externím webovým uživatelům.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
