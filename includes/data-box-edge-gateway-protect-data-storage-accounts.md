---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754244"
---
Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a úložiště dva 512bitové přístupové klíče přidružené k tomuto účtu úložiště.

Jeden z klíčů se používá k ověřování, když Data Box hraniční zařízení získá přístup k účtu úložiště. Další klíč se nachází v rezervy, takže můžete otočit klíče pravidelně.

Z bezpečnostních důvodů se vyžadují mnoho datových centrech obměny klíče. Doporučujeme, abyste postupovali podle těchto osvědčených postupů pro obměna klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě Chraňte klíč účtu. Není distribuovat ostatním uživatelům heslo, intenzivně kód nebo ji uložit kdekoli ve formátu prostého textu, který je přístupný ostatním uživatelům.
- [Znovu vygenerovat klíč účtu](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) přes Azure portal, pokud si myslíte, že může dojít k ohrožení bezpečnosti.
- Správce Azure by měl pravidelně změnit nebo znovu vygenerovat primární nebo sekundární klíč pomocí části úložiště na webu Azure portal a získat přímo přístup k účtu úložiště.