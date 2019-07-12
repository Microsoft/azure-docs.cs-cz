---
title: Správa rolí v pracovním prostoru Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak získat přístup k pracovnímu prostoru služby Azure Machine Learning pomocí řízení přístupu na základě role (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4a5723b2ffbd34fd7cf022bf747b4504b3bdac53
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797666"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Spravovat přístup k pracovnímu prostoru Azure Machine Learning

V tomto článku se dozvíte, jak můžete spravovat přístup k pracovnímu prostoru Azure Machine Learning. [Řízení přístupu na základě role (RBAC)](/azure/role-based-access-control/overview) slouží ke správě přístupu k prostředkům Azure. Uživatelé ve službě Azure Active Directory jsou přiřazeni konkrétní role, které poskytují přístup k prostředkům. Azure poskytuje předdefinované role a možnost vytvářet vlastní role.

## <a name="default-roles"></a>Výchozí role

Pracovní prostor služby Azure Machine Learning je prostředek Azure. Stejně jako ostatní prostředky Azure když se vytvoří nový pracovní prostor Azure Machine Learning, součástí tří výchozích rolí. Můžete přidat uživatele do pracovního prostoru a přiřadit je k jednomu z těchto předdefinovaných rolí.

| Role | Úroveň přístupu |
| --- | --- |
| **Reader** | Jen pro čtení akce v pracovním prostoru. Čtenáři můžou seznamu a zobrazení prostředků v pracovním prostoru, ale nelze vytvořit nebo aktualizovat tyto prostředky. |
| **Přispěvatel** | Zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. Například přispěvatelé můžete vytvořit nový experiment, vytvořit nebo připojit výpočetního clusteru, odeslat spuštění a nasazení webové služby. |
| **Vlastník** | Úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. Kromě toho můžete změnit přiřazení rolí. |

> [!IMPORTANT]
> Přístup role se dají vymezit na více úrovních v Azure. Například někdo s vlastníkem přístup k pracovnímu prostoru nemusí mít přístup vlastníka do skupiny prostředků, který obsahuje tento pracovní prostor. Další informace najdete v tématu [funguje jak RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Další informace o konkrétní předdefinované role, naleznete v tématu [předdefinované role pro Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Spravovat přístup k pracovnímu prostoru

Pokud jste vlastník pracovního prostoru, můžete přidat a odebrat role pro pracovní prostor. Můžete také přiřazení rolí uživatelům. Pokud chcete zjistit, jak spravovat přístup pomocí následujících odkazů:
- [Uživatelské rozhraní webu Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Šablony Azure Resource Manageru](/azure/role-based-access-control/role-assignments-template)

Pokud jste nainstalovali [CLI aplikace Azure Machine Learning](reference-azure-machine-learning-cli.md), můžete také příkaz rozhraní příkazového řádku k přiřazení rolí uživatelům.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Pole je e-mailovou adresu stávajícího uživatele v instanci služby Azure Active Directory, kde se nachází pracovní prostor nadřazeného předplatného. Tady je příklad toho, jak pomocí tohoto příkazu:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Vytvořit vlastní roli

Pokud předdefinované role nestačí, můžete vytvořit vlastní role. Může mít vlastní role číst, zapisovat, odstranit a výpočetních prostředků oprávnění v daném pracovním prostoru. Roli můžete zpřístupnit na úrovni konkrétní pracovní prostor, úrovni skupiny prostředků konkrétní nebo úroveň konkrétní předplatné.

> [!NOTE]
> Musíte být vlastníkem prostředku na této úrovni můžete vytvářet vlastní role v rámci tohoto prostředku.

Chcete-li vytvořit vlastní roli, nejprve vytvořte role definiční soubor JSON, který určuje oprávnění a obor pro roli. Následující příklad definuje vlastní roli s názvem "Mezi odborníky přes Data" oboru na úrovni konkrétní pracovní prostor:

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

Můžete změnit `AssignableScopes` pole, které chcete nastavit obor této vlastní role na úrovni předplatného, na úrovni skupiny prostředků nebo určitý pracovní prostor úroveň.

Tato vlastní role můžete provádět vše v pracovním prostoru, s výjimkou následujících akcí:

- Ji nejde vytvořit nebo aktualizovat výpočetních prostředků.
- Nelze ho odstranit výpočetních prostředků.
- Nelze přidat, odstranit nebo změnit přiřazení rolí.
- Nelze ji odstranit pracovní prostor.

Pokud chcete nasadit tuto vlastní roli, použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po nasazení této roli k dispozici v zadaný pracovní prostor. Nyní můžete přidat a přiřadit tato role na webu Azure Portal. Nebo můžete přiřadit tato role pro uživatele s použitím `az ml workspace share` příkazu rozhraní příkazového řádku:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Další informace najdete v tématu [vlastní role pro prostředky Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Další postup

- [Podnikové zabezpečení – přehled](concept-enterprise-security.md)
- [Bezpečné spuštění experimentů a odvození/skóre uvnitř virtuální sítě](how-to-enable-virtual-network.md)
- [Kurz: Trénování modelů](tutorial-train-models-with-aml.md)
