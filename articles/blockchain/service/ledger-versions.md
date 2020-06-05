---
title: Verze hlavní knihy služeb Azure blockchain, opravy, aktualizace &
description: Přehled podporovaných verzí v hlavní knize ve službě Azure blockchain, včetně zásad týkajících se oprav systémů a počítačů spravovaných systémem a uživatelem spravovaných upgradů.
ms.date: 06/02/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: da7907c2c324932ba43863c1074cdff7d54d3827
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430743"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze hlavní knihy služby Azure blockchain

Služba Azure blockchain používá hlavní knihu [kvora](https://www.goquorum.com/developers) založenou na ethereem navrženou pro zpracování privátních transakcí v rámci skupiny známých účastníků, která je určená jako konsorcium ve službě Azure blockchain Service.

V současné době služba Azure blockchain podporuje [kvorum verze 2.5.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.5.0) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvoru se provádí prostřednictvím hlavní, vedlejší a opravné verze. Pokud je například kvorum verze 2.0.1, typ verze bude zařazen následovně:

|Hlavní | Vedlejší  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Služba Azure blockchain automaticky aktualizuje vydané verze kvora na stávající běžící členy do 30 dnů od jejího zpřístupnění z kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nových verzí hlavní knihy

Služba Azure blockchain poskytuje nejnovější hlavní a dílčí verze hlavní knihy kvora do 60 dnů od jejího zpřístupnění od výrobce kvora. Pro konsorcia si můžete při zřizování nového člena a konsorcia poskytnout maximálně čtyři dílčí verze. Upgrade z verze na hlavní nebo dílčí verzi se v tuto chvíli nepodporuje. Například pokud používáte verzi 2. x, upgrade na verzi 3. x není aktuálně podporován. Podobně pokud používáte verzi 2,2, upgrade na verzi 2,3 není aktuálně podporován.

## <a name="next-steps"></a>Další kroky

[Omezení ve službě Azure blockchain](limits.md)
