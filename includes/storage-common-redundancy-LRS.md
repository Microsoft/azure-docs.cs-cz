---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
Místně redundantní úložiště (LRS) určená k poskytování alespoň % 99.999999999 (11 společnosti 9) odolnost objektů během daného roku nastavením replikace svá data do jednotky škálování úložiště. Jednotka škálování úložiště je hostován v datacentru v oblasti, ve které jste vytvořili účet úložiště. Žádost o zápis k účtu úložiště LRS vrátí úspěšně až po data byla zapsána na všechny repliky. Tyto repliky každý jsou umístěny v oddělené domén selhání a aktualizace domén v rámci jedné jednotky škálování úložiště.

Jednotka škálování úložiště je kolekce stojany uzlů úložiště. (FD) domény selhání je skupina uzlů, které představují fyzická jednotka selhání a lze považovat za uzly, které patří do stejné fyzické racku. Upgradovací doméně (UD) je skupina uzlů, které jsou upgradovány společně během procesu upgradu služby (zavedení). Repliky jsou rozloženy UDs a FDs v rámci jedné jednotky škálování úložiště. Tato architektura zajišťuje, že vaše data k dispozici selhání hardwaru má dopad na jednom racku nebo když jsou uzly upgradován během zavedení.

LRS se možnost replikace nejnižší náklady a nabízí minimálně odolnost ve srovnání s dalšími možnostmi. Pokud dojde k havárii datacenter úrovni (například ještě efektivněji nebo zahlcení), všechny repliky může být ztracené nebo neopravitelné. Chcete-li toto riziko snížilo, společnost Microsoft doporučuje používat zónově redundantní úložiště (ZRS) nebo geograficky redundantní úložiště (GRS).

* Pokud aplikace ukládá data, která lze snadno znovu vytvořena Pokud dojde ke ztrátě dat, můžete zvolit LRS.
* Některé aplikace jsou omezeny na replikaci dat pouze v rámci země z důvodu požadavky zásad správného řízení data. V některých případech může být spárované oblastí, mezi které data se replikují pro GRS účty v jiné zemi. Další informace o spárované oblastech najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).
