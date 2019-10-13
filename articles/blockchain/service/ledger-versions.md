---
title: Služba Azure blockchain podporuje verze hlavní knihy, opravy a upgrade.
description: Přehled podporovaných verzí v hlavní knize ve službě Azure blockchain, včetně zásad týkajících se oprav systémů a počítačů spravovaných systémem a uživatelem spravovaných upgradů.
services: azure-blockchain
keywords: Blockchain
author: PatAltimore
ms.author: patricka
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 07ca22ee84dc7579d59d95b1c303476f44f8f043
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286522"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze hlavní knihy služby Azure blockchain

Služba Azure blockchain používá hlavní knihu [kvora](https://www.goquorum.com/developers) založenou na ethereem navrženou pro zpracování privátních transakcí v rámci skupiny známých účastníků, která je určená jako konsorcium ve službě Azure blockchain Service.

V současné době služba Azure blockchain podporuje [kvorum verze 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvoru se provádí prostřednictvím hlavní, vedlejší a opravné verze. Pokud je například kvorum verze 2.0.1, typ verze bude zařazen následovně:

|Hlavní | Moll  | Použita  |
| :--- | :----- | :----- |
| 2 | 0 | 1\. místo | 

Služba Azure blockchain automaticky aktualizuje vydané verze kvora na stávající běžící členy do 30 dnů od jejího zpřístupnění z kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nových verzí hlavní knihy

Služba Azure blockchain poskytuje nejnovější hlavní a dílčí verze hlavní knihy kvora do 60 dnů od jejího zpřístupnění od výrobce kvora. Pro konsorcia si můžete při zřizování nového člena a konsorcia poskytnout maximálně čtyři dílčí verze. Upgrade z verze na hlavní nebo dílčí verzi se v tuto chvíli nepodporuje. Například pokud používáte verzi 2. x, upgrade na verzi 3. x není aktuálně podporován. Podobně pokud používáte verzi 2,2, upgrade na verzi 2,3 není aktuálně podporován.

## <a name="next-steps"></a>Další kroky

[Omezení ve službě Azure blockchain](limits.md)
