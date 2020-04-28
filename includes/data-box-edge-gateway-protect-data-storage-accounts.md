---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75468101"
---
Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště je řízen předplatným a 2 512 přístupovými klíči úložiště, které jsou přidružené k tomuto účtu úložiště.

Jeden z klíčů se používá pro ověřování, když Data Box Edge zařízení přistupuje k účtu úložiště. Druhý klíč se uchovává v rezervě, takže můžete klíče pravidelně střídat.

Z bezpečnostních důvodů mnoho datových Center vyžaduje rotaci klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě Zabezpečte svůj klíč účtu. Nedistribuujte heslo jiným uživatelům, pevným kódem nebo ho uložte kdekoli v prostém textu, který je přístupný ostatním.
- Pokud se domníváte, že by mohlo dojít k ohrožení, znovu vygenerujte klíč účtu pomocí Azure Portal. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.