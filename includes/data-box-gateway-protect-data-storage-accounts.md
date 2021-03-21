---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582353"
---
Vaše zařízení je přidružené k účtu úložiště, který slouží jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a dva 512bitové přístupové klíče k úložišti přidružené k danému účtu úložiště.

Jeden z těchto klíčů slouží k ověřování při přístupu zařízení Azure Stack Edge k účtu úložiště. Druhý klíč slouží jako záložní, abyste mohli klíče pravidelně obměňovat.

Řada datacenter z bezpečnostních důvodů vyžaduje obměnu klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nedistribuujte heslo jiným uživatelům, pevným kódem nebo ho uložte kdekoli v prostém textu, který je přístupný ostatním uživatelům.
- Pokud se domníváte, že by mohlo dojít k ohrožení, znovu vygenerujte klíč účtu pomocí Azure Portal. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.