---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a8979edf94c0dd0271293feb28c18530faeba09c
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56659967"
---
## <a name="key-transactions-max-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klíč transakce (maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup>):

|Typ klíče|Klíč HSM<br>Vytvoření klíče|Klíč HSM<br>Veškeré ostatní transakce|Software-key<br>Vytvoření klíče|Software-key<br>Veškeré ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> V předchozí tabulce vidíme, že pro RSA 2048 bitů softwaru keys povolujeme 2000 GET transakcí za 10 sekund a, že pro RSA 2048 bitů, klíčů HSM povolujeme GET 1000 transakcí za 10 sekund.
>
> Všimněte si, že jsou váha prahových hodnotách omezování a vynucení je na jejich součet. Například v předchozí tabulce vidíme, že při provádění operací GET na klíče RSA HSM je 8krát dražší, aby se používaly klíče 4096 bitů ve srovnání s 2 048 bitů (od 1 000/125 = 8). Proto v daném intervalu 10 sekund, klientem AKV udělat přesně jeden z následujících před zjištění `429` omezování stavový kód HTTP:
> - 2000 RSA 2048 bitů softwarový klíč GET transakce **nebo**
> - 1000 transakcí HSM klíče RSA 2048 bitů GET, **nebo**
> - 125 HSM klíče šifrování RSA 4096 bitů GET transakce **nebo**
> - 124 HSM klíče šifrování RSA 4096 bitů GET transakce a 8 získat klíč HSM RSA 2048 bitů.

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Tajné kódy, spravovaných klíčů účtu úložiště a transakce úložiště:
| Typ transakce | Maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup> |
| --- | --- |
| Všechny transakce |2000 |

Zobrazit [pokyny k omezování služby Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informace o tom, jak řešit omezování, když překročíte tyto limity.

<sup>1</sup> platí limit úrovni předplatného pro všechny typy transakcí, která je 5 x za limit služby key vault. Pro příklad, HSM, že ostatní transakce podle předplatného jsou omezené na 5000 transakcí za 10 sekund na jedno předplatné.
