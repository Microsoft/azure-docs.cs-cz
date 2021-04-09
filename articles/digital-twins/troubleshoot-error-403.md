---
title: 'Žádost o digitální vlákna Azure se nezdařila se stavem: 403 (zakázáno)'
description: 'U žádosti o službu se nezdařily příčiny a řešení. Stav: 403 (zakázáno) u digitálních vláken Azure.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100545696"
---
# <a name="service-request-failed-status-403-forbidden"></a>Žádost o službu se nezdařila. Stav: 403 (zakázáno)

Tento článek popisuje příčiny a postup řešení pro příjem chyby 403 z žádostí o služby do digitálních vláken Azure. 

## <a name="symptoms"></a>Příznaky

K této chybě může dojít u mnoha typů žádostí o služby, které vyžadují ověření. Výsledkem je, že požadavek rozhraní API selhává a vrátí stav chyby `403 (Forbidden)` .

## <a name="causes"></a>Příčiny

### <a name="cause-1"></a>Příčina #1

Tato chyba nejčastěji znamená, že oprávnění k řízení přístupu na základě role Azure (RBAC) pro službu nejsou nastavena správně. Celá řada akcí pro instanci digitálních vláken Azure vyžaduje, abyste měli v **instanci, kterou se pokoušíte spravovat**, roli *vlastníka dat digitálních vláken Azure* . 

### <a name="cause-2"></a>Příčina #2

Pokud používáte klientskou aplikaci ke komunikaci s digitálními událostmi Azure, které se ověřují při [registraci aplikace](how-to-create-app-registration.md), může k této chybě dojít, protože registrace vaší aplikace nemá oprávnění nastavená pro službu Azure Digital probíhají.

Registrace aplikace musí mít oprávnění k přístupu nakonfigurovaná pro rozhraní API digitálních vláken Azure. Když se klientská aplikace ověřuje proti registraci aplikace, udělí se jim oprávnění, že se registrace aplikace nakonfigurovala.

## <a name="solutions"></a>Řešení

### <a name="solution-1"></a>#1 řešení

Prvním řešením je ověřit, jestli má váš uživatel Azure na instanci, kterou se pokoušíte spravovat, roli _**vlastníka dat s digitálními**_ úlohami Azure. Pokud tuto roli nemáte, nastavte ji.

Všimněte si, že tato role se liší od...
* předchozí název této role během období Preview je *vlastník digitálních vláken Azure (Preview)* (Tato role je stejná, ale změnil se název).
* role *vlastníka* v celém předplatném Azure. *Vlastníkem dat digitálních vláken Azure* je role v rámci digitálních vláken Azure, která je vymezená na tuto jednotlivou instanci digitálních vláken Azure.
* role *vlastníka* v digitálních prozdvojeních Azure. Jedná se o dvě odlišná role správy digitálních vláken Azure a *vlastník dat pro digitální vlákna Azure* je role, která se má použít ke správě.

#### <a name="check-current-setup"></a>Kontrolovat aktuální nastavení

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Opravit problémy 

Pokud toto přiřazení role nemáte, měl by uživatel s rolí vlastníka v rámci vašeho **předplatného Azure** spustit následující příkaz, který vašemu uživateli Azure poskytne roli *vlastníka dat Azure s digitálními* úlohami v **instanci digitálních vláken Azure**. 

Pokud jste vlastníkem předplatného, můžete tento příkaz Spustit sami. Pokud nejste, obraťte se na vlastníka, aby tento příkaz spouštěl vaším jménem.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Další podrobnosti o tomto požadavku role a procesu přiřazení najdete v [části *Nastavení přístupových oprávnění uživatele*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) v tématu *Postupy: nastavení instance a ověřování (CLI nebo portál)*.

Pokud toto přiřazení role už máte *a* k ověření klientské aplikace používáte registraci aplikace Azure AD, můžete pokračovat k dalšímu řešení, pokud toto řešení nevyřešilo problém 403.

### <a name="solution-2"></a>#2 řešení

Pokud k ověření klientské aplikace používáte registraci aplikace Azure AD, druhým možným řešením je ověřit, jestli má registrace aplikace oprávnění nakonfigurovaná pro službu Azure Digital prokážed. Pokud tato nastavení nejsou nakonfigurovaná, nastavte je.

#### <a name="check-current-setup"></a>Kontrolovat aktuální nastavení

Pokud chcete zjistit, jestli jsou oprávnění správně nakonfigurovaná, přejděte na [stránku Přehled registrace aplikace Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) v Azure Portal. Tuto stránku můžete získat sami tak, že na portálu vyhledáte *Registrace aplikací* na panelu hledání.

Přepněte na kartu *všechny aplikace* a zobrazte všechny registrace aplikací, které byly vytvořeny v rámci vašeho předplatného.

V seznamu by se měla zobrazit registrace aplikace, kterou jste právě vytvořili. Výběrem této informace otevřete její podrobnosti.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Registrace aplikací stránka v Azure Portal":::

Nejdřív ověřte, že nastavení oprávnění pro digitální vlákna Azure byla v registraci správně nastavená. Provedete to tak, že v řádku nabídek vyberete *manifest* a zobrazí se kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte tato pole v části `requiredResourceAccess` . Hodnoty by měly odpovídat hodnotám na snímku obrazovky níže:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Zobrazení portálu manifestu pro registraci aplikace Azure AD":::

V dalším kroku vyberte *oprávnění rozhraní API* z řádku nabídek, abyste ověřili, že tato registrace aplikace obsahuje oprávnění ke čtení a zápisu pro digitální vlákna Azure. Měla by se zobrazit položka podobná této:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Zobrazení portálu oprávnění rozhraní API pro registraci aplikace Azure AD, které zobrazuje přístup pro čtení a zápis pro digitální vlákna Azure":::

#### <a name="fix-issues"></a>Opravit problémy

Pokud se některá z těchto informací zobrazí jinak než popsané, postupujte podle pokynů v tématu jak nastavit registraci aplikace v tématu [*Postupy: Vytvoření registrace aplikace*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si instalační kroky pro vytvoření a ověření nové instance digitálního vlákna Azure:
* [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md)

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)
