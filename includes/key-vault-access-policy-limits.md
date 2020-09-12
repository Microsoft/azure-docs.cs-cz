---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380812"
---
Trezor klíčů podporuje až 1024 záznamů zásad přístupu. Každá položka uděluje pro určitý objekt zabezpečení jedinečnou sadu oprávnění. Z důvodu tohoto omezení doporučujeme přiřadit skupinám uživatelů zásady přístupu, pokud je to možné, a ne jednotlivé uživatele. Používání skupin výrazně usnadňuje správu oprávnění pro více lidí ve vaší organizaci. Další informace najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups) .

Úplné podrobnosti o Key Vault řízení přístupu najdete v tématu [Azure Key Vault Security: Správa identit a přístupu](/azure/key-vault/general/overview-security#identity-and-access-management). 
