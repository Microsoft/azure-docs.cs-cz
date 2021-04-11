---
title: Konfigurace privátního koncového bodu
titleSuffix: Azure Machine Learning
description: Pomocí privátního odkazu Azure získáte zabezpečený přístup k pracovnímu prostoru Azure Machine Learning z virtuální sítě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 6fd497e0bc0fd282d57779c483f1e39e8f5ab60a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505473"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurace privátního odkazu Azure pro pracovní prostor Azure Machine Learning

V tomto dokumentu se dozvíte, jak pomocí privátního propojení Azure s vaším pracovním prostorem Azure Machine Learning. Informace o vytvoření virtuální sítě pro Azure Machine Learning najdete v tématu [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md) .

Privátní odkaz Azure umožňuje připojit se k pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](../private-link/private-link-overview.md) .

> [!IMPORTANT]
> Privátní propojení Azure neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální. Operace roviny dat, jako je například použití Azure Machine Learning studia, rozhraní API (včetně publikovaných kanálů) nebo sady SDK, používají privátní koncový bod.
>
> Pokud používáte Mozilla Firefox, může dojít k potížím při pokusu o přístup k privátnímu koncovému bodu pro váš pracovní prostor. Tento problém může souviset s DNS přes HTTPS v Mozilla. Jako alternativní řešení doporučujeme používat Microsoft Edge Google Chrome.

## <a name="prerequisites"></a>Požadavky

* Pokud plánujete použít pracovní prostor s povoleným privátním propojením s klíčem spravovaným zákazníkem, musíte požádat o tuto funkci pomocí lístku podpory. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

* Pro vytvoření privátního koncového bodu v nástroji musíte mít existující virtuální síť. Před přidáním privátního koncového bodu musíte taky [zakázat zásady sítě pro privátní koncové body](../private-link/disable-private-endpoint-network-policy.md) .
## <a name="limitations"></a>Omezení

* Použití Azure Machine Learningho pracovního prostoru s privátním odkazem není v oblastech Azure Government k dispozici.
* Pokud povolíte veřejný přístup k pracovnímu prostoru zabezpečenému pomocí privátního propojení a použijete Azure Machine Learning studia přes veřejný Internet, některé funkce, jako je například Návrhář, nemusí mít přístup k vašim datům. K tomuto problému dochází, když jsou data uložená ve službě, která je zabezpečená za virtuální sítí. Například účet Azure Storage.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

K vytvoření pracovního prostoru s privátním koncovým bodem použijte jednu z následujících metod. Každá z těchto metod __vyžaduje existující virtuální síť__:

