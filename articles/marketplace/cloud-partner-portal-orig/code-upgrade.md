---
title: Upgrade na nejnovější platformu kódu | Dokumentace Microsoftu
description: Toto téma vysvětluje postup upgradu vaší aplikace Microsoft Dynamics 365 pro verzi platformy operace na nejnovější verzi platformy
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809359"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Kód upgradu na nejnovější platformu

Tento článek vysvětluje postup upgradu vaší aplikace Microsoft Dynamics 365 pro verzi platformy operace na nejnovější verzi platformy.

## <a name="overview"></a>Přehled

Microsoft Dynamics 365 pro platformu operace se skládá z následujících součástí:

Dynamics 365 pro binární soubory platformy operace například aplikačního objektu serveru (AOS), platformě pro správu dat, vykazování a framework business intelligence (BI), nástroje pro vývoj a analytické služby. Následující balíčky stromu objektů aplikace (AOT):

1. Aplikační platforma
2. Foundation aplikace
3. Test Essentials

**Důležité**: Pokud chcete přesunout na poslední Dynamics 365 pro operace platformu, vaše Dynamics 365 pro implementaci operace nemůže mít všechna vlastní nastavení (overlayering) u všech AOT balíčky, které patří k platformě. Toto omezení byla zavedena v platform update 3, takže průběžné aktualizace můžete provést na platformu. Pokud používáte platformu, která je starší než platformy s aktualizací 3 naleznete v tématu upgrade na platformě s aktualizací 3 v předchozí části sestavení na konci tohoto článku.

Další informace o upgradu kódu najdete v tématu věnovaném [tady](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).