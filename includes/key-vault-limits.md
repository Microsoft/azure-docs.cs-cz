---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279538"
---
Klíč transakce (maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup>):

|Typ klíče|HSM-Key<br>Vytvoření klíče|Klíč HSM<br>Veškeré ostatní transakce|Softwarový klíč<br>Vytvoření klíče|Softwarový klíč<br>Veškeré ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2048 bitů|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Tajné kódy, spravovaných klíčů účtu úložiště a transakce úložiště:
| Typ transakce | Maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup> |
| --- | --- |
| Všechny transakce |2000 |
|

Zobrazit [pokyny k omezování služby Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informace o tom, jak řešit omezování, když překročíte tyto limity.

<sup>1</sup> platí limit úrovni předplatného pro všechny typy transakcí, která je 5 x za limit služby key vault. Pro příklad, HSM, že ostatní transakce podle předplatného jsou omezené na 5000 transakcí za 10 sekund na jedno předplatné.
