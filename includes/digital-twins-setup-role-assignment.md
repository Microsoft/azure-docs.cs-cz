---
author: baanders
description: zahrnutý soubor pro krok přístupových oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551711"
---
Funkce digitálních vláken Azure používá [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) pro řízení přístupu na základě role (RBAC). To znamená, že před tím, než může uživatel provést volání datové roviny do vaší instance digitálního vlákna Azure, musí být tomuto uživateli přiřazena role s příslušnými oprávněními.

U digitálních vláken Azure je tato role _**vlastníkem dat v Azure**_. Další informace o rolích a zabezpečení najdete v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](../articles/digital-twins/concepts-security.md).

V této části se dozvíte, jak vytvořit přiřazení role pro uživatele v instanci digitálního vlákna Azure pomocí e-mailu tohoto uživatele v tenantovi Azure AD na vašem předplatném Azure. V závislosti na vaší roli ve vaší organizaci můžete nastavit toto oprávnění pro sebe nebo ji nastavit jménem někoho jiného, kdo bude spravovat instanci digitálních vláken Azure.

### <a name="assign-the-role"></a>Přiřazení role

Pokud chcete uživateli udělit oprávnění ke správě instance digitálních vláken Azure, musíte jim přiřadit roli _**vlastníka dat Azure Digital**_ v rámci této instance.

> [!NOTE]
> Všimněte si, že se tato role liší od role *vlastníka* Azure AD, kterou je také možné přiřadit v oboru instance digitálních vláken Azure. Jedná se o dvě odlišné role správy a *vlastník* Azure AD neuděluje přístup k funkcím roviny dat, které jsou uděleny s *vlastníkem dat digitálních vláken Azure*.