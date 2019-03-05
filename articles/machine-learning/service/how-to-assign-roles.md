---
title: Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Další informace o správě uživatelů a rolí v pracovním prostoru služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hning86
ms.author: haining
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 623aaff3cba86e8e523a86e4adcb0a359c339c45
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336452"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning

V tomto článku se dozvíte, jak přidat uživatele do pracovního prostoru Azure Machine Learning. Také se dozvíte, jak přiřadit uživatele pro různé role a vytvořit vlastní role.

## <a name="built-in-roles"></a>Vestavěné role
Pracovní prostor služby Azure Machine Learning je prostředek Azure. Stejně jako ostatní prostředky Azure když se vytvoří nový pracovní prostor Azure Machine Learning, součástí tří výchozích rolí. Můžete přidat uživatele do pracovního prostoru a přiřadit je k jednomu z těchto předdefinovaných rolí.

- **Čtenář**
    
    Tato role umožňuje jen pro čtení akce v pracovním prostoru. Čtenáři můžou seznamu a zobrazení prostředků v pracovním prostoru, ale nelze vytvořit nebo aktualizovat tyto prostředky.

- **Přispěvatel**
    
    Tato role umožňuje uživatelům zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. Například přispěvatelé můžete vytvořit nový experiment, vytvořit nebo připojit výpočetního clusteru, odeslat spuštění a nasazení webové služby.

- **Vlastník**
    
    Tato role poskytuje uživatelům úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. Kromě toho můžete přidat nebo odebrat uživatele a změnit přiřazení rolí.

## <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů
Pokud jste vlastník pracovního prostoru, můžete přidávat a odebírat uživatele z pracovního prostoru výběrem jedné z následujících metod:
- [Uživatelské rozhraní webu Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Šablony Azure Resource Manageru](/azure/role-based-access-control/role-assignments-template)

Pokud jste nainstalovali [CLI aplikace Azure Machine Learning](reference-azure-machine-learning-cli.md), příkaz rozhraní příkazového řádku můžete také použít pro přidání uživatelů do pracovního prostoru.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Pole je e-mailovou adresu stávajícího uživatele v instanci služby Azure Active Directory, kde se nachází pracovní prostor nadřazeného předplatného. Tady je příklad toho, jak pomocí tohoto příkazu:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Vytvořit vlastní roli
Pokud předdefinované role nestačí, můžete vytvořit vlastní role. Může mít vlastní role číst, zapisovat, odstranit a výpočetních prostředků oprávnění v daném pracovním prostoru. Roli můžete zpřístupnit na úrovni konkrétní pracovní prostor, úrovni skupiny prostředků konkrétní nebo úroveň konkrétní předplatné. 

> [!NOTE]
> Musíte být vlastníkem prostředku na této úrovni můžete vytvářet vlastní role v rámci tohoto prostředku.

Chcete-li vytvořit vlastní roli, nejprve se vytvoří soubor JSON definice role, který určuje oprávnění a obor, který chcete použít pro roli. Tady je příklad souboru definice role pro vlastní roli s názvem "Mezi odborníky přes Data" oboru na úrovni konkrétní pracovní prostor:

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

Po nasazení této roli k dispozici v zadaný pracovní prostor. Nyní můžete přidat a přiřadit tato role na webu Azure Portal. Nebo můžete přidat uživatele s touto rolí pomocí `az ml workspace share` příkazu rozhraní příkazového řádku:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Další informace o vlastních rolích v Azure najdete v tématu [tento dokument](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Další postup

Postupujte podle komplexně kurzu se dozvíte, jak používat pracovní prostor vytvářet, trénovat a nasazovat modely ve službě Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelů](tutorial-train-models-with-aml.md)
