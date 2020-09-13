---
title: Správa rolí v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat přístup k pracovnímu prostoru Azure Machine Learning pomocí řízení přístupu na základě role (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: 235135cbbcc7c622f4dd23c2e4f29cc3636dc1ea
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661921"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Správa přístupu k pracovnímu prostoru služby Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spravovat přístup k pracovnímu prostoru Azure Machine Learning. [Řízení přístupu na základě role Azure (Azure RBAC)](/azure/role-based-access-control/overview) se používá ke správě přístupu k prostředkům Azure. Uživatelům v Azure Active Directory jsou přiřazeny konkrétní role, které udělují přístup k prostředkům. Azure poskytuje jak předdefinované role, tak i možnost vytvářet vlastní role.

## <a name="default-roles"></a>Výchozí role

Pracovní prostor služby Azure Machine Learning je prostředek Azure. Podobně jako u jiných prostředků Azure má nově vytvořený pracovní prostor služby Azure Machine Learning tři výchozí role. Do pracovního prostoru můžete přidat uživatele a přiřadit jim jednu z těchto integrovaných rolí.

| Role | Úroveň přístupu |
| --- | --- |
| **Čtenář** | Akce jen pro čtení v pracovním prostoru. Čtenáři můžou v pracovním prostoru vypisovat a zobrazovat prostředky, včetně přihlašovacích údajů [úložiště dat](how-to-access-data.md) . Čtenáři tyto prostředky nemůžou vytvářet ani aktualizovat. |
| **Přispěvatel** | Zobrazení, vytvoření, úprava nebo odstranění prostředků (kde je k dispozici) v pracovním prostoru. Přispěvatelé můžou například vytvořit experiment, vytvořit nebo připojit výpočetní cluster, odeslat běh a nasadit webovou službu. |
| **Vlastník** | Úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit (kde se vztahují) prostředky v pracovním prostoru. Kromě toho můžete měnit přiřazení rolí. |
| **Vlastní role** | Umožňuje přizpůsobit přístup k určitým operacím ovládacího prvku nebo datové roviny v pracovním prostoru. Například odeslání spuštění, vytvoření COMPUTE, nasazení modelu nebo registrace datové sady. |

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

Pokud jste nainstalovali [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), můžete k přiřazení rolí uživatelům použít příkazy rozhraní příkazového řádku:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Toto `user` pole je e-mailová adresa stávajícího uživatele v instanci Azure Active Directory, kde nadřazený odběr pracovního prostoru je život. Tady je příklad, jak použít tento příkaz:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> příkaz AZ ml sdílení pracovního prostoru nefunguje pro federovaný účet Azure Active Directory B2B. Místo příkazu použijte prosím portál uživatelského rozhraní Azure.


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning operace

Azure Machine Learning integrovaných akcí pro mnoho operací a úloh. Úplný seznam najdete v tématu [operace poskytovatele prostředků Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Vytvoření vlastní role

Pokud si s předdefinovanými rolemi nevystačíte, můžete vytvářet role vlastní. Vlastní role můžou mít v tomto pracovním prostoru oprávnění ke čtení, zápisu, odstranění a výpočtu prostředků. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného.

> [!NOTE]
> Abyste mohli vytvářet vlastní role v rámci tohoto prostředku, musíte být vlastníkem prostředku na této úrovni.

Chcete-li vytvořit vlastní roli, nejprve Sestavte soubor JSON definice role, který určuje oprávnění a rozsah této role. Následující příklad definuje vlastní roli s názvem "data odborníka vlastní" vymezenou v konkrétní úrovni pracovního prostoru:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Pole můžete změnit, pokud `AssignableScopes` chcete nastavit rozsah této vlastní role na úrovni předplatného, na úrovni skupiny prostředků nebo na konkrétní úrovni pracovního prostoru.
> Výše uvedená vlastní role je jenom příklad, kde najdete některé navrhované [vlastní role pro službu Azure Machine Learning](#customroles).

Tato vlastní role může dělat všechno v pracovním prostoru s výjimkou následujících akcí:

- Nedokáže vytvořit ani aktualizovat výpočetní prostředek.
- Nejde odstranit výpočetní prostředek.
- Nemůže přidávat, odstraňovat ani měnit přiřazení rolí.
- Pracovní prostor nelze odstranit.

K nasazení této vlastní role použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po nasazení bude tato role k dispozici v zadaném pracovním prostoru. Nyní můžete tuto roli přidat a přiřadit v Azure Portal. Nebo můžete tuto roli přiřadit uživateli pomocí `az ml workspace share` příkazu CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Další informace o vlastních rolích najdete v tématu [vlastní role Azure](/azure/role-based-access-control/custom-roles). Další informace o operacích (akce a ne akce) použitelné s vlastními rolemi najdete v tématu [operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>Otázka: Jaká jsou oprávnění potřebná k provádění některých běžných scénářů ve službě Azure Machine Learning?

Následující tabulka představuje souhrn Azure Machine Learningch aktivit a oprávnění, která jsou potřebná k jejich provedení v nejmenším rozsahu. Pokud například aktivitu lze provést s oborem pracovního prostoru (sloupcem 4), pak všechny vyšší obory s tímto oprávněním budou také fungovat automaticky:

> [!IMPORTANT]
> Všechny cesty v této tabulce, které začínají, `/` jsou **relativní cesty** k `Microsoft.MachineLearningServices/` :

| Aktivita | Rozsah na úrovni předplatného | Rozsah na úrovni skupiny prostředků | Rozsah na úrovni pracovního prostoru |
| ----- | ----- | ----- | ----- |
| Vytvořit nový pracovní prostor | Nevyžadováno | Vlastník nebo přispěvatel | Není k dispozici (vlastník nebo zdědí vyšší obor role po vytvoření) |
| Aktualizace edice pracovního prostoru | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/write` |
| Požádat o kvótu Amlcompute na úrovni předplatného nebo nastavit kvótu úrovně pracovního prostoru | Vlastník, přispěvatel nebo vlastní role </br>která `/locations/updateQuotas/action`</br> v oboru předplatného | Neautorizováno | Neautorizováno |
| Vytvořit nový výpočetní cluster | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |
| Vytvořit novou výpočetní instanci | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |
| Odeslání libovolného typu běhu | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publikování koncového bodu kanálu | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Nasazení registrovaného modelu do prostředku AKS/ACI | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Bodování před nasazeným koncovým bodem AKS | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role umožňující: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (Pokud nepoužíváte Azure Active Directory ověřování) nebo `"/workspaces/read"` (Pokud používáte ověřování tokenů) |
| Přístup k úložišti pomocí interaktivních poznámkových bloků | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| Vytvořit novou vlastní roli | Povolení vlastníka, přispěvatele nebo vlastní role `Microsoft.Authorization/roleDefinitions/write` | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |

> [!TIP]
> Pokud při prvním pokusu o vytvoření pracovního prostoru dojde k chybě, ujistěte se, že vaše role povoluje `Microsoft.MachineLearningServices/register/action` . Tato akce vám umožní zaregistrovat poskytovatele prostředků Azure Machine Learning s vaším předplatným Azure.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>Otázka: Publikují se pro službu Machine Learning předdefinované role Azure?

V současnosti nepublikujeme [předdefinované role Azure](/azure/role-based-access-control/built-in-roles) pro službu Machine Learning. Integrovanou roli se po publikování nedá aktualizovat a pořád ještě spravujeme definice rolí na základě zákaznických scénářů a zpětné vazby. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>Otázka: Existují některé šablony vlastní role pro nejběžnější scénáře ve službě Machine Learning?

Tady je několik běžných scénářů s vlastními navrhovanými definicemi rolí, které můžete použít jako základ k definování vlastních rolí:

* Odborník na __data – vlastní__: umožňuje, aby datový vědecký pracovník prováděl všechny operace v pracovním prostoru **s výjimkou**:

    * Vytváření výpočetních prostředků
    * Nasazení modelů do provozního clusteru AKS
    * Nasazení koncového bodu kanálu v produkčním prostředí

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Vlastní omezení pro datový vědecký pracovník__: v povolených akcích není k většímu omezení definice role bez zástupných znaků. Může provádět všechny operace v pracovním prostoru **s výjimkou**:

    * Vytváření výpočetních prostředků
    * Nasazení modelů do provozního clusteru AKS
    * Nasazení koncového bodu kanálu v produkčním prostředí

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps vlastní__: slouží k přiřazení role k instančnímu objektu a k automatizaci kanálů MLOps. Například pro odeslání spuštění proti již publikovanému kanálu:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Správce pracovního prostoru__: umožňuje provádět všechny operace v rámci oboru pracovního prostoru, **s výjimkou**:

    * Vytváří se nový pracovní prostor.
    * Přiřazení kvót na úrovni předplatného nebo pracovního prostoru
    * Upgrade edice pracovního prostoru

    Správce pracovního prostoru také nemůže vytvořit novou roli. V rámci rozsahu jejich pracovního prostoru může přiřadit jenom existující předdefinované nebo vlastní role:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Customer Label__: umožňuje definovat obor role pouze k označení dat popisků:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Otázka: Návody vypsat všechny vlastní role v předplatném?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>Otázka: Návody najít operace podporované službou Machine Learning?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Můžou se taky najít v seznamu [operací poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>Otázka: Jaké jsou některé běžné možná úskalí při použití Azure RBAC?

Tady je několik věcí, na kterých je potřeba vědět, když používáte řízení přístupu na základě role Azure (Azure RBAC):

- Když vytvoříte prostředek v Azure, řekněme, že nejste přímo vlastníkem tohoto pracovního prostoru. Vaše role se zdědí z nejvyšší role oboru, ke které jste v tomto předplatném udělili autorizaci. Příklad: Pokud jste správce sítě a máte oprávnění k vytvoření pracovního prostoru Machine Learning, bude vám přiřazena role správce sítě v daném pracovním prostoru, nikoli role vlastníka.
- Pokud existují dvě přiřazení rolí stejného Azure Active Directoryho uživatele s konfliktními oddíly akcí/NotActions, operace uvedené v NotActions z jedné role se nemusí projevit, pokud jsou také uvedeny jako akce v jiné roli. Další informace o tom, jak Azure analyzuje přiřazení rolí, najdete v článku [jak Azure RBAC určuje, jestli má uživatel přístup k prostředku](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) .
- K nasazení výpočetních prostředků v rámci virtuální sítě musíte explicitně mít oprávnění k těmto akcím:
    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.
    
    Další informace o RBAC pomocí sítě najdete v tématu [předdefinované role sítě](/azure/role-based-access-control/built-in-roles#networking).

- Může někdy trvat až 1 hodinu, než se vaše nové přiřazení role projeví u oprávnění uložených v mezipaměti napříč zásobníkem.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>Otázka: Jaká oprávnění potřebuji k použití spravované identity přiřazené uživatelem v mých clusterech Amlcompute?

K přiřazení identity přiřazené uživateli v clusterech Amlcompute musí mít jeden oprávnění k zápisu, aby bylo možné vytvořit výpočetní prostředky a mít [roli spravovaného operátora identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Pokud chcete získat další informace o RBAC se spravovanými identitami, přečtěte si téma [Správa identity přiřazené uživatelem](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) .


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>Otázka: Podporujeme na portálu Studio řízení přístupu na základě rolí?

Azure Machine Learning Studio podporuje řízení přístupu na základě role Azure (Azure RBAC). 

> [!IMPORTANT]
> Po přiřazení vlastní role s konkrétními oprávněními pro odborníky na data v pracovním prostoru jsou odpovídající akce (například přidání výpočetního tlačítka) automaticky skryty od uživatelů. Skrytím těchto položek zabráníte jakýmkoli nejasnostem v zobrazení ovládacích prvků, které při použití vrátí oznámení neoprávněného přístupu ze služby.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Otázka: Návody v předplatném najít definici role pro roli?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz. `<role-name>`Měl by být ve stejném formátu, vrácený výše uvedeným příkazem.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Otázka: Návody aktualizovat definici role?

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Musíte mít oprávnění k celému oboru nové definice role. Například pokud má tato nová role obor mezi třemi předplatnými, musíte mít oprávnění ke všem třem předplatným. 

> [!NOTE]
> Použití aktualizací rolí může trvat 15 minut až hodinu, než se použije u všech přiřazení rolí v daném oboru.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Otázka: Můžu definovat roli, která brání aktualizaci edice pracovního prostoru? 

Ano, můžete definovat roli, která brání aktualizaci edice pracovního prostoru. Vzhledem k tomu, že aktualizace pracovního prostoru je volání opravy v objektu pracovního prostoru, provedete to tak, že do `"NotActions"` pole ve své definici JSON zadáte následující akci: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Otázka: Jaká oprávnění jsou potřebná k provádění operací s kvótou v pracovním prostoru? 

K provedení jakékoli operace související s kvótou v pracovním prostoru potřebujete oprávnění na úrovni předplatného. To znamená, že nastavení kvóty na úrovni předplatného nebo kvóty na úrovni předplatného pro spravované výpočetní prostředky můžou nastat jenom v případě, že máte oprávnění k zápisu v oboru předplatného. 


## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení podniku](concept-enterprise-security.md)
- [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)
- [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
- [Operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
