---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753288"
---
Služba Azure Key Vault podporuje dva typy prostředků: trezory a spravované HSM. Následující dvě části popisují omezení služby pro každé z nich.

### <a name="resource-type-vault"></a>Typ prostředku: trezor

Tato část popisuje omezení služby pro typ prostředku `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klíčové transakce (maximální počet transakcí povolených za 10 sekund, na trezor v oblasti<sup>1</sup>):

|Typ klíče|Klíč HSM<br>VYTVOŘIT klíč|Klíč HSM<br>Všechny ostatní transakce|Softwarový klíč<br>VYTVOŘIT klíč|Softwarový klíč<br>Všechny ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2 048-bit|5|1 000|10|2 000|
|RSA 3 072-bit|5|250|10|500|
|RSA 4 096-bit|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|
||||||

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

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Tajné kódy, klíče spravovaného účtu úložiště a transakce trezoru:

| Typ transakcí | Maximální počet transakcí povolených za 10 sekund, na trezor v oblasti<sup>1</sup> |
| --- | --- |
| Všechny transakce |2 000 |

Informace o tom, jak zpracovávat omezení v případě překročení těchto limitů, najdete v tématu [Azure Key Vault pokyny k omezování](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> limit pro všechny typy transakcí v rámci celého předplatného je pětkrát na limit trezoru klíčů. Například modul HSM – ostatní transakce v rámci předplatného jsou omezeny na 5 000 transakcí za 10 sekund na jedno předplatné.

#### <a name="backup-keys-secrets-certificates"></a>Klíče pro zálohování, tajné klíče a certifikáty

Při zálohování objektu trezoru klíčů, jako je tajný klíč, klíč nebo certifikát, bude operace zálohování stahovat objekt jako zašifrovaný objekt BLOB. Tento objekt BLOB není možné dešifrovat mimo Azure. Pokud chcete získat použitelná data z tohoto objektu blob, musíte obnovit objekt blob do trezoru klíčů v rámci stejného předplatného Azure a geografické oblasti Azure.

| Typ transakcí | Maximální povolená verze objektů trezoru klíčů |
| --- | --- |
| Zálohovat jednotlivý klíč, tajný kód, certfiicate |500 |

> [!NOTE]
> Při pokusu o zálohování objektu klíče, tajného kódu nebo certifikátu s více verzemi, než je překročen limit, dojde k chybě. Není možné odstranit předchozí verze klíče, tajného kódu ani certifikátu. 

#### <a name="azure-private-link-integration"></a>Integrace s privátními propojeními Azure

> [!NOTE]
> Počet trezorů klíčů s povolenými soukromými koncovými body pro každé předplatné je upravitelný limit. Níže uvedený limit je nastaven jako výchozí. Pokud chcete pro vaši službu požádat o zvýšení limitu, vytvořte žádost o podporu a vyhodnotí se případ od případu.

| Prostředek | Omezení |
| -------- | -----:|
| Soukromé koncové body na Trezor klíčů | 64 |
| Trezory klíčů s privátními koncovými body na předplatné | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Typ prostředku: spravovaný HSM (Preview)

Tato část popisuje omezení služby pro typ prostředku `managed HSM` .

#### <a name="object-limits"></a>Omezení objektu

|Položka|Omezení|
|----|------:|
Počet instancí HSM na předplatné a oblast|1 (ve verzi Preview)
Počet klíčů na jeden fond HSM|5000
Počet verzí na klíč|100
Počet vlastních definic rolí na HSM|50
Počet přiřazení rolí v oboru HSM|50
Počet přiřazení role v každém oboru jednotlivých klíčů|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Omezení transakcí pro operace správy (počet operací za sekundu na instanci HSM)
|Operace |Počet operací za sekundu|
|----|------:|
Všechny operace RBAC<br/>(zahrnuje všechny operace CRUD pro definice rolí a přiřazení rolí)|5
Úplné zálohování/obnovení HSM<br/>(podporovaná je jenom jedna souběžná operace zálohování nebo obnovení na instanci HSM)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Omezení transakcí pro kryptografické operace (počet operací za sekundu na instanci HSM)

- Každá spravovaná instance HSM představuje 3 oddíly modulu HSM s vyrovnáváním zatížení. Omezení propustnosti jsou funkce základní hardwarové kapacity přidělené pro každý oddíl. Následující tabulky znázorňují maximální propustnost, která je k dispozici alespoň v jednom oddílu. Pokud jsou k dispozici všechny tři oddíly, může být skutečná propustnost až 3x vyšší.
- Zjištěné limity propustnosti předpokládají, že se k dosažení maximální propustnosti používá jeden jeden klíč. Pokud například použijete jeden klíč RSA-2048, bude maximální propustnost 1100 operací podepisování. Pokud použijete 1100 různých klíčů s 1 transakcí za sekundu, nebudou moci dosáhnout stejné propustnosti.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operace klíčů RSA (počet operací za sekundu na instanci HSM)

|Operace|2048 – bit|3072 – bit|4096 – bit|
|--|--:|--:|--:|
Vytvořit klíč|1| 1| 1
Odstranit klíč (obnovitelné odstranění)|10|10|10 
Vymazat klíč|10|10|10 
Záložní klíč|10|10|10 
Obnovit klíč|10|10|10 
Získání informací o klíči|1100|1100|1100
Šifrování|10000|10000|6000
Dešifrování|1100|360|160
Balí|10000|10000|6000
Rozbalení|1100|360|160
Znaménko|1100|360|160
Ověření|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operace s klíči ES (počet operací za sekundu na instanci HSM)

Tato tabulka popisuje počet operací za sekundu pro každý typ křivky.

|Operace|P-256|P-256|P-384|P-521|
|---|---:|---:|---:|---:|
Vytvořit klíč| 1| 1| 1| 1
Odstranit klíč (obnovitelné odstranění)|10|10|10|10
Vymazat klíč|10|10|10|10
Záložní klíč|10|10|10|10
Obnovit klíč|10|10|10|10
Získání informací o klíči|1100|1100|1100|1100
Znaménko|260|260|165|56
Ověření|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operace s klíči AES (počet operací za sekundu na instanci HSM)
- Operace šifrování a dešifrování předpokládají velikost paketu 4KB.
- Omezení propustnosti pro šifrování/dešifrování se vztahují na algoritmy AES-CBC a AES-GCM.
- Omezení propustnosti pro zabalení a rozbalení se vztahují na algoritmus AES-KW.

|Operace|128 – bit|192 – bit|256 – bit|
|--|--:|--:|--:|
Vytvořit klíč|1| 1| 1
Odstranit klíč (obnovitelné odstranění)|10|10|10
Vymazat klíč|10|10|10
Záložní klíč|10|10|10
Obnovit klíč|10|10|10
Získání informací o klíči|1100|1100|1100
Šifrování|8000|8000 |8000 
Dešifrování|8000|8000|8000
Balí|9000|9000|9000
Rozbalení|9000|9000|9000

