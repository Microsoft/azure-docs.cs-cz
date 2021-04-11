---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285189"
---
Vaše zařízení je přidružené k účtu úložiště, který slouží jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a dva 512bitové přístupové klíče k úložišti přidružené k danému účtu úložiště.

Jeden z těchto klíčů slouží k ověřování při přístupu zařízení Azure Stack Edge k účtu úložiště. Druhý klíč slouží jako záložní, abyste mohli klíče pravidelně obměňovat.

Řada datacenter z bezpečnostních důvodů vyžaduje obměnu klíčů. Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nesdělujte heslo jiným uživatelům, nevkládejte ho přímo do kódu a neukládejte ho v prostém textu nikde, kde by k němu měli přístup ostatní uživatelé.
- Pokud se domníváte, že by mohlo dojít k ohrožení, [znovu vygenerujte klíč účtu](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) pomocí Azure Portal.
- Správce Azure by měl primární nebo sekundární klíč pravidelně měnit nebo obnovovat pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.
- K ochraně dat ve vašem účtu služby Azure Storage můžete použít také vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Další informace o tom, jak zabezpečit data, najdete v tématu [Povolení klíčů spravovaných zákazníkem pro váš účet Azure Storage](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
