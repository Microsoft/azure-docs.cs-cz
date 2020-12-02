---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466852"
---
Vaše zařízení je přidružené k účtu úložiště, který slouží jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a dva 512bitové přístupové klíče k úložišti přidružené k danému účtu úložiště.

Jeden z klíčů se používá pro ověřování, když Data Box Edge zařízení přistupuje k účtu úložiště. Druhý klíč slouží jako záložní, abyste mohli klíče pravidelně obměňovat.

Řada datacenter z bezpečnostních důvodů vyžaduje obměnu klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nesdělujte heslo jiným uživatelům, nevkládejte ho přímo do kódu a neukládejte ho v prostém textu nikde, kde by k němu měli přístup ostatní uživatelé.
- Pokud se domníváte, že by mohlo dojít k ohrožení, [znovu vygenerujte klíč účtu](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) pomocí Azure Portal.
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.