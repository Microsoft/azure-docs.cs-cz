---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 8247b1cedc2c5ebc8577af6be485aed0fcd5d6af
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768735"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klíčové transakce (maximální počet transakcí povolených za 10 sekund, na trezor na oblast<sup>1</sup>):

|Typ klíče|Klíč HSM<br>VYTVOŘIT klíč|Klíč HSM<br>Všechny ostatní transakce|Klíč softwaru<br>VYTVOŘIT klíč|Klíč softwaru<br>Všechny ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2 048 bitů|5|1 000|10|2 000|
|RSA 3 072 bitů|5|250|10|500|
|RSA 4 096 bitů|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|

> [!NOTE]
> V předchozí tabulce vidíme, že pro RSA 2 048bitové softwarové klíče jsou povoleny 2 000 transakcí GET za 10 sekund. Pro RSA 2 048bitové klíče hsm jsou povoleny 1 000 transakcí GET za 10 sekund.
>
> Omezení prahové hodnoty jsou váženy a vynucení je na jejich součet. Například, jak je znázorněno v předchozí tabulce, při provádění operací GET na klíčů rsa hsm, je osmkrát dražší použít 4 096bitové klíče ve srovnání s 2 048bitové klíče. To proto, že 1,000/125 = 8.
>
> V daném intervalu 10 sekund může klient Azure Key Vault provést *pouze jednu* z následujících operací, než narazí na `429` omezení stavového kódu HTTP:
> - 2 000 RSA 2 048bitové softwarové transakce GET
> - 1 000 RSA 2 048bitové transakce GET s klíčem hsm
> - 125 RSA 4 096bitové transakce GET s klíčem hsm
> - 124 TRANSAKCÍ GET s klíčem hsm RSA 4 096 bitů rsa a 8 transakcí GET s klíčem hsm RSA 2 048 bitů RSA

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Tajné klíče, klíče účtů spravovaného úložiště a transakce trezoru:

| Typ transakcí | Maximální počet povolených transakcí za 10 sekund na úschovnu na oblast<sup>1</sup> |
| --- | --- |
| Všechny transakce |2 000 |

Informace o tom, jak zpracovat omezení při překročení těchto omezení, naleznete v [tématu Azure Key Vault omezení pokyny](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> Limit pro celý předplatné pro všechny typy transakcí je pětkrát na limit trezoru klíčů. Například transakce HSM ostatní na předplatné jsou omezeny na 5 000 transakcí za 10 sekund na odběr.

### <a name="azure-private-link-integration"></a>Integrace Privátního spojení Azure

| Prostředek | Omezení |
| -------- | ----- |
| Privátní koncové body na trezor klíčů | 64 |
| Trezory klíčů s privátními koncovými body na předplatné | 64 |
