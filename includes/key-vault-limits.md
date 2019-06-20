---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175412"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klíč transakce (maximální počet transakcí, které jsou povolené za 10 sekund, za úložiště na oblast<sup>1</sup>):

|Typ klíče|Klíč HSM<br>Vytvoření klíče|Klíč HSM<br>Veškeré ostatní transakce|Softwarový klíč<br>Vytvoření klíče|Softwarový klíč<br>Veškeré ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1 000|10|2 000|
|RSA 3,072-bit|5|250|10|500|
|RSA 4,096-bit|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|

> [!NOTE]
> V předchozí tabulce vidíme, že pro softwarové klíče RSA 2 048 bitů, 2 000 transakcí GET za 10 sekund jsou povolena. Klíče – RSA 2 048 bitů HSM jsou povoleny 1 000 transakcí GET za 10 sekund.
>
> Jsou váha prahových hodnotách omezování a vynucení je na jejich součet. Například jak je znázorněno v předchozí tabulce, při provádění operace GET s klíči RSA modulu hardwarového zabezpečení, je osm časy dražší, pro použití klíčů 4 096 bitů ve srovnání s 2 048 bitů. Důvodem je, že 1 000/125 = 8.
>
> V daném intervalu 10 sekund, můžete provést klienta Azure Key Vault *pouze jeden* z následujících operací, než se setká `429` omezování stavový kód HTTP:
> - 2 000 transakcí GET softwarový klíč RSA 2 048 bitů
> - 1 000 transakcí GET RSA 2 048 bitů klíč HSM
> - Transakce 125 GET bitů pro RSA 4 096 klíč HSM
> - 124 GET bitů pro RSA 4 096 klíče HSM a 8 transakce GET RSA 2 048 bitů klíč HSM

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Tajné kódy, spravovaných klíčů účtu úložiště a transakce úložiště:
| Typ transakce | Maximální povolené za 10 sekund za transakce úložiště na oblast<sup>1</sup> |
| --- | --- |
| Všechny transakce |2 000 |

Informace o tom, jak řešit omezování, když překročíte tyto limity, najdete v části [pokyny k omezování služby Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> limit úrovni předplatného pro všechny typy transakcí je pětkrát za limit služby key vault. Například HSM ostatní transakce na jedno předplatné jsou omezené na 5000 transakcí za 10 sekund na jedno předplatné.
