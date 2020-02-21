---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: b674f8e31eb61328f60bb24866f73d02653b655f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77494959"
---
**Veřejné zóny DNS**

| Prostředek | Výchozí omezení |
| --- | --- |
| Veřejné Zóny DNS na předplatné |250 <sup>1</sup> |
| Sady záznamů na veřejnou zónu DNS |10 000 <sup>1</sup> |
| Záznamů na sadu záznamů ve veřejné zóně DNS |20 |
| Počet záznamů aliasů pro jeden prostředek Azure |20|
| Privátní DNS zóny na předplatné |1 000|
| Sady záznamů na privátní zónu DNS |25000|
| Počet záznamů na sadu záznamů pro privátní zóny DNS |20|
| Virtual Network odkazy na privátní zónu DNS |1 000|
| Odkazy na virtuální sítě podle privátních zón DNS s povolenou automatickou registrací |100|
| Počet privátních zón DNS, na které může virtuální síť připojit s povolenou automatickou registrací |1|
| Počet privátních zón DNS, které může virtuální síť připojit |1 000|
| Počet dotazů DNS, které může virtuální počítač odeslat Azure DNS překladač za sekundu |500 <sup>2</sup> |
| Maximální počet dotazů DNS zařazených do fronty (čekající odpověď) na virtuální počítač |200 <sup>2</sup> |

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.

<sup>2</sup> . Tato omezení platí pro každý jednotlivý virtuální počítač, nikoli na úrovni virtuální sítě. Dotazy DNS překračující tato omezení jsou vyřazené.