---
title: Služba Azure Blockchain podporované verze knihy, použití dílčích oprav a upgrade
description: Přehled podporovaných účetní knihy verzí ve službě Azure Blockchain, včetně zásady týkající se systémů, použití dílčích oprav a spravovat systém a upgrady spravovat uživatele.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027882"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze služby Azure Blockchain účetní kniha

Používá službu Azure Blockchain podle ethereum během [kvora](https://github.com/jpmorganchase/quorum/wiki) určená pro zpracování privátní transakce ve skupině známých účastníků identifikována jako consortium ve službě Azure Blockchain účetní knihy.

V současné době podporuje služba Azure Blockchain [kvora verze 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvora se provádí prostřednictvím hlavní verze, hlavní bod a verzí menší bodu. Pokud kvora 2.0.1, verze 2 je hlavní verze, vydání hlavní bod je 0 a 1 je vydání vedlejší bodu. Služba automaticky opravy menších novější vydání knihy. Upgrade verze hlavních a hlavní bod v současné době nejsou podporovány.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nové verze knihy

Služba Azure Blockchain poskytuje nejnovější verze knihy během 60 dnů je k dispozici u výrobce účetní knihy. Maximálně čtyři hlavní novější vydání jsou k dispozici pro konsorcia lze vybírat při zřizování nového člena a consortium.

## <a name="next-steps"></a>Další postup

[Omezení ve službě Azure Blockchain](limits.md)
