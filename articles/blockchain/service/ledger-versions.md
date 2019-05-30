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
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399104"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze služby Azure Blockchain účetní kniha

Používá službu Azure Blockchain podle ethereum během [kvora](https://www.goquorum.com/developers) určená pro zpracování privátní transakce ve skupině známých účastníků identifikována jako consortium ve službě Azure Blockchain účetní knihy.

V současné době podporuje služba Azure Blockchain [kvora verze 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvora se provádí prostřednictvím hlavní, vedlejší verzi a vydání opravy. Například pokud je verze kvora 2.0.1, vydání by rozdělit následujícím způsobem:

|Hlavní | Podverze  | Oprava  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Služba Azure Blockchain automaticky aktualizuje opravu verzích kvora pro stávající členy spuštěné do 30 dní od k dispozici od kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nové verze knihy

Azure Blockchain Service poskytuje nejnovější hlavní a dílčí verze knihy kvora během 60 dnů je k dispozici u výrobce kvora. Maximálně čtyři vedlejší verze jsou k dispozici pro konsorcia lze vybírat při zřizování nového člena a consortium. Upgrade z na velký nebo dílčí verze se aktuálně nepodporuje.

## <a name="next-steps"></a>Další postup

[Omezení ve službě Azure Blockchain](limits.md)
