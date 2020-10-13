---
title: 'Žádost o digitální vlákna Azure se nezdařila se stavem: 403 (zakázáno)'
description: 'U žádosti o službu se nezdařily příčiny a řešení. Stav: 403 (zakázáno) u digitálních vláken Azure.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: bc4fbbc265bef00be27c890c3f090a49591dc415
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90562736"
---
# <a name="service-request-failed-status-403-forbidden"></a>Žádost o službu se nezdařila. Stav: 403 (zakázáno)

Tento článek popisuje příčiny a postup řešení pro příjem chyby 403 z žádostí o služby do digitálních vláken Azure. 

## <a name="symptoms"></a>Příznaky

K této chybě může dojít u mnoha typů žádostí o služby, které vyžadují ověření. Výsledkem je, že požadavek rozhraní API selhává a vrátí stav chyby `403 (Forbidden)` .

## <a name="causes"></a>Příčiny

### <a name="cause-1"></a>Příčina #1

Nejčastěji se tato chyba označuje, že vaše oprávnění k řízení přístupu na základě role (RBAC) pro službu nejsou nastavená správně. Celá řada akcí pro instanci digitálních vláken Azure vyžaduje, abyste měli u **instance, kterou se pokoušíte spravovat**, roli *vlastník (Preview) Azure* . 

### <a name="cause-2"></a>Příčina #2

Pokud ke komunikaci s digitálními podmnožinami Azure používáte klientskou aplikaci, k této chybě může dojít, protože registrace aplikace [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) nemá oprávnění nastavená pro službu Azure Digital probíhají.

K registraci aplikace je potřeba mít přístupová oprávnění nakonfigurovaná pro rozhraní API digitálních vláken Azure. Když se klientská aplikace ověřuje proti registraci aplikace, udělí se jim oprávnění, že se registrace aplikace nakonfigurovala.

## <a name="solutions"></a>Řešení

### <a name="solution-1"></a>#1 řešení

Prvním řešením je ověřit, jestli má váš uživatel Azure na instanci, kterou se pokoušíte spravovat, roli _**vlastníka (Preview) Azure Digital Revlákens**_ . Pokud tuto roli nemáte, nastavte ji.

Všimněte si, že tato role se liší od...
* role *vlastníka* v celém předplatném Azure. *Vlastník digitálních vláken Azure (Preview)* je role v rámci digitálních vláken Azure a je vymezená na tuto jednotlivou instanci digitálních vláken Azure.
* role *vlastníka* v digitálních prozdvojeních Azure. Jedná se o dvě odlišná role správy digitálních vláken Azure a *vlastník Azure Digital autoguards (Preview)* je role, která se má použít ke správě ve verzi Preview.

#### <a name="check-current-setup"></a>Kontrolovat aktuální nastavení

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Opravit problémy 

Pokud toto přiřazení role nemáte, měl by uživatel s rolí vlastníka v rámci vašeho **předplatného Azure** spustit následující příkaz, který uživateli Azure poskytne roli Azure Digital neplnících *vlastníka (Preview)* v **instanci digitálních vláken Azure**. 

Pokud jste vlastníkem předplatného, můžete tento příkaz Spustit sami. Pokud ne, obraťte se na vlastníka, aby tento příkaz spustili vaším jménem.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Další podrobnosti o tomto požadavku role a procesu přiřazení najdete v [části *Nastavení přístupových oprávnění uživatele* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) v tématu *Postupy: nastavení instance a ověřování (CLI nebo portál)*.

Pokud již máte přiřazení této role k problému 403, pokračujte dalším řešením.

### <a name="solution-2"></a>#2 řešení

Druhým řešením je ověřit, jestli má registrace aplikace služby Azure AD oprávnění nakonfigurovaná pro službu Azure Digital revlákens. Pokud není nakonfigurováno, nastavte je.

#### <a name="check-current-setup"></a>Kontrolovat aktuální nastavení

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Nejdřív ověřte, že nastavení oprávnění pro digitální vlákna Azure byla v registraci správně nastavená. Provedete to tak, že v řádku nabídek vyberete *manifest* a zobrazí se kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte tato pole v části `requiredResourceAccess` . Hodnoty by měly odpovídat hodnotám na snímku obrazovky níže:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Opravit problémy

Pokud se některá z těchto informací zobrazí jinak než popsané, postupujte podle pokynů v části Postup nastavení registrace aplikace v části [ *Nastavení oprávnění přístupu pro klientské aplikace* v tématu](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) *Postupy: nastavení instance a ověřování (CLI nebo portál)*.

## <a name="next-steps"></a>Další kroky

Přečtěte si instalační kroky pro vytvoření a ověření nové instance digitálního vlákna Azure:
* [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md)

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)