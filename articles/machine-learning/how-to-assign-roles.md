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
ms.openlocfilehash: 5257d9f94f6304c2a8dbea3f1648a71d0ba65e94
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064746"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Správa přístupu k pracovnímu prostoru Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

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


## <a name="frequently-asked-questions"></a>Nejčastější dotazy


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Otázka: Jaká jsou oprávnění potřebná k provádění různých akcí ve službě Azure Machine Learning?

Následující tabulka představuje souhrn Azure Machine Learningch aktivit a oprávnění, která jsou potřebná k jejich provedení v nejmenším rozsahu. Jako příklad, pokud je možné aktivitu provést s rozsahem pracovního prostoru (sloupce 4), budou automaticky fungovat i všechny vyšší obory s tímto oprávněním. Všechny cesty v této tabulce jsou **relativní cesty** k `Microsoft.MachineLearningServices/`.

| Aktivita | Rozsah na úrovni předplatného | Rozsah na úrovni skupiny prostředků | Rozsah na úrovni pracovního prostoru |
|---|---|---|---|
| Vytvořit nový pracovní prostor | Nepožadováno | Vlastník nebo přispěvatel | Není k dispozici (vlastník nebo zdědí vyšší obor role po vytvoření) |
| Vytvořit nový výpočetní cluster | Nepožadováno | Nepožadováno | Vlastník, přispěvatel nebo vlastní role povolují: `workspaces/computes/write` |
| Vytvořit nový virtuální počítač s poznámkovým blokem | Nepožadováno | Vlastník nebo přispěvatel | Není možné |
| Vytvořit novou výpočetní instanci | Nepožadováno | Nepožadováno | Vlastník, přispěvatel nebo vlastní role povolují: `workspaces/computes/write` |
| Aktivita roviny dat, jako je odeslání spuštění, přístup k datům, nasazení modelu nebo publikování kanálu | Nepožadováno | Nepožadováno | Vlastník, přispěvatel nebo vlastní role povolují: `workspaces/*/write` <br/> Všimněte si, že budete také potřebovat úložiště dat zaregistrované v pracovním prostoru, aby služba MSI mohla získat přístup k datům ve vašem účtu úložiště. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Otázka: Návody vypsat všechny vlastní role v předplatném?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Otázka: Návody v předplatném najít definici role pro roli?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz. Všimněte si, že `<role-name>` by měla být ve stejném formátu, vrácený výše uvedeným příkazem.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Otázka: Návody aktualizovat definici role?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Všimněte si, že musíte mít oprávnění k celému oboru nové definice role. Například pokud má tato nová role obor mezi třemi předplatnými, musíte mít oprávnění ke všem třem předplatným. 

> [!NOTE]
> Použití aktualizací rolí může trvat 15 minut až hodinu, než se použije u všech přiřazení rolí v daném oboru.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Otázka: Můžu definovat roli, která brání aktualizaci edice pracovního prostoru? 

Ano, můžete definovat roli, která brání aktualizaci edice pracovního prostoru. Vzhledem k tomu, že aktualizace pracovního prostoru je volání opravy v objektu pracovního prostoru, provedete to tak, že do pole `"NotActions"` ve své definici JSON zadáte následující akci: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Otázka: Jaká oprávnění jsou potřebná k provádění operací s kvótou v pracovním prostoru? 

K provedení jakékoli operace související s kvótou v pracovním prostoru potřebujete oprávnění na úrovni předplatného. To znamená, že nastavení kvóty na úrovni předplatného nebo kvóty na úrovni předplatného pro spravované výpočetní prostředky můžou nastat jenom v případě, že máte oprávnění k zápisu v oboru předplatného. 


## <a name="next-steps"></a>Další kroky

- [Přehled podnikového zabezpečení](concept-enterprise-security.md)
- [Zabezpečené spouštění experimentů a odvozování/vystavení ve virtuální síti](how-to-enable-virtual-network.md)
- [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
- [Operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
