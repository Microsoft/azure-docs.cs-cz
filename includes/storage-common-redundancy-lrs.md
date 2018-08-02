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
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399987"
---
Místně redundantní úložiště (LRS) je navržená k poskytování alespoň 99,999999999 % (11 9) odolnosti objektů v průběhu daného roku díky replikuje vaše data v jednotce škálování úložiště. Jednotky škálování úložiště je hostována v datacentru oblasti, ve kterém jste vytvořili svůj účet úložiště. Žádost o zápis do účtu úložiště LRS skončí úspěšně, až po data se zapsala do všech replik. Tyto repliky každý jsou umístěny v samostatné domény selhání a aktualizačními doménami v rámci jedné jednotky škálování úložiště.

Jednotky škálování úložiště je kolekce stojany uzlů úložiště. Doména selhání (FD) je skupina uzlů, které představují fyzické jednotky selhání a může být považována za uzly patřící do stejného fyzického stojanu. Upgradovací doména (UD) je skupina uzlů, které budou upgradovány společně během procesu upgradu služby (nasazení). Repliky jsou rozděleny mezi aktualizačními doménami a doménami selhání v rámci jedné jednotky škálování úložiště. Tato architektura se zajistí, že vaše data budou dostupná, pokud selhání hardwaru ovlivní jednu stojanu nebo pokud uzly se upgraduje během zavádění řešení.

LRS je nejnižší náklady na možnost replikace a nabízí nejnižší odolnosti ve srovnání s další možnosti. Pokud dojde k havárii úrovni datacentra (třeba fire nebo zahlcení), všechny repliky může být ztracené nebo neobnovitelná. Chcete-li toto riziko snížit, společnost Microsoft doporučuje používat zónově redundantní úložiště (ZRS) nebo geograficky redundantní úložiště (GRS).

* Pokud aplikace ukládá data, která můžou být snadno znovu vytvořena Pokud dojde ke ztrátě dat, můžete zvolit LRS.
* Některé aplikace jsou omezeny na replikaci dat pouze v rámci země z důvodu požadavků na data zásad správného řízení. V některých případech může být spárovaných oblastí, ve které se replikují data pro účty GRS v jiné zemi. Další informace o spárovaných oblastí najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).
