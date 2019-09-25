---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219026"
---
Místně redundantní úložiště (LRS) replikuje vaše data třikrát v jednom datovém centru. LRS poskytuje v průběhu daného roku alespoň 99,999999999% (11 devíti) odolnosti objektů. LRS je možnost replikace s nejnižšími náklady a nabízí minimální odolnost v porovnání s jinými možnostmi.

Pokud dojde k havárii na úrovni datacentra (například při požáru nebo zahlcení), může dojít ke ztrátě nebo obnovení všech replik v účtu úložiště pomocí LRS. Pro zmírnění tohoto rizika Společnost Microsoft doporučuje používat úložiště ZRS (Zone-redundantní úložiště), geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště (GZRS).

Požadavek na zápis do účtu úložiště LRS se úspěšně vrátí až po zápisu dat do všech tří replik.

Můžete použít LRS v následujících scénářích:

* Pokud vaše aplikace ukládá data, která je možné snadno rekonstruovat, pokud dojde ke ztrátě dat, můžete se rozhodnout pro LRS.
* Některé aplikace jsou omezené na replikaci dat v rámci země nebo oblasti z důvodu požadavků na zásady správného řízení dat. V některých případech se spárované oblasti, ve kterých se replikují data pro účty GRS, můžou nacházet v jiné zemi nebo oblasti. Další informace o spárovaných oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).
