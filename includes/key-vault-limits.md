Klíč transakce (maximální počet povolených za 10 sekund, za transakcí trezoru každou oblast<sup>1</sup>):

|Typ klíče|HSM-Key<br>Vytvoření klíče|Klíč HSM<br>Všechny ostatní transakce|Software-key<br>Vytvoření klíče|Software-key<br>Všechny ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Tajné klíče, spravovat klíče účtu úložiště a transakce úložiště:
| Typ transakce | Maximální počet povolených za 10 sekund, za transakcí trezoru podle oblastí<sup>1</sup> |
| --- | --- |
| Všechny transakce |2000 |
|

V tématu [Azure Key Vault omezení pokyny](../articles/key-vault/key-vault-ovw-throttling.md) informace o tom, jak zpracovat omezení při překročení těchto limitů.

<sup>1</sup> existuje omezení celé předplatné pro všechny typy transakcí, který je 5 x za limit trezoru klíčů. Například HSM - dalších transakcí podle předplatného jsou omezeny na 5000 transakce za 10 sekund na jedno předplatné.
