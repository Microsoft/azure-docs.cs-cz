---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562116"
---
Vaše zařízení je přidružené k účtu úložiště, který slouží jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a dva 512bitové přístupové klíče k úložišti přidružené k danému účtu úložiště.

Jeden z těchto klíčů slouží k ověřování při přístupu zařízení Azure Stack Edge k účtu úložiště. Druhý klíč slouží jako záložní, abyste mohli klíče pravidelně obměňovat.

Řada datacenter z bezpečnostních důvodů vyžaduje obměnu klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nesdělujte heslo jiným uživatelům, nevkládejte ho přímo do kódu a neukládejte ho v prostém textu nikde, kde by k němu měli přístup ostatní uživatelé.
- Pokud se domníváte, že by mohlo dojít k ohrožení, znovu vygenerujte klíč účtu pomocí Azure Portal. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.