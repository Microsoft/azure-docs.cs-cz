---
title: Chyba ověřování Průzkumníka digitálních vláken Azure
description: Příčiny a řešení pro ověřování se nezdařilo. v Průzkumníku digitálních vláken Azure.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495028"
---
# <a name="authentication-failed"></a>Ověřování se nezdařilo

Tento článek popisuje příčiny a kroky řešení pro příjem chyby "ověřování se nezdařilo" při spuštění ukázky [Průzkumníka digitálních vláken Azure](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) na vašem místním počítači. 

## <a name="symptoms"></a>Příznaky

Při nastavování a spouštění aplikace Průzkumník digitálních vláken Azure se pokusy o ověření v aplikaci zobrazí s následující chybovou zprávou:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Snímek obrazovky s chybovou zprávou v Průzkumníkovi digitálních vláken Azure s následujícím textem: ověření se nezdařilo. Pokud aplikaci spouštíte lokálně, ujistěte se, že jste přihlášeni k Azure na hostitelském počítači, nebo například spuštěním příkazu AZ Login v příkazovém řádku, přihlášením k aplikaci Visual Studio nebo VS Code nebo nastavením proměnných prostředí. Pokud potřebujete další informace, přečtěte si soubor Readme nebo vyhledejte DefaultAzureCredential v dokumentaci k Azure. identity. Pokud používáte aplikaci ADT-Explorer, která je hostovaná v cloudu, ujistěte se prosím, že vaše hostující funkce Azure má nastavenou spravovanou identitu přiřazenou systémem. Další informace najdete v souboru Readme.":::

## <a name="causes"></a>Příčiny

### <a name="cause-1"></a>Příčina #1

Aplikace Průzkumník digitálních vláken Azure používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (součást `Azure.Identity` knihovny), která bude vyhledávat přihlašovací údaje v rámci místního prostředí.

Jako text chyby se může zobrazit tato chyba, pokud jste nezadali místní přihlašovací údaje pro `DefaultAzureCredential` výběr.

Další informace o používání místních přihlašovacích údajů v Průzkumníkovi digitálních vláken Azure najdete v části [*Nastavení místních přihlašovacích údajů Azure*](quickstart-adt-explorer.md#set-up-local-azure-credentials) v rychlém startu pro digitální vlákna Azure *: Prozkoumejte ukázkový scénář*.

### <a name="cause-2"></a>Příčina #2

K této chybě může dojít také v případě, že váš účet Azure nemá nastavená oprávnění řízení přístupu na základě rolí Azure (Azure RBAC) na instanci digitálních vláken Azure. Aby bylo možné získat přístup k datům ve vaší instanci, musíte mít na instanci, kterou se pokoušíte číst nebo spravovat, roli vlastníka dat služby Azure **Digital** dostupní data nebo **digitální vlákna Azure** . 

Další informace o zabezpečení a rolích v digitálních prozdvojeních Azure najdete v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md).

## <a name="solutions"></a>Řešení

### <a name="solution-1"></a>#1 řešení

Nejdřív ověřte, že jste pro aplikaci zadali potřebné přihlašovací údaje.

#### <a name="provide-local-credentials"></a>Zadat místní přihlašovací údaje

`DefaultAzureCredential` ověří službu pomocí informací z místního přihlášení Azure. Přihlašovací údaje Azure můžete zadat tak, že se přihlásíte k účtu Azure v místním okně [Azure CLI](/cli/azure/install-azure-cli) nebo v aplikaci Visual Studio nebo Visual Studio Code.

Můžete zobrazit typy přihlašovacích údajů, které `DefaultAzureCredential` akceptují, a také pořadí, ve kterém se pokoušejí, v dokumentaci k [identitě Azure pro DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Pokud jste již místně přihlášení k pravému účtu Azure a problém není vyřešen, pokračujte dalším řešením.

### <a name="solution-2"></a>#2 řešení

Pokud se snažíte data spravovat, ověřte, že váš uživatel Azure má roli **čtečky dat Azure Digital** reprezentujes v instanci digitálních vláken Azure. stačí, když se pokoušíte načíst jeho data, nebo **Pokud se** pokoušíte spravovat svá data v instanci.

Všimněte si, že tato role se liší od...
* předchozí název této role během období Preview je *vlastník digitálních vláken Azure (Preview)* (Tato role je stejná, ale změnil se název).
* role *vlastníka* v celém předplatném Azure. *Vlastníkem dat digitálních vláken Azure* je role v rámci digitálních vláken Azure, která je vymezená na tuto jednotlivou instanci digitálních vláken Azure.
* role *vlastníka* v digitálních prozdvojeních Azure. Jedná se o dvě odlišná role správy digitálních vláken Azure a *vlastník dat pro digitální vlákna Azure* je role, která se má použít ke správě.

 Pokud tuto roli nemáte, nastavte ji tak, aby se problém vyřešil.

#### <a name="check-current-setup"></a>Kontrolovat aktuální nastavení

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Opravit problémy 

Pokud toto přiřazení role nemáte, měl by uživatel s rolí vlastníka v rámci vašeho **předplatného Azure** spustit následující příkaz, který uživateli Azure poskytne příslušnou roli pro **instanci digitálních vláken Azure**. 

Pokud jste vlastníkem předplatného, můžete tento příkaz Spustit sami. Pokud nejste, obraťte se na vlastníka, aby tento příkaz spouštěl vaším jménem. Název role je buď **vlastník dat** pro přístup pro úpravy, nebo **čtečku dat digitálního vlákna Azure** , která je pro přístup pro čtení.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Další podrobnosti o tomto požadavku role a procesu přiřazení najdete v [části *Nastavení přístupových oprávnění uživatele*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) v tématu *Postupy: nastavení instance a ověřování (CLI nebo portál)*.

## <a name="next-steps"></a>Další kroky

Přečtěte si instalační kroky pro vytvoření a ověření nové instance digitálního vlákna Azure:
* [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md)

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)
