---
title: Správa rolí v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Zjistěte, jak získat přístup k pracovnímu prostoru Azure Machine Learning pomocí řízení přístupu na základě rolí (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270092"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Správa přístupu k pracovnímu prostoru Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spravovat přístup k pracovnímu prostoru Azure Machine Learning. [Řízení přístupu na základě rolí (RBAC)](/azure/role-based-access-control/overview) se používá ke správě přístupu k prostředkům Azure. Uživatelům ve službě Azure Active Directory jsou přiřazeny konkrétní role, které udělují přístup k prostředkům. Azure poskytuje předdefinované role a možnost vytvářet vlastní role.

## <a name="default-roles"></a>Výchozí role

Pracovní prostor Azure Machine Learning je prostředek Azure. Stejně jako ostatní prostředky Azure, když se vytvoří nový pracovní prostor Azure Machine Learning, je dodáván se třemi výchozími rolemi. Můžete přidat uživatele do pracovního prostoru a přiřadit je k jedné z těchto předdefinovaných rolí.

| Role | Úroveň přístupu |
| --- | --- |
| **Čtenář** | Akce jen pro čtení v pracovním prostoru. Čtenáři mohou vypsat a zobrazit datové zdroje v pracovním prostoru, ale nemohou tyto datové zdroje vytvářet ani aktualizovat. |
| **Přispěvatel** | Zobrazení, vytvoření, úprava nebo odstranění (pokud je to možné) datových zdrojů v pracovním prostoru. Přispěvatelé mohou například vytvořit experiment, vytvořit nebo připojit výpočetní cluster, odeslat spuštění a nasadit webovou službu. |
| **Vlastník** | Úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit (pokud je to možné) datové zdroje v pracovním prostoru. Kromě toho můžete změnit přiřazení rolí. |

> [!IMPORTANT]
> Přístup k rolím může být vymezen na více úrovnív Azure. Například někdo s přístupem vlastníka k pracovnímu prostoru nemusí mít přístup vlastníka ke skupině prostředků, která obsahuje pracovní prostor. Další informace naleznete v tématu [Jak RBAC funguje](/azure/role-based-access-control/overview#how-rbac-works).

Další informace o konkrétních předdefinovaných rolích najdete [v tématu Předdefinované role pro Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Správa přístupu k pracovnímu prostoru

Pokud jste vlastníkem pracovního prostoru, můžete přidat a odebrat role pro pracovní prostor. Můžete také přiřadit role uživatelům. Pomocí následujících odkazů zjistíte, jak spravovat přístup:
- [UI portálu Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [ROZHRANÍ API PRO ODPOČINEK](/azure/role-based-access-control/role-assignments-rest)
- [Šablony Azure Resource Manageru](/azure/role-based-access-control/role-assignments-template)

Pokud jste nainstalovali [rozhraní příkazu KLA Azure Machine Learning](reference-azure-machine-learning-cli.md), můžete také použít příkaz cli přiřadit role uživatelům.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Toto `user` pole je e-mailová adresa existujícího uživatele v instanci služby Azure Active Directory, kde existuje nadřazené předplatné pracovního prostoru. Zde je příklad použití tohoto příkazu:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Vytvoření vlastní role

Pokud jsou předdefinované role nedostatečné, můžete vytvořit vlastní role. Vlastní role mohly mít oprávnění ke čtení, zápisu, odstranění a výpočtu prostředků v tomto pracovním prostoru. Roli můžete zpřístupnit na určité úrovni pracovního prostoru, na úrovni určité skupiny prostředků nebo na určité úrovni předplatného.

> [!NOTE]
> Chcete-li vytvořit vlastní role v rámci tohoto prostředku, musíte být vlastníkem prostředku na této úrovni.

Chcete-li vytvořit vlastní roli, nejprve vytvořte soubor JSON definice role, který určuje oprávnění a rozsah pro roli. Následující příklad definuje vlastní roli s názvem "Data Scientist" vymezenou na určité úrovni pracovního prostoru:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

`AssignableScopes` Pole můžete změnit a nastavit rozsah této vlastní role na úrovni předplatného, na úrovni skupiny prostředků nebo na konkrétní úrovni pracovního prostoru.

Tato vlastní role může dělat vše v pracovním prostoru s výjimkou následujících akcí:

- Nelze vytvořit nebo aktualizovat výpočetní prostředek.
- Nelze odstranit výpočetní prostředek.
- Nelze přidávat, odstraňovat ani měnit přiřazení rolí.
- Pracovní prostor nelze odstranit.

Chcete-li nasadit tuto vlastní roli, použijte následující příkaz y Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po nasazení bude tato role k dispozici v zadaném pracovním prostoru. Teď můžete přidat a přiřadit tuto roli na webu Azure Portal. Nebo můžete přiřadit tuto roli uživateli `az ml workspace share` pomocí příkazu příkazu příkazu příkazu:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Další informace o vlastních rolích najdete [v tématu Vlastní role pro prostředky Azure](/azure/role-based-access-control/custom-roles).

Další informace o operacích (akcích) použitelných s vlastními rolemi naleznete v [tématu Operace zprostředkovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Nejčastější dotazy


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Otázka: Jaká oprávnění potřebná k provádění různých akcí ve službě Azure Machine Learning?

Následující tabulka je souhrn aktivit Azure Machine Learning a oprávnění potřebná k jejich provádění v nejmenším oboru. Jako příklad, pokud lze aktivitu provést s oborem pracovního prostoru (sloupec 4), pak všechny vyšší obor s tímto oprávněním bude také pracovat automaticky. Všechny cesty v této tabulce jsou `Microsoft.MachineLearningServices/`relativní **cesty** k .

| Aktivita | Obor na úrovni předplatného | Obor na úrovni skupiny prostředků | Obor na úrovni pracovního prostoru |
|---|---|---|---|
| Vytvoření nového pracovního prostoru | Není požadováno | Vlastník nebo přispěvatel | Není k dispozici (stane se vlastníkem nebo dědí roli s vyšším rozsahem po vytvoření) |
| Vytvoření nového výpočetního clusteru | Není požadováno | Není požadováno | Vlastník, přispěvatel nebo vlastní role umožňující:`workspaces/computes/write` |
| Vytvoření nového virtuálního počítače s poznámkovým blokem | Není požadováno | Vlastník nebo přispěvatel | Není možný |
| Vytvoření nové instance výpočetních prostředků | Není požadováno | Není požadováno | Vlastník, přispěvatel nebo vlastní role umožňující:`workspaces/computes/write` |
| Aktivita roviny dat, jako je odesílání spuštění, přístup k datům, nasazení modelu nebo publikování kanálu | Není požadováno | Není požadováno | Vlastník, přispěvatel nebo vlastní role umožňující:`workspaces/*/write` <br/> Všimněte si, že potřebujete také úložiště dat registrované v pracovním prostoru, aby MSI přístup k datům v účtu úložiště. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Otázka: Jak zobrazím všechny vlastní role v předplatném?

V příkazovém příkazu Konto Azure spusťte následující příkaz.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Otázka: Jak najdu definici role pro roli v mém předplatném?

V příkazovém příkazu Konto Azure spusťte následující příkaz. Všimněte `<role-name>` si, že by měl být ve stejném formátu vrácena výše uvedený příkaz.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Otázka: Jak lze aktualizovat definici role?

V příkazovém příkazu Konto Azure spusťte následující příkaz.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Všimněte si, že musíte mít oprávnění k celému oboru nové definice role. Například pokud tato nová role má obor přes tři odběry, musíte mít oprávnění pro všechny tři odběry. 

> [!NOTE]
> Aktualizace rolí může trvat 15 minut až hodinu použít ve všech přiřazení rolí v tomto oboru.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Otázka: Mohu definovat roli, která brání aktualizaci edice pracovního prostoru? 

Ano, můžete definovat roli, která brání aktualizaci edice pracovního prostoru. Vzhledem k tomu, že aktualizace pracovního prostoru je volání patch na `"NotActions"` objektu pracovního prostoru, můžete to provést umístěním následující akce v poli v definici JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Otázka: Jaká oprávnění jsou potřebná k provádění operací kvót v pracovním prostoru? 

K provedení jakékoli operace související s kvótou v pracovním prostoru potřebujete oprávnění na úrovni předplatného. To znamená, že k nastavení kvóty na úrovni předplatného nebo kvóty na úrovni pracovního prostoru pro spravované výpočetní prostředky může dojít pouze v případě, že máte oprávnění k zápisu v oboru předplatného. 


## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení podniku](concept-enterprise-security.md)
- [Bezpečné spouštění experimentů a odvození/skóre uvnitř virtuální sítě](how-to-enable-virtual-network.md)
- [Výuka: Modely vlaků](tutorial-train-models-with-aml.md)
- [Operace zprostředkovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
