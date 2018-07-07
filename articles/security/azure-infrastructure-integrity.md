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
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901312"
---
# <a name="azure-infrastructure-integrity"></a>Integrity infrastruktury Azure

## <a name="software-installation"></a>Instalace softwaru
Všechny součásti v zásobníku softwaru, které jsou nainstalované ve prostředí Azure jsou vlastní vytvořené po procesu Security Development Lifecycle (SDL) od Microsoftu. Všechny součásti softwaru (včetně bitové kopie operačního systému a SQL Database) jsou nasazeny jako součást procesu změny a produktem Release Management. Operační systém, na kterém běží na všech uzlech je přizpůsobená verze systému Windows Server 2008 nebo Windows Server 2012. Přesnou verzi je vybráno FC podle role, kterou se chystá pro operační systém k přehrání. Kromě toho hostitelského operačního systému nepovoluje instalace všech komponent neoprávněného softwaru.

Některé komponenty Microsoft Azure (například RDFE, portál pro vývojáře, atd.) jsou nasazeny jako zákazníků Azure běží na operačním systému hosta virtuálního počítače hosta.

## <a name="virus-scans-on-builds"></a>Antivirové kontroly v Buildech
Azure softwarová komponenta (včetně operačního systému) sestavení musí projít antivirovou kontrolu pomocí nástroje Microsoft Endpoint Protection (MEP) antivirový program. Každý virů vytvoří protokolu v adresáři přidružené sestavení s podrobnostmi o co byla provedena a výsledky kontroly. Vyhledávání virů je součástí sestavení zdrojový kód pro všechny komponenty v rámci Azure. Kód nebudou přesunuta do produkčního prostředí bez nutnosti vyčištění a úspěšné antivirové kontroly. Pokud existují nějaké problémy, které jste si poznamenali, sestavení je zmrazen a pak přejdete na zabezpečení týmy v rámci Microsoft Security identifikovat, ve kterém byl zadán kód "podvodný" sestavení.

## <a name="closedlocked-environment"></a>Zavření/uzamčen prostředí
Ve výchozím nastavení nemají Azure infrastruktury uzly a virtuálních počítačů hosta všechny uživatelské účty vytvořené na ně. Kromě toho jsou zakázané taky účty správců výchozí Windows. Správci z Microsoft Azure za podporu (WALS) můžete – s správné ověření – Přihlaste se do těchto počítačů a spravovat Azure produkční sítě pro nouzových oprav.

## <a name="microsoft-azure-sql-database-authentication"></a>Ověřování Microsoft Azure SQL Database
Stejně jako u jakékoli použití systému SQL Server, musí být podléhat důkladné kontrole Správa uživatelských účtů. Microsoft Azure SQL Database podporuje jenom ověřování systému SQL Server. Uživatelské účty s silná hesla a nakonfigurovaná s konkrétní práva, by měla sloužit i k doplnění model zabezpečení dat zákazníka.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Brána firewall/ACL mezi MSFT podnikové sítě a clusteru Microsoft Azure
Seznamy ACL nebo brána Firewall mezi MS podnikové sítě a služby platformy Microsoft Azure SQL Database chránit insider neoprávněným přístupem. Kromě toho přístup pouze uživatelé z rozsahy IP adres z Microsoft CorpNet koncový bod WinFabric platformy správy.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Brána firewall/ACL mezi uzly v clusteru služby Azure SQL DB
Jako další ochranu, jako součást defense v hloubce strategie byly implementovány seznamy ACL nebo brána Firewall mezi uzly v clusteru s Microsoft Azure SQL DB. Veškerá komunikace uvnitř clusteru WinFabric platformy, stejně jako všechny spuštěním kódu je důvěryhodný.

## <a name="custom-mas-watchdogs"></a>Vlastní MAs (Watchdogs)
Microsoft Azure SQL Database využívá vlastní MAs volá watchdogs monitorovat stav clusteru služby Microsoft Azure SQL DB.

## <a name="web-protocols"></a>Webové protokoly

### <a name="role-instance-monitoring-and-restart"></a>Monitorování instancí rolí a restartování
Azure zajišťuje, že se všechny spuštěné role (internetové webové nebo role pracovních procesů back endové zpracování) nasazené můžou trvalý stav monitorování, můžete si ověřte, že jsou efektivní a efektivní poskytování služeb, ve kterých jste byla zřízena. V případě, že role nebude v pořádku, tak buď kritická chyba v aplikaci se problém hostované nebo základní konfiguraci v rámci samotné instanci role, Microsoft Azure FC zjistí problém v instanci role a spustit opravnou stav .

### <a name="compute-connectivity"></a>Konektivita služby COMPUTE
Azure tak zajišťuje jistotu, že je dostupné prostřednictvím standardních protokolů webové aplikace/služby nasazené. Virtuální instancemi webové role přístupem k Internetu bude mít externí připojení k Internetu a bude dostupný přímo prostřednictvím webových uživatelů. Virtuální instancí rolí pracovního procesu back endové zpracování mít externí připojení k Internetu, ale nelze přistupovat přímo pomocí uživateli s externí webový ochráníte tak citlivosti a integritu operací, které role pracovních procesů provádění jménem veřejně přístupný webový virtuální instance rolí.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Ochrany zákaznických dat v Azure](azure-protection-of-customer-data.md)
