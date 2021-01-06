---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934526"
---
Trezor klíčů podporuje až 1 024 položek zásad přístupu, z nichž každá uděluje odlišnou sadu oprávnění ke konkrétnímu objektu zabezpečení. Z důvodu tohoto omezení doporučujeme přiřadit skupinám uživatelů zásady přístupu, pokud je to možné, a ne jednotlivé uživatele. Používání skupin výrazně usnadňuje správu oprávnění pro více lidí ve vaší organizaci. Další informace najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md) .

Úplné podrobnosti o Key Vault řízení přístupu najdete v tématu [Azure Key Vault Security: Správa identit a přístupu](../articles/key-vault/general/security-overview.md#identity-management).