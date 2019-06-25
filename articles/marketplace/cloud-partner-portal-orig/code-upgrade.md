---
title: Upgrade na nejnovější platformu kódu | Azure Marketplace
description: Toto téma vysvětluje postup upgradu vaší aplikace Microsoft Dynamics 365 pro verzi platformy operace na nejnovější verzi platformy
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935290"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Kód upgradu na nejnovější platformu

Tento článek vysvětluje postup upgradu vaší aplikace Microsoft Dynamics 365 pro verzi platformy operace na nejnovější verzi platformy.

## <a name="overview"></a>Přehled

Microsoft Dynamics 365 pro platformu operace se skládá z následujících součástí:

Dynamics 365 pro binární soubory platformy operace například aplikačního objektu serveru (AOS), platformě pro správu dat, vykazování a framework business intelligence (BI), nástroje pro vývoj a analytické služby. Následující balíčky stromu objektů aplikace (AOT):

1. Aplikační platforma
2. Foundation aplikace
3. Test Essentials

**Důležité**: Pokud chcete přesunout na poslední Dynamics 365 pro operace platformu, vaše Dynamics 365 pro implementaci operace nemůže mít všechna vlastní nastavení (překrývání vrstev) u všech AOT balíčky, které patří k platformě. Toto omezení byla zavedena v platform update 3, takže průběžné aktualizace můžete provést na platformu. Pokud používáte platformu, která je starší než platformy s aktualizací 3 naleznete v tématu upgrade na platformě s aktualizací 3 v předchozí části sestavení na konci tohoto článku.

Další informace o upgradu kódu najdete v tématu věnovaném [tady](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).