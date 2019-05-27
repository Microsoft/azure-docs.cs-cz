---
title: Přehled služby Azure Blockchain Workbench
description: Přehled služby Azure Blockchain Workbench a jejích funkcí
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/20/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 16e2497f4e26d16c008161477fd7d573e59e84db
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955202"
---
# <a name="what-is-azure-blockchain-workbench"></a>Co je Azure Blockchain Workbench?

Azure Blockchain Workbench je kolekce služeb a funkcí Azure navržených tak, aby vám pomohly vytvořit a nasadit blockchainové aplikace, které umožňují sdílet obchodní procesy a data s jinými organizacemi. Azure Blockchain Workbench umožňuje generovat infrastrukturu potřebnou k sestavení blockchainových aplikací, která vývojářům umožňuje zaměřit se na vytváření obchodní logiky a tzv. „chytrých kontraktů“. Dále usnadňuje tvorbu blockchainových aplikací tím, že integruje různé služby a funkce Azure, které pomáhají automatizovat nejčastější úkoly při vývoji.

## <a name="create-blockchain-applications"></a>Vytváření blockchainových aplikací

Blockchain Workbench umožňuje definovat blockchainové aplikace pomocí konfigurace a psaním kódu chytrých kontraktů. Můžete rovnou začít vyvíjet blockchainovou aplikaci a soustředit se na definování kontraktu a napsání obchodní logiky místo na generování uživatelského rozhraní a nastavování podpůrných služeb.

## <a name="manage-applications-and-users"></a>Správa aplikací a uživatelů

Azure Blockchain Workbench nabízí webovou aplikaci a rozhraní REST API, které slouží ke správě blockchainových aplikací a uživatelů. Správci služby Blockchain Workbench mohou spravovat přístup k aplikacím a přiřazovat uživatelům role aplikace. Uživatelé Azure AD jsou automaticky mapováni na členy aplikace.

## <a name="integrate-blockchain-with-applications"></a>Integrace blockchainu do aplikací

Rozhraní REST API služby Blockchain Workbench a rozhraní API založená na zprávách můžete integrovat do stávajících systémů. Rozhraní API umožňují nahradit nebo použít technologie více distribuovaných registrů, nabízených úložišť a databází.

Blockchain Workbench mění zprávy, odeslané rozhraní API založenému na zprávách, na vytvořené transakce ve formátu, který očekává blockchainové nativní rozhraní API.  Služba Workbench přihlašuje a přesměrovává transakce na příslušný blockchain. 

Workbench automaticky poskytuje události službám Service Bus a Event Grid, které posílají zprávy podřízeným spotřebitelům. Vývojáři můžou integrovat kterýkoli z těchto systémů zasílání zpráv, aby mohli řídit transakce a prohlížet výsledky.

## <a name="deploy-a-blockchain-network"></a>Nasazení blockchainové sítě

Azure Blockchain Workbench zjednodušuje nastavení síťového blockchainového konsorcia ve formě předem nakonfigurovaného řešení využívajícího šablonu řešení Azure Resource Manageru. Šablona usnadňuje nasazení, protože umožňuje nasadit všechny součásti potřebné k provozu konsorcia. Dnes Blockchain Workbench podporuje projekt Ethereum.

## <a name="use-active-directory"></a>Použít službu Active Directory

U stávajících blockchainových protokolů jsou blockchainové identity vyjádřeny jako adresy v síti. Azure Blockchain Workbench přidruží blockchainovou identitu k identitě v Active Directory. To usnadňuje tvorbu podnikových aplikací, které využívají identity z Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Synchronizace zřetězených dat s nezřetězeným úložištěm

Azure Blockchain Workbench usnadňuje analýzu blockchainových událostí a dat tím, že automaticky synchronizuje blockchainová data s nezřetězeným úložištěm. Místo extrahování dat přímo z blockchainu můžete vytvářet dotazy do nezřetězených databázových systémů, jako je SQL Server. Blockchain zkušenosti se pro koncové uživatele, kteří provádějí úlohy analýzy dat nevyžaduje.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Architektura Azure Blockchain Workbench](architecture.md)
