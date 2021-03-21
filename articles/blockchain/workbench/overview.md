---
title: Přehled služby Azure blockchain Workbench Preview
description: Přehled služby Azure blockchain Workbench Preview a jejích možností.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87003139"
---
# <a name="what-is-azure-blockchain-workbench"></a>Co je Azure Blockchain Workbench?

Azure blockchain Workbench Preview je kolekce služeb a funkcí Azure, které vám pomůžou vytvářet a nasazovat aplikace blockchain pro sdílení obchodních procesů a dat s jinými organizacemi. Azure Blockchain Workbench umožňuje generovat infrastrukturu potřebnou k sestavení blockchainových aplikací, která vývojářům umožňuje zaměřit se na vytváření obchodní logiky a tzv. „chytrých kontraktů“. Dále usnadňuje tvorbu blockchainových aplikací tím, že integruje různé služby a funkce Azure, které pomáhají automatizovat nejčastější úkoly při vývoji.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Vytváření blockchainových aplikací

Blockchain Workbench umožňuje definovat blockchainové aplikace pomocí konfigurace a psaním kódu chytrých kontraktů. Můžete rovnou začít vyvíjet blockchainovou aplikaci a soustředit se na definování kontraktu a napsání obchodní logiky místo na generování uživatelského rozhraní a nastavování podpůrných služeb.

## <a name="manage-applications-and-users"></a>Správa aplikací a uživatelů

Azure Blockchain Workbench nabízí webovou aplikaci a rozhraní REST API, které slouží ke správě blockchainových aplikací a uživatelů. Správci služby Blockchain Workbench mohou spravovat přístup k aplikacím a přiřazovat uživatelům role aplikace. Uživatelé Azure AD jsou automaticky mapováni na členy aplikace.

## <a name="integrate-blockchain-with-applications"></a>Integrace blockchainu do aplikací

Rozhraní REST API služby Blockchain Workbench a rozhraní API založená na zprávách můžete integrovat do stávajících systémů. Rozhraní API umožňují nahradit nebo použít technologie více distribuovaných registrů, nabízených úložišť a databází.

Blockchain Workbench může transformovat zprávy odeslané do svého rozhraní API založené na zprávách pro vytváření transakcí ve formátu očekávaném nativním rozhraním API této blockchain.  Služba Workbench přihlašuje a přesměrovává transakce na příslušný blockchain. 

Workbench automaticky poskytuje události službám Service Bus a Event Grid, které posílají zprávy podřízeným spotřebitelům. Vývojáři můžou integrovat kterýkoli z těchto systémů zasílání zpráv, aby mohli řídit transakce a prohlížet výsledky.

## <a name="deploy-a-blockchain-network"></a>Nasazení blockchainové sítě

Azure Blockchain Workbench zjednodušuje nastavení síťového blockchainového konsorcia ve formě předem nakonfigurovaného řešení využívajícího šablonu řešení Azure Resource Manageru. Šablona usnadňuje nasazení, protože umožňuje nasadit všechny součásti potřebné k provozu konsorcia. Dnes Blockchain Workbench podporuje projekt Ethereum.

## <a name="use-active-directory"></a>Použít Active Directory

U stávajících blockchainových protokolů jsou blockchainové identity vyjádřeny jako adresy v síti. Azure Blockchain Workbench přidruží blockchainovou identitu k identitě v Active Directory. To usnadňuje tvorbu podnikových aplikací, které využívají identity z Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Synchronizace zřetězených dat s nezřetězeným úložištěm

Azure Blockchain Workbench usnadňuje analýzu blockchainových událostí a dat tím, že automaticky synchronizuje blockchainová data s nezřetězeným úložištěm. Místo extrahování dat přímo z blockchainu můžete vytvářet dotazy do nezřetězených databázových systémů, jako je SQL Server. Pro koncové uživatele, kteří provádějí úlohy analýzy dat, se blockchain odbornosti nevyžaduje.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky ke službě Azure blockchain News najdete na [blogu Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/) , abyste měli přehled o nabídkách služeb blockchain a informacích od týmu Azure blockchain Engineering.

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain.

* [Microsoft Q&Stránka s otázkou pro Azure blockchain Workbench](/answers/topics/azure-blockchain-workbench.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Architektura Azure Blockchain Workbench](architecture.md)
