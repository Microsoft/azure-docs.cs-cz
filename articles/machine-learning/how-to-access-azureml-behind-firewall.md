---
title: Použít bránu firewall
titleSuffix: Azure Machine Learning
description: Řízení přístupu k pracovním prostorům Azure Machine Learning pomocí bran Azure firewall. Přečtěte si o hostitelích, které musíte přes bránu firewall umožňovat, aby Azure Machine Learning správně fungovaly.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 27b625dfa31b366d95922e1dd0bad7fda6e86ed4
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540066"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Pro Azure Machine Learning použít pracovní prostor za bránou firewall

V tomto článku se dozvíte, jak nakonfigurovat Azure Firewall pro použití s pracovním prostorem Azure Machine Learning.

> [!IMPORTANT]
> I když jsou informace v tomto dokumentu založené na použití Azure Firewall, měli byste ho používat s jinými produkty brány firewall. Pokud máte dotazy týkající se povolení komunikace přes bránu firewall, přečtěte si prosím dokumentaci pro bránu firewall, kterou používáte.

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
| **your-acr-server-name.azurecr.io** | Vyžaduje se jenom v případě, že je vaše Azure Container Registry za virtuální sítí. V této konfiguraci se privátní odkaz vytvoří z prostředí Microsoftu do instance ACR v rámci vašeho předplatného. Pro Azure Machine Learning pracovní prostor použijte název serveru ACR. |

## <a name="python-hosts"></a>Hostitelé Pythonu

Hostitelé v této části se používají k instalaci balíčků Pythonu. Jsou požadovány během vývoje, školení a nasazení. 

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **anaconda.com** | Používá se k instalaci výchozích balíčků. |
| **\*. anaconda.org** | Slouží k získání dat úložiště. |
| **pypi.org** | Slouží k vypsání závislostí z výchozího indexu, pokud existují, a index není přepsán uživatelským nastavením. Pokud je index přepsán, je nutné také povolte ** \* . pythonhosted.org**. |

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
