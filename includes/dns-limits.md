---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334572"
---
**Veřejné zóny DNS**

| Prostředek | Omezení |
| --- | --- |
| Veřejné zóny DNS na předplatné |250 <sup>1.</sup> |
| Sady záznamů pro veřejnou zónu DNS |10 000 <sup>1</sup> |
| Záznamy na záznam nastavený ve veřejné zóně DNS |20 |
| Počet záznamů aliasu pro jeden prostředek Azure |20|
| Privátní zóny DNS na předplatné |1000|
| Sady záznamů pro soukromou zónu DNS |250 000|
| Záznamy na sadu záznamů pro soukromé zóny DNS |20|
| Propojení virtuálních sítí na soukromou zónu DNS |1000|
| Propojení virtuálních sítí na soukromé zóny DNS s povolenou automatickou registrací |100|
| Počet privátních zón DNS, na které se virtuální síť může propojit s povolenou automatickou registrací |1|
| Počet privátních dns zón, které může virtuální síť propojit |1000|
| Počet dotazů DNS, které může virtuální počítač odeslat překladači Azure DNS za sekundu |500 <sup>2</sup> |
| Maximální počet dotazů DNS zařazených do fronty (čekající odpověď) na virtuální počítač |200 <sup>2</sup> |

<sup>1.</sup> Pokud potřebujete zvýšit tato omezení, obraťte se na podporu Azure.

<sup>2.</sup> Tyto limity se vztahují na každý jednotlivý virtuální počítač a ne na úrovni virtuální sítě. Dotazy DNS překračující tato omezení jsou vynechány.