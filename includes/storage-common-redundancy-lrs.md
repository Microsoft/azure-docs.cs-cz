---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 124f5c01b7718f729094de1c02391946ff50cef4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66113760"
---
Místně redundantní úložiště (LRS) zajišťuje alespoň 99,999999999 % (11 nines) odolnosti objektů v průběhu daného roku. LRS poskytuje tento objekt odolnosti replikuje vaše data na jednotce škálování úložiště. Datové centrum, nachází v oblasti, ve kterém jste vytvořili účet úložiště hostuje jednotce škálování úložiště. Žádost o zápis do účtu úložiště LRS skončí úspěšně, až data se zapisují do všech replik. Každá replika se nachází v samostatných doménách selhání a upgradovacích doménách v jednotce škálování úložiště.

Jednotky škálování úložiště je kolekce stojany uzlů úložiště. Doména selhání (FD) je skupinu uzlů, které představují fyzické jednotky selhání. Doména selhání můžete představit jako uzly patřící do stejného fyzického stojanu. Upgradovací doména (UD) je skupina uzlů, které budou upgradovány společně během procesu upgradu služby (nasazení). Repliky jsou rozděleny mezi aktualizačními doménami a doménami selhání v rámci jedné jednotky škálování úložiště. Tato architektura se zajistí, že vaše data jsou k dispozici, pokud selhání hardwaru ovlivní jednu stojanu nebo pokud uzly se upgraduje během upgradu služby.

LRS se možnost replikace nejnižší náklady a nabízí nejnižší odolnosti ve srovnání s další možnosti. Pokud dojde k havárii úrovni datacentra (třeba fire nebo zahlcení), všechny repliky může být ztracené nebo neobnovitelná. Chcete-li toto riziko snížit, společnost Microsoft doporučuje používat zónově redundantní úložiště (ZRS) nebo geograficky redundantní úložiště (GRS).

* Pokud aplikace ukládá data, která můžou být snadno znovu vytvořena Pokud dojde ke ztrátě dat, můžete zvolit LRS.
* Některé aplikace jsou omezeny na replikaci dat pouze v rámci zemi/oblast dat požadavky zásad správného řízení. V některých případech může být spárovaných oblastí, ve kterých se data replikují pro účty GRS v jiné zemi/oblast. Další informace o spárovaných oblastí najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).
