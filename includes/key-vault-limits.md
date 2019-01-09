---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9a39abf77a7396302f93e5a423271402b7c3edb3
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084000"
---
Klíč transakce (maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup>):

|Typ klíče|Klíč HSM<br>Vytvoření klíče|Klíč HSM<br>Veškeré ostatní transakce|Softwarový klíč<br>Vytvoření klíče|Softwarový klíč<br>Veškeré ostatní transakce|
|:---|---:|---:|---:|---:|
|RSA 2048 bitů|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Výše uvedené mezní hodnoty jsou váha a vynucení je na jejich součet. 125 RSA-HSM - 4 tisíc operací a 0 RSA-HSM - 2 kb / s, nebo 124 RSA-HSM - 4 kB a 16 RSA-HSM - 2k můžete provést. Následně ve stejném intervalu 10 sekundách žádné jiné operace způsobí výjimku AKV klienta.

> [!NOTE]
> Když se podíváte v následující tabulce, uvidíte, že pro podporovaný software klíče povolujeme, že 2000 transakce za 10 sekund a za HSM 2048bitových klíčů umožňujeme 1000 transakcí za 10 sekund. Poměr podporovaný software transakce 3072 klíče 2048 klíče je 500/2000 nebo 0.4. To znamená, pokud zákazníka nemá 500 3072 klíče transakce za 10 sekund, aby dosáhnout maximálního limitu a nemůže provádět žádné další klíčové operace. 
   
|Typ klíče  | Softwarový klíč |Klíč HSM  |
|---------|---------|---------|
|RSA 2048 bitů     |    2000     |   1000    |
|RSA 3072-bit     |     500    |    250     |
|RSA 4096-bit     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|P-384 ECC     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Tajné kódy, spravovaných klíčů účtu úložiště a transakce úložiště:
| Typ transakce | Maximální počet povolených za 10 sekund za transakcí úložiště na oblast<sup>1</sup> |
| --- | --- |
| Všechny transakce |2000 |
|

Zobrazit [pokyny k omezování služby Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informace o tom, jak řešit omezování, když překročíte tyto limity.

<sup>1</sup> platí limit úrovni předplatného pro všechny typy transakcí, která je 5 x za limit služby key vault. Pro příklad, HSM, že ostatní transakce podle předplatného jsou omezené na 5000 transakcí za 10 sekund na jedno předplatné.
