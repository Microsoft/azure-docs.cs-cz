---
title: Verze hlavní knihy služeb Azure blockchain, opravy, aktualizace &
description: Přehled podporovaných verzí v hlavní knize ve službě Azure blockchain, včetně zásad týkajících se oprav systémů a počítačů spravovaných systémem a uživatelem spravovaných upgradů.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 03eaf86ece191cce7eef6647a582a66495692db2
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284928"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Podporované verze hlavní knihy služby Azure blockchain

Služba Azure blockchain používá hlavní knihu [kvora](https://www.goquorum.com/developers) založenou na ethereem navrženou pro zpracování privátních transakcí v rámci skupiny známých účastníků, která je určená jako konsorcium ve službě Azure blockchain Service.

V současné době služba Azure blockchain podporuje [kvorum verze 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) a [správce transakcí Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Správa aktualizací a upgradů

Správa verzí v kvoru se provádí prostřednictvím hlavní, vedlejší a opravné verze. Pokud je například kvorum verze 2.0.1, typ verze bude zařazen následovně:

|Hlavní | Podverze  | Použita  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Služba Azure blockchain automaticky aktualizuje vydané verze kvora na stávající běžící členy do 30 dnů od jejího zpřístupnění z kvora.

## <a name="availability-of-new-ledger-versions"></a>Dostupnost nových verzí hlavní knihy

Služba Azure blockchain poskytuje nejnovější hlavní a dílčí verze hlavní knihy kvora do 60 dnů od jejího zpřístupnění od výrobce kvora. Pro konsorcia si můžete při zřizování nového člena a konsorcia poskytnout maximálně čtyři dílčí verze. Upgrade z verze na hlavní nebo dílčí verzi se v tuto chvíli nepodporuje. Například pokud používáte verzi 2. x, upgrade na verzi 3. x není aktuálně podporován. Podobně pokud používáte verzi 2,2, upgrade na verzi 2,3 není aktuálně podporován.

## <a name="next-steps"></a>Další kroky

[Omezení ve službě Azure blockchain](limits.md)
