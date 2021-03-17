---
title: Správa rolí v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat přístup k pracovnímu prostoru Azure Machine Learning pomocí řízení přístupu na základě role Azure (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 01/20/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 8420aecbc160fa6df2640d2ba0ae8a8b77702b67
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624536"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Správa přístupu k pracovnímu prostoru služby Azure Machine Learning

V tomto článku se dozvíte, jak spravovat přístup (autorizaci) k pracovnímu prostoru Azure Machine Learning. [Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) se používá ke správě přístupu k prostředkům Azure, jako je třeba možnost vytvářet nové prostředky nebo používat stávající. Uživatelům v Azure Active Directory (Azure AD) se přiřazují konkrétní role, které udělují přístup k prostředkům. Azure poskytuje jak předdefinované role, tak i možnost vytvářet vlastní role.

> [!TIP]
> I když se tento článek zaměřuje na Azure Machine Learning, jednotlivé služby, na kterých se Azure ML spoléhá, poskytují vlastní nastavení RBAC. Pomocí informací v tomto článku můžete například nakonfigurovat, kdo může odesílat žádosti o vyhodnocování do modelu nasazeného jako webové služby ve službě Azure Kubernetes. Služba Azure Kubernetes ale poskytuje svou vlastní sadu rolí Azure. Informace o RBAC pro konkrétní služby, které mohou být užitečné při Azure Machine Learning, najdete na následujících odkazech:
>
> * [Řízení přístupu k prostředkům clusteru Azure Kubernetes](../aks/azure-ad-rbac.md)
> * [Použití Azure RBAC pro autorizaci Kubernetes](../aks/manage-azure-rbac.md)
> * [Použití služby Azure RBAC pro přístup k datům objektu BLOB](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Použití některých rolí může omezit funkce uživatelského rozhraní v Azure Machine Learning Studiu pro jiné uživatele. Pokud například role uživatele nemá možnost vytvořit výpočetní instanci, možnost vytvoření výpočetní instance nebude v nástroji Studio k dispozici. Toto chování je očekávané a brání uživateli v pokusech o operace, které by vrátily chybu odepření přístupu.

## <a name="default-roles"></a>Výchozí role

Pracovní prostor služby Azure Machine Learning je prostředek Azure. Podobně jako u jiných prostředků Azure má nově vytvořený pracovní prostor služby Azure Machine Learning tři výchozí role. Do pracovního prostoru můžete přidat uživatele a přiřadit jim jednu z těchto integrovaných rolí.

| Role | Úroveň přístupu |
| --- | --- |
| **Čtenář** | Akce jen pro čtení v pracovním prostoru. Čtenáři můžou v pracovním prostoru vypisovat a zobrazovat prostředky, včetně přihlašovacích údajů [úložiště dat](how-to-access-data.md) . Čtenáři tyto prostředky nemůžou vytvářet ani aktualizovat. |
| **Přispěvatel** | Zobrazení, vytvoření, úprava nebo odstranění prostředků (kde je k dispozici) v pracovním prostoru. Přispěvatelé můžou například vytvořit experiment, vytvořit nebo připojit výpočetní cluster, odeslat běh a nasadit webovou službu. |
| **Vlastník** | Úplný přístup k pracovnímu prostoru, včetně možnosti zobrazit, vytvořit, upravit nebo odstranit (kde se vztahují) prostředky v pracovním prostoru. Kromě toho můžete měnit přiřazení rolí. |
| **Vlastní role** | Umožňuje přizpůsobit přístup k určitým operacím ovládacího prvku nebo datové roviny v pracovním prostoru. Například odeslání spuštění, vytvoření COMPUTE, nasazení modelu nebo registrace datové sady. |

> [!IMPORTANT]
> Přístup k rolím může být v Azure omezený na více úrovní. Například někdo s přístupem vlastníka k pracovnímu prostoru nemusí mít oprávnění vlastníka ke skupině prostředků, která obsahuje pracovní prostor. Další informace najdete v tématu [Jak funguje Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-works).

V současné době nejsou k dispozici žádné další předdefinované role, které jsou specifické pro Azure Machine Learning. Další informace o předdefinovaných rolích najdete v tématu [předdefinované role Azure](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Správa přístupu k pracovnímu prostoru

Pokud jste vlastníkem pracovního prostoru, můžete přidat a odebrat role pro pracovní prostor. Role můžete přiřadit také uživatelům. Pomocí následujících odkazů zjistíte, jak spravovat přístup:
- [Azure Portal UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Šablony Azure Resource Manageru](../role-based-access-control/role-assignments-template.md)

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

Další informace o vlastních rolích najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning operace

Další informace o operacích (akce a ne akce) použitelné s vlastními rolemi najdete v tématu [operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). K vypsání operací můžete použít taky následující příkaz Azure CLI:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Výpis vlastních rolí

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Pokud chcete zobrazit definici role pro konkrétní vlastní roli, použijte následující příkaz rozhraní příkazového řádku Azure CLI. `<role-name>`Měl by být ve stejném formátu, vrácený výše uvedeným příkazem:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

V rozhraní příkazového řádku Azure CLI spusťte následující příkaz:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Musíte mít oprávnění k celému oboru nové definice role. Například pokud má tato nová role obor mezi třemi předplatnými, musíte mít oprávnění ke všem třem předplatným. 

> [!NOTE]
> Použití aktualizací rolí může trvat 15 minut až hodinu, než se použije u všech přiřazení rolí v daném oboru.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Použití šablon Azure Resource Manager pro opakování

Pokud předpokládáte, že budete muset znovu vytvořit složitá přiřazení rolí, může být šablona Azure Resource Manager velká. [Šablona přiřazení role 201 – Machine-Learning-závislosti (role](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) ) ukazuje, jak lze zadat přiřazení rolí ve zdrojovém kódu pro opakované použití. 

## <a name="common-scenarios"></a>Obvyklé scénáře

Následující tabulka představuje souhrn Azure Machine Learningch aktivit a oprávnění, která jsou potřebná k jejich provedení v nejmenším rozsahu. Pokud například aktivitu lze provést s oborem pracovního prostoru (sloupcem 4), pak všechny vyšší obory s tímto oprávněním budou také fungovat automaticky:

> [!IMPORTANT]
> Všechny cesty v této tabulce, které začínají, `/` jsou **relativní cesty** k `Microsoft.MachineLearningServices/` :

| Aktivita | Rozsah na úrovni předplatného | Rozsah na úrovni skupiny prostředků | Rozsah na úrovni pracovního prostoru |
| ----- | ----- | ----- | ----- |
| Vytvořit nový pracovní prostor | Nevyžadováno | Vlastník nebo přispěvatel | Není k dispozici (vlastník nebo zdědí vyšší obor role po vytvoření) |
| Požádat o kvótu Amlcompute na úrovni předplatného nebo nastavit kvótu úrovně pracovního prostoru | Vlastník, přispěvatel nebo vlastní role </br>která `/locations/updateQuotas/action`</br> v oboru předplatného | Neautorizováno | Neautorizováno |
| Vytvořit nový výpočetní cluster | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |
| Vytvořit novou výpočetní instanci | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |
| Odeslání libovolného typu běhu | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publikování kanálů a koncových bodů | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Nasazení registrovaného modelu do prostředku AKS/ACI | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Bodování před nasazeným koncovým bodem AKS | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role umožňující: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (Pokud nepoužíváte Azure Active Directory ověřování) nebo `"/workspaces/read"` (Pokud používáte ověřování tokenů) |
| Přístup k úložišti pomocí interaktivních poznámkových bloků | Nevyžadováno | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Vytvořit novou vlastní roli | Povolení vlastníka, přispěvatele nebo vlastní role `Microsoft.Authorization/roleDefinitions/write` | Nevyžadováno | Vlastník, přispěvatel nebo vlastní role povolují: `/workspaces/computes/write` |

> [!TIP]
> Pokud při prvním pokusu o vytvoření pracovního prostoru dojde k chybě, ujistěte se, že vaše role povoluje `Microsoft.MachineLearningServices/register/action` . Tato akce vám umožní zaregistrovat poskytovatele prostředků Azure Machine Learning s vaším předplatným Azure.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Spravovaná identita přiřazená uživatelem s výpočetním clusterem Azure ML

Pokud chcete přiřadit identitu přiřazenou uživateli k Azure Machine Learning výpočetnímu clusteru, potřebujete oprávnění k zápisu pro vytvoření úlohy COMPUTE a [spravovaného operátoru identity](../role-based-access-control/built-in-roles.md#managed-identity-operator). Další informace o službě Azure RBAC se spravovanými identitami najdete v článku [Správa identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .

### <a name="mlflow-operations"></a>Operace MLflow

Pokud chcete provádět operace MLflow s vaším pracovním prostorem Azure Machine Learning, použijte následující obory, které vlastní role:

| Operace MLflow | Obor |
| --- | --- |
| Vypíše všechny experimenty v úložišti sledování pracovních prostorů, získá experiment podle ID, získá experiment podle názvu. | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Vytvoření experimentu s názvem, nastavení značky na experimentu, obnovení experimentu označeného k odstranění| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Odstranění experimentu | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Získat data a metadata o spuštění a související data a získat seznam všech hodnot pro zadanou metriku pro daný běh, zobrazit artefakty pro spuštění | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Vytvoření nového spuštění v rámci experimentu, odstranění spuštění, obnovení odstraněných spuštění, metriky protokolu pod aktuálním spuštěním, nastavení značek při spuštění, odstranění značek při spuštění, parametrů protokolu (dvojice klíč-hodnota) použité pro spuštění, zaznamenání dávky metrik, paraí a značek pro spuštění, stav spuštění aktualizace | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Získat registrovaný model podle názvu, načíst seznam všech registrovaných modelů v registru, vyhledat registrované modely, nejnovější modely verzí pro jednotlivé fáze požadavků, získat verzi registrovaného modelu, vyhledat verze modelu, získat identifikátor URI, kde jsou uložené artefakty verze modelu, vyhledejte běhy pomocí ID experimentu. | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Vytvoří nový registrovaný model, aktualizuje název/popis registrovaného modelu, přejmenuje existující registrovaný model, vytvoří novou verzi modelu, aktualizuje Popis verze modelu a převede registrovaný model na jednu z fází. | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Odstraňte registrovaný model společně se všemi jeho verzemi, odstraňte konkrétní verze registrovaného modelu. | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Příklady vlastních rolí

### <a name="data-scientist"></a>Datový vědec

Umožňuje, aby datový vědecký pracovník prováděl všechny operace v pracovním prostoru **s výjimkou**:

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

### <a name="data-scientist-restricted"></a>Datový vědecký pracovník omezený

Přísnější definice role bez zástupných znaků v povolených akcích. Může provádět všechny operace v pracovním prostoru **s výjimkou**:

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
     
### <a name="mlflow-data-scientist"></a>MLflow data – vědecký pracovník

Umožňuje, aby datový vědecký pracovník prováděl všechny operace podporované MLflow AzureML **s výjimkou**:

* Vytváření výpočetních prostředků
* Nasazení modelů do provozního clusteru AKS
* Nasazení koncového bodu kanálu v produkčním prostředí

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
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

### <a name="mlops"></a>MLOps

Slouží k přiřazení role k instančnímu objektu a k automatizaci MLOps kanálů. Například pro odeslání spuštění proti již publikovanému kanálu:

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

### <a name="workspace-admin"></a>Správce pracovního prostoru

Umožňuje provádět všechny operace v rámci oboru pracovního prostoru, **s výjimkou**:

* Vytváří se nový pracovní prostor.
* Přiřazení kvót na úrovni předplatného nebo pracovního prostoru

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
### <a name="data-labeler"></a>Popisek dat

Slouží k definování rozsahu role pouze k označení dat popisků:

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

## <a name="troubleshooting"></a>Řešení potíží

Tady je několik věcí, na kterých je potřeba vědět, když používáte řízení přístupu na základě role Azure (Azure RBAC):

- Při vytváření prostředku v Azure, jako je například pracovní prostor, nejste přímo vlastníkem prostředku. Role je děděna z nejvyšší role oboru, ke které jste v tomto předplatném udělili autorizaci. Příklad: Pokud jste správce sítě a máte oprávnění k vytvoření pracovního prostoru Machine Learning, bude vám přiřazena role správce sítě v daném pracovním prostoru, nikoli role vlastníka.

- K provádění operací s kvótami v pracovním prostoru potřebujete oprávnění na úrovni předplatného. To znamená, že nastavení kvóty na úrovni předplatného nebo kvóty na úrovni předplatného pro spravované výpočetní prostředky můžou nastat jenom v případě, že máte oprávnění k zápisu v oboru předplatného.

- Pokud existují dvě přiřazení rolí stejného Azure Active Directoryho uživatele s konfliktními oddíly akcí/NotActions, operace uvedené v NotActions z jedné role se nemusí projevit, pokud jsou také uvedeny jako akce v jiné roli. Další informace o tom, jak Azure analyzuje přiřazení rolí, najdete v článku [jak Azure RBAC určuje, jestli má uživatel přístup k prostředku](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) .

- K nasazení výpočetních prostředků v rámci virtuální sítě musíte explicitně mít oprávnění k těmto akcím:
    - `Microsoft.Network/virtualNetworks/join/action` na prostředku virtuální sítě.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` v prostředku podsítě.
    
    Další informace o RBAC v Azure s využitím sítě najdete v tématu [předdefinované role sítě](../role-based-access-control/built-in-roles.md#networking).

- V některých případech může trvat až 1 hodinu, než se vaše nové přiřazení role projeví u oprávnění uložených v mezipaměti napříč zásobníkem.

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení podniku](concept-enterprise-security.md)
- [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)
- [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
- [Operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
