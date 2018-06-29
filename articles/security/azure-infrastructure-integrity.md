---
title: Integritu infrastruktury Azure
description: Tento článek řeší integritu infrastruktury Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 18d7fab30c0bbaa5292fe5d3003b7f2309b34d3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102169"
---
# <a name="integrity-of-azure-infrastructure"></a>Integritu infrastruktury Azure   

## <a name="software-installation"></a>Instalace softwaru
Všechny součásti v zásobníku softwaru, které jsou nainstalovány v prostředí Azure jsou vlastním následující proces životního cyklu SDL (Security Development) společnosti Microsoft. Všechny součásti softwaru (včetně bitové kopie operačního systému a SQL Database) jsou nasazeny jako součást procesu změn a správy verzí. Operační systém, který běží na všech uzlech je přizpůsobená verze systému Windows Server 2008 nebo Windows Server 2012. Přesnou verzi je zvolen podle FC podle role zamýšlené pro operační systém k přehrání. Kromě toho hostitelským operačním systémem neumožňuje instalaci všech součástí neoprávněného softwaru.

Některé součásti Microsoft Azure (například RDFE, portál pro vývojáře, atd.) jsou nasazeny jako Azure zákazníků na virtuálním počítači hosta systémem hostovaného operačního systému.

## <a name="virus-scans-on-builds"></a>Antivirové kontroly na sestavení
Sestavení součásti (včetně operačního systému) Azure softwaru muset projít antivirovou kontrolu pomocí nástroje Microsoft Endpoint Protection (MEP) antivirový. Každý antivirovou kontrolu vytvoří protokolu v adresáři přidružené sestavení s podrobnostmi o tom, co byla provedena a výsledky kontroly. Kontrola virů je součástí zdrojového kódu sestavení pro každou součást v rámci Azure. Kód nebudou přesunuta do produkčního prostředí bez nutnosti vyčištění a úspěšné antivirové kontroly. Pokud jsou všechny problémy uvedené, sestavení není aktivní a pak přejde na týmy zabezpečení v rámci Microsoft Security k identifikaci, kdy byl zadán kód "podvodný" sestavení.

## <a name="closedlocked-environment"></a>Uzavřený uzamčení prostředí
Ve výchozím nastavení nemají uzly infrastruktury Azure a virtuální počítače hostované všechny uživatelské účty vytvořené na ně. Kromě toho jsou zakázány výchozí účty správce systému Windows. Správci z Microsoft Azure za provozu podporu (WALS) může – s správné ověření – přihlášení do těchto počítačů a spravovat Azure produkční sítě pro nouzový opravy.

## <a name="microsoft-azure-sql-database-authentication"></a>Ověřování Microsoft Azure SQL Database
Stejně jako u jakékoli použití systému SQL Server, musí podléhat důkladné kontrole Správa uživatelských účtů. Microsoft Azure SQL Database podporuje jenom ověřování systému SQL Server. Uživatelské účty s silná hesla a nakonfigurovaná s konkrétní práva se má použít také k doplňují model zabezpečení dat zákazníka.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Brány firewall nebo seznamy ACL mezi MSFT CorpNet a Cluster Microsoft Azure
Seznamy ACL nebo brány Firewall mezi služby platformy a podnikové síti MS ochránit před neoprávněným zevnitř přístup Microsoft Azure SQL Database. Navíc přístup pouze uživatelé z rozsahů IP adres z Microsoft CorpNet koncový bod WinFabric platformy správy.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Brány firewall nebo seznamy ACL mezi uzly v clusteru služby Azure SQL DB
Jako další ochranu, jako součást obrany v hloubka strategie je implementovaná seznamy ACL nebo brány Firewall mezi uzly v clusteru s podporou služby Microsoft Azure SQL DB. Veškerá komunikace uvnitř WinFabric platformy clusteru, a také všechny spuštěním kódu je důvěryhodný.

## <a name="custom-mas-watchdogs"></a>Vlastní MAs (Watchdogs)
Microsoft Azure SQL Database využívá vlastní MAs názvem watchdogs tak, aby monitorovala Microsoft Azure SQL DB clusteru.

## <a name="web-protocols"></a>Webových protokolů

### <a name="role-instance-monitoring-and-restart"></a>Monitorování instance role a restartování
Azure zajistí, že všechny spuštěné role (internetové webové nebo rolí pracovního procesu zpracování back-end), nasazení se vztahují dlouhodobě stavu monitorování zjistí, že se efektivně a efektivně poskytování služeb, ve kterých jste byla zřízený. V případě, že role se změní na není v pořádku, podle buď kritickou chybu v aplikaci se problém hostované nebo základní konfiguraci v rámci samotné instanci role, bude Microsoft Azure FC zjištění problému v rámci role instance a inicializaci opravné stavu .

### <a name="compute-connectivity"></a>Výpočetní připojení
Azure zajistí, že bude dostupné prostřednictvím standardních protokolů webové aplikace nebo služby nasazené. Internetové webové role virtuální instance bude mít externí připojení k Internetu a bude přímo dosažitelná webovým uživatelům. Virtuální instance rolí back-end zpracování pracovního procesu mají externí připojení k Internetu, ale nelze přistupovat přímo pomocí uživatel s externí webový ochráníte velkých a malých písmen a integritu operace, které role pracovního procesu provádět jménem veřejně přístupný webový virtuální instance role.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
