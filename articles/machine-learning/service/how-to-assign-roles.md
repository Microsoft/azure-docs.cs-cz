---
title: Správa rolí v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat přístup k pracovnímu prostoru Azure Machine Learning pomocí řízení přístupu na základě role (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: b5f42ac015ff492b67aa73c799ec1d952b468ed0
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932179"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Správa přístupu k pracovnímu prostoru Azure Machine Learning
[!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak spravovat přístup k pracovnímu prostoru Azure Machine Learning. [Řízení přístupu na základě role (RBAC)](/azure/role-based-access-control/overview) se používá ke správě přístupu k prostředkům Azure. Uživatelům v Azure Active Directory jsou přiřazeny konkrétní role, které udělují přístup k prostředkům. Azure poskytuje jak předdefinované role, tak i možnost vytvářet vlastní role.

## <a name="default-roles"></a>Výchozí role

Azure Machine Learning pracovní prostor je prostředek Azure. Podobně jako u jiných prostředků Azure se při vytvoření nového pracovního prostoru Azure Machine Learning dodává se třemi výchozími rolemi. Do pracovního prostoru můžete přidat uživatele a přiřadit jim jednu z těchto integrovaných rolí.

| Role | Úroveň přístupu |
| --- | --- |
| **Čtenář** | Akce jen pro čtení v pracovním prostoru. Čtenáři můžou vypisovat a zobrazovat prostředky v pracovním prostoru, ale nemůžou tyto prostředky vytvářet ani aktualizovat. |
| **Přispěvatel** | Zobrazení, vytvoření, úprava nebo odstranění prostředků (kde je k dispozici) v pracovním prostoru. Přispěvatelé můžou například vytvořit experiment, vytvořit nebo připojit výpočetní cluster, odeslat běh a nasadit webovou službu. |
| **Vlastník** | Úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit (kde se vztahují) prostředky v pracovním prostoru. Kromě toho můžete změnit přiřazení rolí. |

> [!IMPORTANT]
> Přístup k rolím může být v Azure omezený na více úrovní. Například někdo s přístupem vlastníka k pracovnímu prostoru nemusí mít oprávnění vlastníka ke skupině prostředků, která obsahuje pracovní prostor. Další informace najdete v tématu [jak funkce RBAC funguje](/azure/role-based-access-control/overview#how-rbac-works).

Další informace o konkrétních předdefinovaných rolích najdete v tématu [předdefinované role pro Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Správa přístupu k pracovnímu prostoru

Pokud jste vlastníkem pracovního prostoru, můžete přidat a odebrat role pro pracovní prostor. Role můžete přiřadit také uživatelům. Pomocí následujících odkazů zjistíte, jak spravovat přístup:
- [Azure Portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Šablony Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Pokud jste nainstalovali [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), můžete k přiřazení rolí uživatelům použít taky příkaz CLI.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Pole `user` je e-mailová adresa stávajícího uživatele v instanci Azure Active Directory, kde je nadřazený odběr pracovního prostoru. Tady je příklad, jak použít tento příkaz:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Vytvoření vlastní role

Pokud jsou předdefinované role nedostatečné, můžete vytvořit vlastní role. Vlastní role můžou mít v tomto pracovním prostoru oprávnění ke čtení, zápisu, odstranění a výpočtu prostředků. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného.

> [!NOTE]
> Abyste mohli vytvářet vlastní role v rámci tohoto prostředku, musíte být vlastníkem prostředku na této úrovni.

Chcete-li vytvořit vlastní roli, nejprve Sestavte soubor JSON definice role, který určuje oprávnění a rozsah této role. Následující příklad definuje vlastní roli s názvem "data odborníka" vymezená na konkrétní úrovni pracovního prostoru:

:`data_scientist_role.json`
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

Pole `AssignableScopes` můžete změnit tak, aby se obor této vlastní role nastavil na úrovni předplatného, na úrovni skupiny prostředků nebo na konkrétní úrovni pracovního prostoru.

Tato vlastní role může dělat všechno v pracovním prostoru s výjimkou následujících akcí:

- Nedokáže vytvořit ani aktualizovat výpočetní prostředek.
- Nejde odstranit výpočetní prostředek.
- Nemůže přidávat, odstraňovat ani měnit přiřazení rolí.
- Pracovní prostor nelze odstranit.

K nasazení této vlastní role použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po nasazení bude tato role k dispozici v zadaném pracovním prostoru. Nyní můžete tuto roli přidat a přiřadit v Azure Portal. Nebo můžete tuto roli přiřadit uživateli pomocí příkazu `az ml workspace share` CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Další informace o vlastních rolích najdete v tématu [vlastní role pro prostředky Azure](/azure/role-based-access-control/custom-roles).

Další informace o operacích (akcích) použitelných s vlastními rolemi najdete v tématu [operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Další kroky

- [Přehled podnikového zabezpečení](concept-enterprise-security.md)
- [Zabezpečené spouštění experimentů a odvozování/vystavení ve virtuální síti](how-to-enable-virtual-network.md)
- [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
- [Operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)