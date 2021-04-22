---
title: Vytvoření a Správa výpočetní instance
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a spravovat výpočetní instanci Azure Machine Learning. Použijte jako vývojové prostředí nebo jako cíl výpočtů pro účely vývoje a testování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861212"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Vytvoření a Správa výpočetní instance Azure Machine Learning

Naučte se, jak vytvořit a spravovat [výpočetní instanci](concept-compute-instance.md) v pracovním prostoru Azure Machine Learning.

Použijte výpočetní instanci jako vaše plně nakonfigurované a spravované vývojové prostředí v cloudu. Pro vývoj a testování můžete také použít instanci jako [školicí výpočetní cíl](concept-compute-target.md#train) nebo pro [cíl odvození](concept-compute-target.md#deploy).   Výpočetní instance může spouštět více úloh paralelně a má frontu úloh. Ve vývojovém prostředí se výpočetní instance nedá sdílet s ostatními uživateli v pracovním prostoru.

V tomto článku získáte informace o těchto tématech:

* Vytvoření výpočetní instance 
* Správa (spuštění, zastavení, restart, odstranění) výpočetní instance
* Přístup k oknu terminálu 
* Instalace balíčků R nebo Pythonu
* Vytváření nových prostředí nebo Jupyter jader

Výpočetní instance můžou úlohy spouštět bezpečně ve [virtuálním síťovém prostředí](how-to-secure-training-vnet.md), aniž by museli podniky otevírat porty SSH. Úloha se spustí v kontejnerovém prostředí a zabalí závislosti vašich modelů v kontejneru Docker. 

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Vytvořit

**Časový odhad**: přibližně 5 minut.

Vytvoření výpočetní instance je jednorázový proces pro váš pracovní prostor. Výpočetní prostředky můžete znovu použít jako pracovní stanici pro vývoj nebo jako cíl pro školení. K vašemu pracovnímu prostoru můžete připojit více výpočetních instancí.

Vyhrazená jádra na jednu oblast a kvótu pro rodinu virtuálních počítačů, která platí pro vytváření výpočetních instancí, jsou sjednocená a sdílená s Azure Machine Learning školením kvóty výpočetních clusterů. Zastavení výpočetní instance neuvolní kvótu, aby bylo zajištěno, že budete moci restartovat výpočetní instanci. Poznámka: po vytvoření není možné změnit velikost virtuálního počítače výpočetní instance.

Následující příklad ukazuje, jak vytvořit výpočetní instanci:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [ComputeInstance – třída](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Další informace najdete v tématu [AZ ml computetarget Create computeinstance](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance) reference.

# <a name="studio"></a>[Studio](#tab/azure-studio)

Pokud jste připraveni spustit jeden z vašich poznámkových bloků, vytvořte v pracovním prostoru v Azure Machine Learning Studiu novou instanci služby COMPUTE z oddílu **COMPUTE** nebo v části **poznámkové bloky** .

Informace o vytvoření výpočetní instance v studiu najdete v tématu [Vytvoření výpočetních cílů v Azure Machine Learning Studiu](how-to-create-attach-compute-studio.md#compute-instance).

---

Můžete také vytvořit výpočetní instanci pomocí [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Vytvořit jménem uživatele (Preview)

Jako správce můžete vytvořit instanci služby COMPUTE jménem odborníka na data a přiřadit k nim instanci s těmito možnostmi:
* [Šablona Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Podrobnosti o tom, jak najít TenantID a ObjectID potřebné v této šabloně, najdete v tématu [Vyhledání ID objektů identity pro konfiguraci ověřování](../healthcare-apis/fhir/find-identity-object-ids.md).  Tyto hodnoty můžete také najít na portálu Azure Active Directory.
* Rozhraní REST API

Data, na základě kterých vytvoříte výpočetní instanci pro potřeby, jsou tato oprávnění [řízení přístupu založená na rolích Azure (Azure RBAC)](../role-based-access-control/overview.md) : 
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/spustit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zastavit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/restartovat/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/applicationaccess/Action*

Vědecký pracovník dat může spustit, zastavit a restartovat výpočetní instanci. Můžou použít výpočetní instanci pro:
* Jupyter
* JupyterLab
* RStudio
* Integrované poznámkové bloky

## <a name="manage"></a>Spravovat

Spuštění, zastavení, restartování a odstranění výpočetní instance. Instance výpočetního prostředí se automaticky nezvětšuje, takže nezapomeňte prostředek zastavit, aby nedocházelo k průběžným poplatkům.

> [!TIP]
> Instance COMPUTE má disk s operačním systémem s 120 GB. Pokud vyčerpáte místo na disku, před zastavením nebo restartováním výpočetní instance [pomocí terminálu](how-to-access-terminal.md) vymažte aspoň 1-2 GB.

# <a name="python"></a>[Python](#tab/python)

V níže uvedených příkladech je název výpočetní instance **instance** .

* Získat stav

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Zastavit

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Spustit

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Restartovat

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Odstranit

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

V níže uvedených příkladech je název výpočetní instance **instance** .

* Zastavit

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Další informace najdete v tématu [AZ ml computetarget stop computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

* Spustit 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Další informace najdete v tématu [AZ ml computetarget Start computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

* Restartovat 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Další informace najdete v tématu [AZ ml computetarget restart computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Odstranit

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Další informace najdete v tématu [AZ ml computetarget Delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

V pracovním prostoru v Azure Machine Learning Studiu vyberte **COMPUTE** a pak v horní části vyberte **výpočetní instanci** .

![Správa výpočetní instance](./media/concept-compute-instance/manage-compute-instance.png)

Můžete provést následující akce:

* Vytvořit novou výpočetní instanci 
* Aktualizujte kartu COMPUTE Instances.
* Spusťte, zastavte a restartujte výpočetní instanci.  Za instanci platíte za každé, když je spuštěná. Pokud nepoužíváte výpočetní instanci, můžete ji zastavit, abyste snížili náklady. Zastavení výpočetní instance ho zruší. Pak ho znovu spusťte, až ho budete potřebovat.
* Odstraňte výpočetní instanci.
* Vyfiltrujte seznam výpočetních instancí, aby se zobrazily jenom ty, které jste vytvořili.

Pro každou výpočetní instanci v pracovním prostoru, který jste vytvořili (nebo které jste vytvořili za vás), můžete:

* Přístup k Jupyter, JupyterLab, RStudio instance COMPUTE
* SSH do výpočetní instance. Přístup SSH je ve výchozím nastavení zakázán, ale lze jej povolit v době vytváření výpočetních instancí. Přístup přes SSH je prostřednictvím mechanismu veřejného a privátního klíče. Karta vám poskytne podrobnosti o připojení SSH, jako je například IP adresa, uživatelské jméno a číslo portu.
* Získejte podrobnosti o konkrétní výpočetní instanci, jako je třeba IP adresa a oblast.

---


[Azure RBAC](../role-based-access-control/overview.md) umožňuje řídit, kteří uživatelé v pracovním prostoru můžou vytvořit, odstranit, spustit, zastavit a restartovat výpočetní instanci. Všichni uživatelé v roli přispěvatel a vlastník pracovního prostoru můžou vytvářet, odstraňovat, spouštět, zastavovat a restartovat výpočetní instance v rámci pracovního prostoru. Avšak pouze tvůrce konkrétní výpočetní instance nebo přiřazeného uživatele, pokud byl vytvořen jménem, má povolen přístup k Jupyter, JupyterLab a RStudio této výpočetní instance. Výpočetní instance je vyhrazená jednomu uživateli, který má rootový přístup, a může být terminálem přes Jupyter/JupyterLab/RStudio. Instance COMPUTE bude mít přihlášený jeden uživatel a všechny akce budou používat identitu tohoto uživatele pro Azure RBAC a připisující experimenty. Přístup přes SSH je řízený pomocí mechanismu veřejného a privátního klíče.

Tyto akce lze řídit pomocí Azure RBAC:
* *Microsoft. MachineLearningServices/pracovní prostory/výpočetní výkon/čtení*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/odstranit*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/spustit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zastavit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/restartovat/akce*

## <a name="next-steps"></a>Další kroky

* [Přístup k terminálu výpočetních instancí](how-to-access-terminal.md)
* [Vytváření a Správa souborů](how-to-manage-files.md)
* [Odeslat školicí běh](how-to-set-up-training-targets.md)
