---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94329501"
---
**Veřejné zóny DNS**

| Prostředek | Omezení |
| --- | --- |
| Veřejné Zóny DNS na předplatné |250 <sup>1</sup> |
| Sady záznamů na veřejnou zónu DNS |10 000 <sup>1</sup> |
| Záznamů na sadu záznamů ve veřejné zóně DNS |20 |
| Počet záznamů aliasů pro jeden prostředek Azure |20|

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.

**Privátní DNS zóny**

| Prostředek | Omezení |
| --- | --- |
| Privátní DNS zóny na předplatné |1000|
| Sady záznamů na privátní zónu DNS |250 000|
| Počet záznamů na sadu záznamů pro privátní zóny DNS |20|
| Virtual Network odkazy na privátní zónu DNS |1000|
| Odkazy na virtuální sítě podle privátních zón DNS s povolenou automatickou registrací |100|
| Počet privátních zón DNS, na které může virtuální síť připojit s povolenou automatickou registrací |1|
| Počet privátních zón DNS, které může virtuální síť připojit |1000|
| Počet dotazů DNS, které může virtuální počítač odeslat Azure DNS překladač za sekundu |1000 <sup>1</sup> |
| Maximální počet dotazů DNS zařazených do fronty (čekající odpověď) na virtuální počítač |200 <sup>1</sup> |

<sup>1</sup> Tato omezení platí pro každý jednotlivý virtuální počítač, nikoli na úrovni virtuální sítě. Dotazy DNS překračující tato omezení jsou vyřazené.