> [!TIP]
> Pokud chcete současně vytvořit pracovní prostor, privátní koncový bod a virtuální síť, přečtěte si téma [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Sada Azure Machine Learning Python SDK poskytuje třídu [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , která se dá použít s [pracovním prostorem. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) k vytvoření pracovního prostoru s privátním koncovým bodem. Tato třída vyžaduje existující virtuální síť.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Rozšíření Azure CLI pro Machine Learning](reference-azure-machine-learning-cli.md) poskytuje příkaz [AZ ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_create) . Následující parametry tohoto příkazu lze použít k vytvoření pracovního prostoru s privátní sítí, ale vyžaduje existující virtuální síť:

* `--pe-name`: Název privátního koncového bodu, který je vytvořen.
* `--pe-auto-approval`: Zda mají být připojení privátního koncového bodu k pracovnímu prostoru automaticky schválena.
* `--pe-resource-group`: Skupina prostředků, ve které se vytvoří privátní koncový bod. Musí se jednat o stejnou skupinu, která obsahuje virtuální síť.
* `--pe-vnet-name`: Existující virtuální síť pro vytvoření privátního koncového bodu v nástroji.
* `--pe-subnet-name`: Název podsítě, v níž se má vytvořit privátní koncový bod. Výchozí hodnota je `default`.

Tyto parametry jsou kromě dalších požadovaných parametrů pro příkaz Create. Například následující příkaz vytvoří nový pracovní prostor v oblasti Západní USA s použitím existující skupiny prostředků a virtuální sítě:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Karta __sítě__ v nástroji Azure Machine Learning Studio umožňuje konfigurovat soukromý koncový bod. Vyžaduje ale existující virtuální síť. Další informace najdete v tématu [vytvoření pracovních prostorů na portálu](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Přidání privátního koncového bodu do pracovního prostoru

K přidání privátního koncového bodu do existujícího pracovního prostoru použijte jednu z následujících metod:

> [!WARNING]
>
> Pokud máte k tomuto pracovnímu prostoru přidružené nějaké výpočetní cíle a nejsou za stejnou virtuální sítí, v rámci které se vytvoří privátní koncový bod, nebudou fungovat.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Další informace o třídách a metodách, které se používají v tomto příkladu, naleznete v tématu [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) a [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Rozšíření Azure CLI pro Machine Learning](reference-azure-machine-learning-cli.md) poskytuje příkaz [AZ ml v rámci privátního koncového bodu v pracovním prostoru přidat](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) .

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z pracovního prostoru Azure Machine Learning na portálu vyberte __připojení privátních koncových bodů__ a pak vyberte __+ privátní koncový bod__. Použijte pole k vytvoření nového privátního koncového bodu.

* Při výběru __oblasti__ vyberte stejnou oblast jako vaše virtuální síť. 
* Při výběru __typu prostředku__ použijte __Microsoft. MachineLearningServices/Workspaces__. 
* Nastavte __prostředek__ na název vašeho pracovního prostoru.

Nakonec vyberte __vytvořit__ k vytvoření privátního koncového bodu.

---

## <a name="remove-a-private-endpoint"></a>Odebrání privátního koncového bodu

K odebrání privátního koncového bodu z pracovního prostoru použijte jednu z následujících metod:

# <a name="python"></a>[Python](#tab/python)

Pro odebrání privátního koncového bodu použijte [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) .

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Rozšíření Azure CLI pro Machine Learning](reference-azure-machine-learning-cli.md) poskytuje příkaz [AZ ml v rámci privátního koncového bodu v pracovním prostoru](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) .

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z pracovního prostoru Azure Machine Learning na portálu vyberte __připojení privátního koncového bodu__ a pak vyberte koncový bod, který chcete odebrat. Nakonec vyberte __Odebrat__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Použití pracovního prostoru v rámci privátního koncového bodu

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti.

> [!IMPORTANT]
> Aby nedošlo k dočasnému přerušení připojení, společnost Microsoft doporučuje po povolení privátního odkazu vyprázdnit mezipaměť DNS na počítačích, které se připojují k pracovnímu prostoru. 

Informace o službě Azure Virtual Machines najdete v [dokumentaci k Virtual Machines](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Povolit veřejný přístup

V některých situacích možná budete chtít, aby se někdo mohl připojit k zabezpečenému pracovnímu prostoru přes Veřejný koncový bod, a ne přes virtuální síť. Po nakonfigurování pracovního prostoru s privátním koncovým bodem můžete volitelně povolit veřejný přístup k pracovnímu prostoru. Tím nedojde k odebrání privátního koncového bodu. Veškerá komunikace mezi součástmi za virtuální sítí je stále zabezpečená. Umožňuje také veřejný přístup k pracovnímu prostoru, a to i k privátnímu přístupu prostřednictvím virtuální sítě.

> [!WARNING]
> Při připojování přes Veřejný koncový bod některé funkce studia nebudou mít přístup k vašim datům. K tomuto problému dochází, když jsou data uložená ve službě, která je zabezpečená za virtuální sítí. Například účet Azure Storage.

Chcete-li povolit veřejný přístup k pracovnímu prostoru podporujícímu soukromým linkám, postupujte následovně:

# <a name="python"></a>[Python](#tab/python)

Pro odebrání privátního koncového bodu použijte [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) .

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Rozšíření Azure CLI pro Machine Learning](reference-azure-machine-learning-cli.md) poskytuje příkaz [AZ ml Workspace Update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_update) . Chcete-li povolit veřejný přístup k pracovnímu prostoru, přidejte parametr `--allow-public-access true` .

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V současné době neexistuje způsob, jak tuto funkci povolit pomocí portálu.

---


## <a name="next-steps"></a>Další kroky

* Další informace o zabezpečení Azure Machine Learning pracovního prostoru najdete v článku věnovaném [izolaci virtuální sítě a ochraně osobních údajů](how-to-network-security-overview.md) .

* Pokud plánujete používat vlastní řešení DNS ve virtuální síti, přečtěte si téma [jak použít pracovní prostor s vlastním serverem DNS](how-to-custom-dns.md).
