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
ms.openlocfilehash: f6c74da2e9189c5dddb88cb80f04422f49cfaee2
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594249"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning

V tomto článku se dozvíte, jak přidat uživatele do pracovního prostoru Azure Machine Learning, a přiřadit jim s různými rolemi. Také se dozvíte, jak vytvářet vlastní role.

## <a name="built-in-roles"></a>Vestavěné role
Pracovní prostor Azure Machine Learning je prostředek Azure. A stejně jako prostředek Azure při vytvoření nového pracovního prostoru Azure Machine Learning obsahuje tři výchozí role. Můžete přidat uživatele do pracovního prostoru a přiřadit je k jednomu z těchto předdefinovaných rolí.

- **Čtenář**
    
    Tato role umožňuje jen pro čtení akce v pracovním prostoru. K této roli můžete vytvořit seznam a zobrazit prostředky v pracovním prostoru, jako je například experimenty, spuštění, výpočetních, modely, webové služby, publikovat kanály a tak dále. Ale nemáte oprávnění k vytvoření nebo aktualizaci těchto prostředků.

- **Přispěvatel**
    
    Tato role umožňuje uživatelům zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. S touto rolí můžete vytvořit nový experiment, vytvořit nebo připojit výpočetního clusteru, například odeslat spuštění, zaregistrujte model a nasazení webové služby.

- **Vlastník**
    
    Tato role poskytuje úplný přístup k pracovnímu prostoru, abyste mohli zobrazit, vytvořit, upravit nebo odstranit, (kde je to možné) prostředky v pracovním prostoru. Kromě toho můžete také přidat nebo odebrat uživatele a změnit jejich přiřazení rolí.

## <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů
Pokud jste vlastníkem daného pracovního prostoru, můžete přidat uživatele do nebo odebrat uživatele z pracovního prostoru výběrem jedné z následujících metod k provedení této akce:
- [Uživatelské rozhraní webu Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Šablony Azure Resource Management](/azure/role-based-access-control/role-assignments-template).

Případně pokud jste nainstalovali [CLI aplikace Azure Machine Learning](reference-azure-machine-learning-cli.md), můžete také vhodné příkazu rozhraní příkazového řádku pro přidání uživatele do pracovního prostoru.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Pamatujte, že `user` pole je e-mailovou adresu stávajícího uživatele ve stejné Azure Active Directory ve kterém se nachází nadřazené předplatné pracovního prostoru. Níže je příklad toho, jak pomocí tohoto příkazu:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Vytvořit vlastní roli
Pokud předdefinované role nejsou dostatečná k vašim potřebám, můžete také vytvořit vlastní role. Může mít vlastní role číst, zapsat nebo odstranit oprávnění v pracovním prostoru a výpočetních prostředků v daném pracovním prostoru. A můžete zpřístupnit role na úrovni konkrétní pracovní prostor, úrovni skupiny prostředků konkrétní nebo úroveň konkrétní předplatné. 

> [!NOTE]
> Chcete-li vytvořit vlastní role v rámci tohoto prostředku, musí být vlastníkem prostředku na této úrovni.

Chcete-li vytvořit vlastní roli, nejprve se vytvoří soubor JSON definice role oprávnění a obor, který chcete použít pro roli. Níže je soubor definice role pro vlastní roli s názvem "Mezi odborníky přes Data" oboru na úrovni konkrétní pracovní prostor.

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

Tato role umožňuje dělat všechno, co je v pracovním prostoru, s výjimkou následujících akcí:
- Ji nejde vytvořit nebo aktualizovat výpočetních prostředků.
- Nelze ho odstranit výpočetních prostředků.
- Nelze přidat, odstranit uživatele nebo změnit přiřazení rolí uživatele.
- Nelze ji odstranit pracovní prostor.

Pokud chcete nasadit tuto vlastní roli, použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po nasazení této roli k dispozici v zadaný pracovní prostor. Nyní můžete přidat uživatele a tuto roli přiřadit k nim na webu Azure Portal. Nebo můžete přidat uživatele s použitím této role `az ml workspace share` příkazu rozhraní příkazového řádku:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Můžete také změnit `AssignableScopes` pole, které chcete nastavit obor této vlastní role na úrovni předplatného na úrovni skupiny prostředků, nebo na úrovni konkrétní pracovní prostor (jak je znázorněno).

Další informace o tom, jak vlastní role fungovat v Azure, najdete [tento dokument](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Další postup

Postupujte podle komplexně kurzu se dozvíte, jak používat pracovní prostor pro vytváření, trénování a nasazujte modely pomocí služby Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelů](tutorial-train-models-with-aml.md)
