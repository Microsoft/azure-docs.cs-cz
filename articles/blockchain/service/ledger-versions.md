---
title: Verze hlavní knihy služby Azure Blockchain, opravy & upgrade
description: Přehled podporovaných verzí účetních knih ve službě Azure Blockchain Service, včetně zásad týkajících se oprav systémů a upgradů spravovaných systémem a upgradů spravovaných uživateli.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325193"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze hlavní knihy služby Azure Blockchain

Služba Azure Blockchain používá hlavní knihu [kvorů](https://www.goquorum.com/developers) založenou na ethereum určenou pro zpracování soukromých transakcí v rámci skupiny známých účastníků, identifikované jako konsorcium ve službě Azure Blockchain Service.

Služba Azure Blockchain Service v současné době podporuje [kvorum verze 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvoru se provádí prostřednictvím hlavních, vedlejších a oprav. Pokud je například verze Kvora 2.0.1, bude typ vydání rozdělen takto:

|Hlavní | Vedlejší  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Služba Azure Blockchain service automaticky aktualizuje vydání kvora na stávající spuštěné členy do 30 dnů od zpřístupnění z kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nových verzí hlavní knihy

Služba Azure Blockchain service poskytuje nejnovější hlavní a dílčí verze hlavní knihy kvora do 60 dnů od dostupnosti od výrobce kvora. Při zajišťování nového člena a konsorcia jsou k dispozici maximálně čtyři menší verze. Upgrade z hlavní nebo menší verze není v současné době podporován. Pokud například používáte verzi 2.x, upgrade na verzi 3.x není aktuálně podporován. Podobně pokud používáte verzi 2.2, upgrade na verzi 2.3 není aktuálně podporován.

## <a name="next-steps"></a>Další kroky

[Limity ve službě Azure Blockchain](limits.md)
