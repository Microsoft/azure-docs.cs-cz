---
title: Konfigurace privátního koncového bodu
titleSuffix: Azure Machine Learning
description: Pomocí privátního odkazu Azure získáte zabezpečený přístup k pracovnímu prostoru Azure Machine Learning z virtuální sítě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828118"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurace privátního odkazu Azure pro pracovní prostor Azure Machine Learning

V tomto dokumentu se dozvíte, jak pomocí privátního propojení Azure s vaším pracovním prostorem Azure Machine Learning. Informace o vytvoření virtuální sítě pro Azure Machine Learning najdete v tématu [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md) .

Privátní odkaz Azure umožňuje připojit se k pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. Přístup k pracovnímu prostoru pak můžete omezit tak, aby se nacházet jenom přes privátní IP adresy. Soukromý odkaz pomáhá snižovat riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Privátní propojení Azure neovlivňuje plochu ovládacího prvku Azure (operace správy), jako je například odstranění pracovního prostoru nebo Správa výpočetních prostředků. Například vytvoření, aktualizace nebo odstranění cíle služby Compute. Tyto operace se provádějí na veřejném Internetu jako normální. Operace roviny dat, jako je například použití Azure Machine Learning studia, rozhraní API (včetně publikovaných kanálů) nebo sady SDK, používají privátní koncový bod.
>
> Pokud používáte Mozilla Firefox, může dojít k potížím při pokusu o přístup k privátnímu koncovému bodu pro váš pracovní prostor. Tento problém může souviset s DNS přes HTTPS v Mozilla. Jako alternativní řešení doporučujeme používat Microsoft Edge Google Chrome.

## <a name="prerequisites"></a>Předpoklady

Pokud plánujete použít pracovní prostor s povoleným privátním propojením s klíčem spravovaným zákazníkem, musíte požádat o tuto funkci pomocí lístku podpory. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Omezení

Použití Azure Machine Learningho pracovního prostoru s privátním odkazem není k dispozici ve Azure Government oblastech nebo v oblastech Azure Čína 21Vianet.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Vytvoření pracovního prostoru, který používá privátní koncový bod

K vytvoření pracovního prostoru s privátním koncovým bodem použijte jednu z následujících metod:

> [!TIP]
> Šablona Azure Resource Manager může v případě potřeby vytvořit novou virtuální síť. Ostatní metody vyžadují stávající virtuální síť.

# <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

Šablona Azure Resource Manager v [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) poskytuje snadný způsob, jak vytvořit pracovní prostor s privátním koncovým bodem a virtuální sítí.

Informace o použití této šablony, včetně privátních koncových bodů, najdete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Sada Azure Machine Learning Python SDK poskytuje třídu [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , která se dá použít s [pracovním prostorem. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) k vytvoření pracovního prostoru s privátním koncovým bodem. Tato třída vyžaduje existující virtuální síť.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Rozšíření Azure CLI pro Machine Learning](reference-azure-machine-learning-cli.md) poskytuje příkaz [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . Následující parametry tohoto příkazu lze použít k vytvoření pracovního prostoru s privátní sítí, ale vyžaduje existující virtuální síť:

* `--pe-name`: Název privátního koncového bodu, který je vytvořen.
* `--pe-auto-approval`: Zda mají být připojení privátního koncového bodu k pracovnímu prostoru automaticky schválena.
* `--pe-resource-group`: Skupina prostředků, ve které se vytvoří privátní koncový bod. Musí se jednat o stejnou skupinu, která obsahuje virtuální síť.
* `--pe-vnet-name`: Existující virtuální síť pro vytvoření privátního koncového bodu v nástroji.
* `--pe-subnet-name`: Název podsítě, v níž se má vytvořit privátní koncový bod. Výchozí hodnota je `default`.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Karta __sítě__ v nástroji Azure Machine Learning Studio umožňuje konfigurovat soukromý koncový bod. Vyžaduje ale existující virtuální síť. Další informace najdete v tématu [vytvoření pracovních prostorů na portálu](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Použití pracovního prostoru v rámci privátního koncového bodu

Vzhledem k tomu, že komunikace s pracovním prostorem je povolená jenom z virtuální sítě, musí být všechna vývojová prostředí, která používají pracovní prostory, členy virtuální sítě. Například virtuální počítač ve virtuální síti.

> [!IMPORTANT]
> Aby nedošlo k dočasnému přerušení připojení, společnost Microsoft doporučuje po povolení privátního odkazu vyprázdnit mezipaměť DNS na počítačích, které se připojují k pracovnímu prostoru. 

Informace o službě Azure Virtual Machines najdete v [dokumentaci k Virtual Machines](/azure/virtual-machines/).


## <a name="next-steps"></a>Další kroky

* Další informace o zabezpečení Azure Machine Learning pracovního prostoru najdete v článku věnovaném [izolaci virtuální sítě a ochraně osobních údajů](how-to-network-security-overview.md) .

* Pokud plánujete používat vlastní řešení DNS ve virtuální síti, přečtěte si téma [jak použít pracovní prostor s vlastním serverem DNS](how-to-custom-dns.md).
