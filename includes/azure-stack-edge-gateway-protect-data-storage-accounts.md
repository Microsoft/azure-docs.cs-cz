---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900843"
---
Vaše zařízení je přidružené k účtu úložiště, který slouží jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a dva 512bitové přístupové klíče k úložišti přidružené k danému účtu úložiště.

Jeden z klíčů se používá pro ověřování, když Data Box Edge zařízení přistupuje k účtu úložiště. Druhý klíč slouží jako záložní, abyste mohli klíče pravidelně obměňovat.

Řada datacenter z bezpečnostních důvodů vyžaduje obměnu klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nesdělujte heslo jiným uživatelům, nevkládejte ho přímo do kódu a neukládejte ho v prostém textu nikde, kde by k němu měli přístup ostatní uživatelé.
- Pokud se domníváte, že by mohlo dojít k ohrožení, [znovu vygenerujte klíč účtu](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) pomocí Azure Portal.
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.
