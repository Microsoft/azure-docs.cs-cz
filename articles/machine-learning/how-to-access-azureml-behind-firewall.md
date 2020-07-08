---
title: Použít bránu firewall
titleSuffix: Azure Machine Learning
description: Řízení přístupu k pracovním prostorům Azure Machine Learning pomocí bran Azure firewall. Přečtěte si o hostitelích, které musíte přes bránu firewall umožňovat, aby Azure Machine Learning správně fungovaly.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: 31daec93352c0e142075a55c61f2b8d3a6d56fab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080228"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Použít pracovní prostor za Azure Firewall Azure Machine Learning

V tomto článku se dozvíte, jak nakonfigurovat Azure Firewall pro použití s pracovním prostorem Azure Machine Learning.

Azure Firewall lze použít k řízení přístupu k pracovnímu prostoru Azure Machine Learning a k veřejnému Internetu. Pokud není správně nakonfigurovaný, může Brána firewall způsobovat problémy s vaším pracovním prostorem. K dispozici je celá řada názvů hostitelů, které jsou používány Azure Machine Learning pracovním prostorem, které jsou popsány v tomto článku.

## <a name="network-rules"></a>Pravidla sítě

V bráně firewall vytvořte síťové pravidlo umožňující provoz na a z adres v tomto článku.

> [!TIP]
> Když přidáváte pravidlo sítě, nastavte __protokol__ na any a porty na `*` .
>
> Další informace o konfiguraci Azure Firewall najdete v tématu [nasazení a konfigurace Azure firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Hostitelé Microsoftu

Hostitelé v této části vlastní Microsoft a poskytují služby vyžadované pro správné fungování pracovního prostoru.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Školicí clustery |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Používá se v Azure Machine Learning Studiu |
| **\*. azureml.ms** | Používá se Azure Machine Learning rozhraní API. |
| **\*. experiments.azureml.net** | Používá experimenty spuštěné v Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Používá se k registraci a nasazení modelů.|
| **mlworkspace.azure.ai** | Používá se Azure Portal při zobrazení pracovního prostoru. |
| **\*. aether.ms** | Používá se při spouštění Azure Machine Learningch kanálů. |
| **\*. instances.azureml.net** | Azure Machine Learning výpočetní instance |
| **\*. instances.azureml.ms** | Pokud je v pracovním prostoru povolený privátní odkaz, Azure Machine Learning výpočetní instance. |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Microsoft Container Registry pro základní image Docker |

## <a name="python-hosts"></a>Hostitelé Pythonu

Hostitelé v této části se používají k instalaci balíčků Pythonu. Jsou požadovány během vývoje, školení a nasazení. 

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **anaconda.com** | Používá se při instalaci balíčků conda. |
| **pypi.org** | Používá se při instalaci balíčků PIP. |

## <a name="r-hosts"></a>Hostitelé R

Hostitelé v této části se používají k instalaci balíčků R. Jsou požadovány během vývoje, školení a nasazení.

> [!IMPORTANT]
> Sada R SDK pro Azure Machine Learning interně používá balíčky Pythonu. Proto musíte taky umožnit hostitelům Pythonu přes bránu firewall.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **cloud.r-project.org** | Používá se při instalaci balíčků CRAN. |

Další kroky

* [[Nasazení a konfigurace Azure firewall](../firewall/tutorial-firewall-deploy-portal.md)]
* [Zabezpečení experimentů s Azure ML a odvození úloh v rámci Azure Virtual Network](how-to-enable-virtual-network.md)
