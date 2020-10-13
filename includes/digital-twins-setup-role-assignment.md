---
author: baanders
description: zahrnutý soubor pro krok přístupových oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87408299"
---
Funkce digitálních vláken Azure používá [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) pro řízení přístupu na základě role (RBAC). To znamená, že před tím, než může uživatel provést volání datové roviny do vaší instance digitálního vlákna Azure, musí být tomuto uživateli přiřazena role s příslušnými oprávněními.

U digitálních vláken Azure je tato role _**vlastníkem Azure Digital Revlákens (Preview)**_. Další informace o rolích a zabezpečení najdete v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](../articles/digital-twins/concepts-security.md).

V této části se dozvíte, jak vytvořit přiřazení role pro uživatele v instanci digitálního vlákna Azure pomocí e-mailu tohoto uživatele v tenantovi Azure AD na vašem předplatném Azure. V závislosti na vaší roli ve vaší organizaci můžete nastavit toto oprávnění pro sebe nebo ji nastavit jménem někoho jiného, kdo bude spravovat instanci digitálních vláken Azure.

### <a name="assign-the-role"></a>Přiřazení role

Pokud chcete uživatelům udělit oprávnění ke správě instance digitálních vláken Azure, musíte jim přiřadit roli _**vlastník Azure Digital Realiass (Preview)**_ v rámci této instance.

> [!NOTE]
> Všimněte si, že se tato role liší od role *vlastníka* Azure AD, kterou je také možné přiřadit v oboru instance digitálních vláken Azure. Jedná se o dvě odlišné role správy a *vlastník* Azure AD neuděluje přístup k funkcím roviny dat, které jsou uděleny s *vlastníkem digitálních vláken Azure (Preview)*.