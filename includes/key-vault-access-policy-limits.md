---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026500"
---
Trezor klíčů podporuje až 1 024 položek zásad přístupu, z nichž každá uděluje odlišnou sadu oprávnění ke konkrétnímu objektu zabezpečení. Z důvodu tohoto omezení doporučujeme přiřadit skupinám uživatelů zásady přístupu, pokud je to možné, a ne jednotlivé uživatele. Používání skupin výrazně usnadňuje správu oprávnění pro více lidí ve vaší organizaci. Další informace najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md) .

Úplné podrobnosti o Key Vault řízení přístupu najdete v tématu [Azure Key Vault Security: Správa identit a přístupu](../articles/key-vault/general/overview-security.md#identity-and-access-management).