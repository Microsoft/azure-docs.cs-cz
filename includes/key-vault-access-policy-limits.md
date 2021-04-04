---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934526"
---
Trezor klíčů podporuje až 1024 záznamů zásad přístupu. Každá položka uděluje pro určitý objekt zabezpečení jedinečnou sadu oprávnění. Z důvodu tohoto omezení doporučujeme přiřadit skupinám uživatelů zásady přístupu, pokud je to možné, a ne jednotlivé uživatele. Používání skupin výrazně usnadňuje správu oprávnění pro více lidí ve vaší organizaci. Další informace najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md) .

Úplné podrobnosti o Key Vault řízení přístupu najdete v tématu [Azure Key Vault Security: Správa identit a přístupu](../articles/key-vault/general/security-overview.md#identity-management).