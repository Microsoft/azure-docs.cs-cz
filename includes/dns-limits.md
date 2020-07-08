---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805825"
---
**Veřejné zóny DNS**

| Prostředek | Omezení |
| --- | --- |
| Veřejné Zóny DNS na předplatné |250 <sup>1</sup> |
| Sady záznamů na veřejnou zónu DNS |10 000 <sup>1</sup> |
| Záznamů na sadu záznamů ve veřejné zóně DNS |20 |
| Počet záznamů aliasů pro jeden prostředek Azure |20|
| Privátní DNS zóny na předplatné |1000|
| Sady záznamů na privátní zónu DNS |250 000|
| Počet záznamů na sadu záznamů pro privátní zóny DNS |20|
| Virtual Network odkazy na privátní zónu DNS |1000|
| Odkazy na virtuální sítě podle privátních zón DNS s povolenou automatickou registrací |100|
| Počet privátních zón DNS, na které může virtuální síť připojit s povolenou automatickou registrací |1|
| Počet privátních zón DNS, které může virtuální síť připojit |1000|
| Počet dotazů DNS, které může virtuální počítač odeslat Azure DNS překladač za sekundu |500 <sup>2</sup> |
| Maximální počet dotazů DNS zařazených do fronty (čekající odpověď) na virtuální počítač |200 <sup>2</sup> |

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.

<sup>2</sup> . Tato omezení platí pro každý jednotlivý virtuální počítač, nikoli na úrovni virtuální sítě. Dotazy DNS překračující tato omezení jsou vyřazené.
