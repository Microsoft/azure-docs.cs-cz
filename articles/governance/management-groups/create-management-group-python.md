---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí Pythonu'
description: V tomto rychlém startu pomocí Pythonu vytvoříte skupinu pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101753"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Rychlý Start: Vytvoření skupiny pro správu pomocí Pythonu

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Přidat knihovnu grafu prostředků

Aby bylo možné povolit Pythonu pro správu skupin pro správu, musí být knihovna přidána. Tato knihovna funguje bez ohledu na to, kde je možné použít Python, včetně [bash ve Windows 10](/windows/wsl/install-win10) nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaný nejnovější Python (minimálně **3,8**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [Python.org](https://www.python.org/downloads/).

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.5.1**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Rozhraní příkazového řádku Azure vyžaduje, aby Python mohl používat **ověřování založené** na rozhraní CLI v následujících příkladech. Informace o dalších možnostech najdete v tématu [ověřování pomocí knihoven pro správu Azure pro Python](/azure/developer/python/azure-sdk-authenticate).

1. Ověření prostřednictvím rozhraní příkazového řádku Azure

   ```azurecli
   az login
   ```

1. V prostředí Python dle výběru nainstalujte požadované knihovny pro skupiny pro správu:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Pokud je Python nainstalovaný pro všechny uživatele, musí být tyto příkazy spuštěné z konzoly se zvýšenými oprávněními.

1. Ověřte, zda byly knihovny nainstalovány. `azure-mgmt-managementgroups` by měla být **0.2.0** nebo vyšší, `azure-mgmt-resource` měla by být **9.0.0** nebo vyšší a `azure-cli-core` měla by být **2.5.0** nebo vyšší.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Vytvoření skupiny pro správu

1. Vytvořte skript Pythonu a uložte následující zdroj jako `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Ověřování pomocí Azure CLI pomocí `az login` .

1. Do terminálu zadejte následující příkaz:

   ```bash
   py mgCreate.py
   ```

Výsledek vytvoření skupiny pro správu je výstupem do konzoly jako `LROPoller` objekt.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat nainstalované knihovny z prostředí Pythonu, můžete k tomu použít následující příkaz:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)