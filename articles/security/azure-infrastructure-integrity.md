---
title: Integrity infrastruktury Azure
description: Tento článek se zabývá integrity infrastruktury Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 65fe541f61389a2e52033cdaedcfcec4944faf35
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171455"
---
# <a name="azure-infrastructure-integrity"></a>Integrity infrastruktury Azure

## <a name="software-installation"></a>Instalace softwaru
Všechny součásti v zásobníku softwaru, které jsou nainstalované ve prostředí Azure jsou vlastní vytvořené po procesu Microsoft Security Development Lifecycle (SDL). Všechny součásti softwaru, včetně imagí operačního systému (OS) a SQL Database jsou nasazeny jako součást správy změn a správy proces vydávání verzí. Operační systém, na kterém běží na všech uzlech je přizpůsobená verze systému Windows Server 2008 nebo Windows Server 2012. Kontroler prostředků infrastruktury (FC) podle role, kterou se chystá pro operační systém pro přehrávání je vybráno přesnou verzi. Hostitelský operační systém neumožňuje instalace všech komponent neoprávněného softwaru.

Některé komponenty Azure jsou nasazeny jako zákazníků Azure hostovaný virtuální počítač běží na hostovaném operačním systému.

## <a name="virus-scans-on-builds"></a>Hledání virů v buildech
Sestavení součástí (včetně operačního systému) softwaru Azure mají podstoupit antivirovou kontrolu, který používá nástroj antivirové ochrany koncového bodu. Každý virů vytvoří protokolu v adresáři přidružené sestavení s podrobnostmi o co byla provedena a výsledky kontroly. Vyhledávání virů je součástí sestavení zdrojový kód pro všechny komponenty v rámci Azure. Kód nebyl přesunut do produkčního prostředí bez nutnosti vyčištění a úspěšné antivirové kontroly. Pokud jsou uvedené všechny problémy, sestavení je zmrazen a pokračující na zabezpečení týmy v rámci Microsoft Security identifikovat, ve kterém byl zadán kód "podvodný" sestavení.

## <a name="closed-and-locked-environment"></a>Prostředí zamknuté a uzavřené
Ve výchozím nastavení Azure infrastruktury uzly a virtuálních počítačů hosta nemají uživatelské účty vytvořené na ně. Kromě toho jsou zakázané taky účty správců výchozí Windows. Správce z Azure živé podpoře můžete, s správné ověření, přihlaste se do těchto počítačů a spravovat produkční sítě Azure pro nouzových oprav.

## <a name="azure-sql-database-authentication"></a>Ověřování služby Azure SQL Database
Stejně jako u jakékoli použití systému SQL Server, musí být podléhat důkladné kontrole Správa uživatelských účtů. Azure SQL Database podporuje jenom ověřování systému SQL Server. Doplnit model zabezpečení na základě dat, uživatelské účty s silná hesla a nakonfigurovaná s konkrétní práva by měla sloužit také.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Seznamy řízení přístupu a brány firewall mezi podnikovou síť společnosti Microsoft a Azure cluster
Seznamy řízení přístupu (ACL) a brány firewall mezi podnikovou síť společnosti Microsoft a služby platformy zabránit přístupu k neoprávněným insider instancí SQL Database. Kromě toho přístup pouze uživatelé z rozsahů IP adres z podnikové sítě Microsoft koncový bod správy platformy Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Seznamy řízení přístupu a brány firewall mezi uzly v clusteru SQL Database
Jako další ochranu, jako součást defense v hloubce strategie seznamy řízení přístupu a brána firewall je implementovaná mezi uzly v clusteru SQL Database. Veškerá komunikace uvnitř clusteru platformy Windows Fabric a také všechny spuštěním kódu je důvěryhodný.

## <a name="custom-monitoring-agents"></a>Vlastní agentů monitorování
SQL Database využívá vlastní agentů monitorování (MAs), také nazývané watchdogs, tak, aby monitorovala clusteru SQL Database.

## <a name="web-protocols"></a>Webové protokoly

### <a name="role-instance-monitoring-and-restart"></a>Monitorování instancí rolí a restartování
Azure zajišťuje, že všechna nasazení, spuštění role (internetové webové nebo role pracovních procesů back endové zpracování) jsou v souladu s monitorování k zajištění, že se efektivně a účinně poskytovat služby, u kterých jste byly zřízené trvalého stavu. Pokud nebude v pořádku, kritické chyby v aplikaci, která je hostovaná nebo příčinu problému konfigurace v rámci samotné instanci role, role, zjistí problém v instanci role FC a zahájí opravné stavu.

### <a name="compute-connectivity"></a>Konektivita služby COMPUTE
Azure zajišťuje, že nasazené aplikace nebo služba dostupné prostřednictvím standardních protokolů založených na webu. Virtuální instance webové role Internetová mít externí připojení k Internetu a jsou dostupná přímo z webových uživatelů. Pro ochranu utajení a integritu operací, které role pracovních procesů provádění jménem veřejně přístupný webový virtuální instance rolí, virtuální instance role pracovního procesu back endové zpracování mít externí připojení k Internetu, ale není možné přístup přímo uživatelé externí web.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
