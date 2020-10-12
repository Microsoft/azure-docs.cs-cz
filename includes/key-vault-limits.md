---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 01b3c9584f3ecddbcdcc6938f5eb469510a47a4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838769"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klíčové transakce (maximální počet transakcí povolených za 10 sekund, na trezor v oblasti<sup>1</sup>):

|Typ klíče|Klíč HSM<br>VYTVOŘIT klíč|Klíč HSM<br>Všechny ostatní transakce|Softwarový klíč<br>VYTVOŘIT klíč|Softwarový klíč<br>Všechny ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2 048-bit|5|1 000|10|2 000|
|RSA 3 072-bit|5|250|10|500|
|RSA 4 096-bit|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|

> [!NOTE]
> V předchozí tabulce uvidíme, že pro klíče RSA 2 048 jsou povolené transakce 2 000 GET transakcí za 10 sekund. V případě šifrování RSA 2 048 jsou povoleny klíče HSM, 1 000 GET transakcí za 10 sekund.
>
> Prahové hodnoty omezování jsou vážené a vynucování je na jejich součtu. Například, jak je uvedeno v předchozí tabulce, když provádíte operace GET na klíčích RSA HSM, je to osm dražších k použití 4 096 bitových klíčů v porovnání s 2 048 bitovými klíči. To je proto, že je 1000/125 = 8.
>
> V zadaném intervalu 10 sekund může klient Azure Key Vault provést *pouze jednu* z následujících operací, než zaznamená `429` stavový kód protokolu http:
> - 2 000 RSA 2 048-bitový software-klíč získat transakce
> - 1 000 RSA 2 048-bit HSM-Key GET Transactions
> - 125 RSA 4 096-bit HSM-Key GET Transactions
> - 124 RSA 4 096-bit HSM-Key GET Transactions a 8 RSA 2 048-bit HSM-Key GET Transactions

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Tajné kódy, klíče spravovaného účtu úložiště a transakce trezoru:

| Typ transakcí | Maximální počet transakcí povolených za 10 sekund, na trezor v oblasti<sup>1</sup> |
| --- | --- |
| Všechny transakce |2 000 |

Informace o tom, jak zpracovávat omezení v případě překročení těchto limitů, najdete v tématu [Azure Key Vault pokyny k omezování](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> limit pro všechny typy transakcí v rámci celého předplatného je pětkrát na limit trezoru klíčů. Například modul HSM – ostatní transakce v rámci předplatného jsou omezeny na 5 000 transakcí za 10 sekund na jedno předplatné.

### <a name="azure-private-link-integration"></a>Integrace s privátními propojeními Azure

> [!NOTE]
> Počet trezorů klíčů s povolenými soukromými koncovými body pro každé předplatné je upravitelný limit. Níže uvedený limit je nastaven jako výchozí. Pokud chcete pro vaši službu požádat o zvýšení limitu, pošlete prosím e-mail na adresu akv-privatelink@microsoft.com . Tyto žádosti schválíme na základě případu.

| Prostředek | Omezení |
| -------- | ----- |
| Soukromé koncové body na Trezor klíčů | 64 |
| Trezory klíčů s privátními koncovými body na předplatné | 400 |
