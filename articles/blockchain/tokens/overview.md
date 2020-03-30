---
title: Co jsou tokeny Azure Blockchain
description: Azure Blockchain Tokens je platforma jako služba (PaaS) pro vystavování a správu tokenů.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240279"
---
# <a name="what-is-azure-blockchain-tokens"></a>Co je služba Azure Blockchain Tokens?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens je platforma jako služba (PaaS) pro standardizované vystavování a správu tokenů napříč knihami blockchainu v Azure.

Pomocí tokenů Azure Blockchain můžete vytvářet standardizované tokeny pro blockchainové řešení pomocí předem sestavené šablony tokenů. Můžete také sestavit vlastní šablonu tokenu pomocí služby. Po vytvoření použijte tokeny Azure Blockchain k připojení a vydání tokenů na blockchainu. Po vydání pak můžete spravovat tokeny v několika blockchainových sítích.

## <a name="templates"></a>Šablony

Pomocí tokenů Azure Blockchain vyberte předem vytvořenou šablonu tokenu nebo vytvořte vlastní šablonu tokenu. Tokeny Azure Blockchain podporují kompostovatelnost šablony tokenů, která umožňuje vytvořit vlastní šablonu tokenu na základě podporovaného chování. Šablony tokenů lze použít pro většinu blockchainových řešení, protože mapují na nejčastěji používané tokeny. Můžete začít se šablonou, přizpůsobit ji a nasadit tokeny pro vaše řešení.

Další informace o šablonách tokenů Azure Blockchain najdete v [tématu Šablony tokenů Azure Blockchain](templates.md).

## <a name="management"></a>Správa

Azure Blockchain Tokens poskytuje správu portálu Azure a API pro připojení k existující blockchainové síti. V současné době se můžete připojit ke [službě Azure Blockchain Service](../service/overview.md) nebo jinému rodinnému blockchainu Ethereum.

Po připojení k jedné nebo více blockchainovým sítím můžete pomocí api tokenů Azure Blockchain vydávat a spravovat tokeny pro použití ve vašem blockchainovém řešení. Pomocí api můžete integrovat správu tokenů ve vašich obchodních aplikacích a logice. Například můžete použít rozhraní REST API ke správě tokenů namísto správy tokenů přímo na blockchainu.

## <a name="blockchains-and-accounts"></a>Blockchainy a účty

Azure Blockchain Tokens poskytují správu portálu Azure a API pro vytváření nových skupin a nových blockchainových účtů v připojených blockchainových sítích. Můžete vytvářet nové účty přímo v připojených sítích a tokeny Azure Blockchain spravuje vaše soukromé klíče účtu vaším jménem. Pomocí skupin můžete seskupit různé blockchainové účty z více sítí a spravovat řízení přístupu prostřednictvím skupin.

Další informace o správě účtů tokenů Azure Blockchain najdete v tématu [Správa účtů tokenů Azure Blockchain](account-management.md).

## <a name="token-taxonomy-framework"></a>Architektura taxonomie tokenu

Tokeny Azure Blockchain jsou postavené na základě standardů s názvem Token Taxonomy Framework (TTF). TTF je sada dodávek vytvořených z tokenové skupiny [Token Taxonomy Initiative (Token Taxonomy Initiative).](https://entethalliance.org/participate/token-taxonomy-initiative/) Pracovní skupina TTI definuje obchodní taxonomii pro tokeny a jejich chování, které lze použít ve všech hlavních účetních knihách, včetně etherea, Kvora, Corda a Hyperledger Fabric. Cílem pracovní skupiny je vytvořit rámec, který standardizuje používání žetonů z obchodního hlediska, aby se zabránilo zjednodušení a demokratizoval vývoj založený na tokenech. Tím, že umožňuje průmyslu definovat tyto tokeny a jejich chování na obchodní úrovni, podrobné implementace tokeny jsou abstrahovány od obchodní logiky, která manipuluje tokeny.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky z Azure Blockchain najdete na [blogu Azure Blockchain,](https://azure.microsoft.com/blog/topics/blockchain/) kde si můžete dát aktuální informace o nabídkách blockchainových služeb a informacích od technického týmu Azure Blockchain.

Chcete-li získat přístup k náhledu tokenů Azure Blockchain, [obraťte se na tým tokenů Azure Blockchain](https://aka.ms/PreviewForm).

Chcete-li poskytnout zpětnou vazbu od produktů nebo požádat o nové funkce, pošlete si návrh na návrh prostřednictvím [fóra pro zpětnou vazbu Azure pro blockchain](https://aka.ms/blockchainuservoice).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [dostupných šablonách tokenů Azure Blockchain](templates.md).
