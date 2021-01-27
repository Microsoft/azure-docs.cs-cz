---
title: Použít vlastní server DNS
titleSuffix: Azure Machine Learning
description: Jak nakonfigurovat vlastní server DNS pro práci s pracovním prostorem Azure Machine Learning a soukromým koncovým bodem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2215c47fcd250a9ac1d6621f7e4b434bd33b3832
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871091"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Používání pracovního prostoru s vlastním serverem DNS

Při použití Azure Machine Learningho pracovního prostoru s privátním koncovým bodem existuje [několik způsobů, jak řešit překlad názvů DNS](../private-link/private-endpoint-dns.md). Ve výchozím nastavení Azure automaticky zpracovává překlad adres IP pro váš pracovní prostor a soukromý koncový bod. Pokud místo toho _použijete vlastní server DNS_ _, musíte ručně vytvořit položky DNS nebo pro tento pracovní prostor použít podmíněné servery pro dodávání.

> [!IMPORTANT]
> Tento článek popisuje, jak najít plně kvalifikovaný název domény (FQDN) a IP adresy pro tyto položky, které neposkytuje informace o konfiguraci záznamů DNS pro tyto položky. Informace o tom, jak přidat záznamy, najdete v dokumentaci k vašemu softwaru DNS.

## <a name="prerequisites"></a>Požadavky

- Virtual Network Azure, který používá [vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Azure Machine Learning pracovní prostor s privátním koncovým bodem. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Znalost používání [izolace sítě během školení & odvození](./how-to-network-security-overview.md).

- Známá [Konfigurace zóny DNS privátního koncového bodu Azure](../private-link/private-endpoint-dns.md)

- Volitelně můžete rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="fqdns-in-use"></a>Používané plně kvalifikované názvy domény
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>Tyto plně kvalifikované názvy domén jsou používány v následujících oblastech: eastus, southcentralus a westus2.
Následující seznam obsahuje plně kvalifikované názvy domény (FQDN) používané vaším pracovním prostorem:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Pokud vytvoříte výpočetní instanci, musíte také přidat položku pro `<instance-name>.<region>.instances.azureml.ms` s privátní IP adresou privátního koncového bodu v pracovním prostoru.

    > [!NOTE]
    > Instance služby COMPUTE jsou dostupné jenom v rámci virtuální sítě.
    
### <a name="these-fqdns-are-in-use-in-all-other-regions"></a>Tyto plně kvalifikované názvy domén se používají ve všech ostatních oblastech.
Následující seznam obsahuje plně kvalifikované názvy domény (FQDN) používané vaším pracovním prostorem:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > Instance služby COMPUTE jsou dostupné jenom v rámci virtuální sítě.

## <a name="find-the-ip-addresses"></a>Najít IP adresy

Pokud chcete najít interní IP adresy pro plně kvalifikované názvy domén ve virtuální síti, použijte jednu z následujících metod:

> [!NOTE]
> Plně kvalifikované názvy domény a IP adresy se budou lišit v závislosti na vaší konfiguraci. Například hodnota identifikátoru GUID v názvu domény bude specifická pro váš pracovní prostor.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyberte __pracovní prostor__ Azure Machine Learning.
1. V části __Nastavení__ vyberte __připojení privátního koncového bodu__.
1. Vyberte odkaz ve sloupci __privátní koncový bod__ , který se zobrazí.
1. V dolní části stránky je seznam plně kvalifikovaných názvů domény (FQDN) a IP adresy privátního koncového bodu v pracovním prostoru.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Seznam plně kvalifikovaných názvů domén na portálu":::

---

Informace vrácené ze všech metod jsou stejné; seznam plně kvalifikovaného názvu domény a privátní IP adresy pro prostředky.

| FQDN | IP adresa |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Některé plně kvalifikované názvy domén nejsou zobrazeny v seznamu privátním koncovým bodem, ale jsou vyžadovány pracovním prostorem v eastus, southcentralus a westus2. Tyto plně kvalifikované názvy domén jsou uvedené v následující tabulce a musí se taky přidat na server DNS nebo do zóny Azure Privátní DNS:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Pokud máte výpočetní instanci, použijte `<instance-name>.<region>.instances.azureml.ms` , kde `<instance-name>` je název vaší výpočetní instance. Použijte prosím privátní IP adresu privátního koncového bodu v pracovním prostoru. Počítejte s tím, že ke výpočetní instanci se dá dostat jenom z virtuální sítě.
>
> Pro všechny tyto IP adresy použijte stejnou adresu jako u `*.api.azureml.ms` položek vrácených z předchozích kroků.

## <a name="next-steps"></a>Další kroky

Další informace o použití Azure Machine Learning s virtuální sítí najdete v tématu [Přehled virtuální sítě](how-to-network-security-overview.md).

Další informace o integraci privátních koncových bodů do konfigurace DNS najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../private-link/private-endpoint-dns.md).