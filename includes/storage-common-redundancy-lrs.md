---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015964"
---
Místně redundantní úložiště (LRS) poskytuje v průběhu daného roku alespoň 99,999999999% (11 devíti) odolnosti objektů. LRS zajišťuje odolnost tohoto objektu replikací vašich dat do jednotky škálování úložiště. Datové centrum, které je umístěné v oblasti, ve které jste vytvořili účet úložiště, hostuje jednotku škálování úložiště. Požadavek na zápis do účtu úložiště LRS se úspěšně vrátí až po zápisu dat do všech replik. Každá replika se nachází v samostatných doménách selhání a upgradovacích doménách v rámci jednotky škálování úložiště.

Jednotka škálování úložiště je kolekce skříní uzlů úložiště. Doména selhání (FD) je skupina uzlů, které reprezentují fyzickou jednotku selhání. Doména selhání se považuje za uzly patřící do stejného fyzického stojanu. Upgradovací doména (UD) je skupina uzlů, které jsou upgradovány společně během procesu upgradu služby (zavedení). Repliky jsou rozloženy mezi UDs a doménami selhání v rámci jedné jednotky škálování úložiště. Tato architektura zajišťuje dostupnost vašich dat v případě, že selhání hardwaru má vliv na jeden stojan nebo když jsou uzly upgradovány během upgradu služby.

LRS je možnost replikace s nejnižšími náklady a nabízí minimální odolnost v porovnání s jinými možnostmi. Pokud dojde k havárii na úrovni datacentra (například při požáru nebo zahlcení), může dojít ke ztrátě nebo obnovení všech replik. Pro zmírnění tohoto rizika Společnost Microsoft doporučuje používat úložiště ZRS (Zone-redundantní úložiště), geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště (GZRS).

* Pokud vaše aplikace ukládá data, která je možné snadno rekonstruovat, pokud dojde ke ztrátě dat, můžete se rozhodnout pro LRS.
* Některé aplikace jsou omezené na replikaci dat v rámci země nebo oblasti z důvodu požadavků na zásady správného řízení dat. V některých případech se spárované oblasti, ve kterých se replikují data pro účty GRS, můžou nacházet v jiné zemi nebo oblasti. Další informace o spárovaných oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).
