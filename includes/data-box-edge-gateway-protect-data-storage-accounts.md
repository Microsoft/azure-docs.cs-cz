---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468101"
---
Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště je řízen předplatným a dvěma 512bitovými přístupovými klíči úložiště přidruženými k tomuto účtu úložiště.

Jeden z klíčů se používá pro ověřování, když zařízení Data Box Edge přistupuje k účtu úložiště. Druhý klíč je držen v záloze, takže můžete klíče pravidelně otáčet.

Z bezpečnostních důvodů mnoho datových center vyžaduje střídání klíčů. Doporučujeme dodržovat tyto doporučené postupy pro střídání klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě chraňte klíč svého účtu. Nedistribuujte heslo ostatním uživatelům, nezakódujte ho ani ho neukládejte kamkoli ve formátu prostého textu, který je přístupný ostatním uživatelům.
- Pokud si myslíte, že by mohl být ohrožen, obnovte klíč účtu prostřednictvím portálu Azure. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).
- Správce Azure by měl pravidelně měnit nebo regenerovat primární nebo sekundární klíč pomocí části Úložiště na webu Azure Portal pro přímý přístup k účtu úložiště.