---
title: Podpora přidání virtuálních počítačů Azure do existující skupiny dostupnosti | Dokumenty společnosti Microsoft
description: Tento článek obsahuje matici supportability, o které řadě virtuálních aplikací můžete kombinovat ve stejné sadě dostupnosti
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122919"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Podpora přidání virtuálních počítačů Azure do existující sady dostupnosti

Občas může dojít k omezení při přidávání nových virtuálních počítačů (VM) do existující skupiny dostupnosti. Následující podrobnosti grafu, které řady virtuálních aplikací můžete kombinovat ve stejné sadě dostupnosti.

Tady je matice supportability pro kombinaci různých typů virtuálních počítačů:

Sada dostupnosti & řady|Druhý virtuální virtuální město|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|První virtuální virtuální měn|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Všechny ostatní řady nemohou být ve stejné sadě dostupnosti, protože vyžadují konkrétní hardware.

Velikost virtuálního počítače A8/A9 nelze smíchat kvůli požadavku na vyhrazenou páteřní síť RDMA.
