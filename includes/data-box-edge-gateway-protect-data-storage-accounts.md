---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562116"
---
Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště je řízen předplatným a 2 512 přístupovými klíči úložiště, které jsou přidružené k tomuto účtu úložiště.

Jeden z klíčů se používá pro ověřování, když Azure Stack hraniční zařízení přistupuje k účtu úložiště. Druhý klíč se uchovává v rezervě, takže můžete klíče pravidelně střídat.

Z bezpečnostních důvodů mnoho datových Center vyžaduje rotaci klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě Zabezpečte svůj klíč účtu. Nedistribuujte heslo jiným uživatelům, pevným kódem nebo ho uložte kdekoli v prostém textu, který je přístupný ostatním.
- Pokud se domníváte, že by mohlo dojít k ohrožení, znovu vygenerujte klíč účtu pomocí Azure Portal. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.