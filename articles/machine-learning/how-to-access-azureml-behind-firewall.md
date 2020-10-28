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
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d0f30edeb24f3c4abed6f144f3fb7f755cc08a72
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629455"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Pro Azure Machine Learning použít pracovní prostor za bránou firewall

V tomto článku se dozvíte, jak nakonfigurovat Azure Firewall pro řízení přístupu k pracovnímu prostoru Azure Machine Learning a k veřejnému Internetu.   Další informace o zabezpečení Azure Machine Learning najdete v tématu [Enterprise Security for Azure Machine Learning](concept-enterprise-security.md)

I když jsou informace v tomto dokumentu založené na použití [Azure firewall](../firewall/tutorial-firewall-deploy-portal.md), měli byste ho používat s jinými produkty brány firewall. Pokud máte dotazy týkající se povolení komunikace přes bránu firewall, přečtěte si prosím dokumentaci pro bránu firewall, kterou používáte.

## <a name="application-rules"></a>Pravidla aplikace

V bráně firewall vytvořte _pravidlo aplikace_ , které umožní provoz na základě adres v tomto článku a z nich.

> [!TIP]
> Když přidáváte pravidlo sítě, nastavte __protokol__ na any a porty na `*` .
>
> Další informace o konfiguraci Azure Firewall najdete v tématu [nasazení a konfigurace Azure firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="routes"></a>Trasy

Při konfiguraci odchozí trasy pro podsíť, která obsahuje prostředky Azure Machine Learning, použijte pokyny v části [vynucené tunelování](how-to-secure-training-vnet.md#forced-tunneling) pro zabezpečení školicího prostředí.

## <a name="microsoft-hosts"></a>Hostitelé Microsoftu

Pokud není správně nakonfigurovaný, může Brána firewall způsobovat problémy s vaším pracovním prostorem. K dispozici je celá řada názvů hostitelů, které používá Azure Machine Learning pracovní prostor.

Hostitelé v této části vlastní Microsoft a poskytují služby vyžadované pro správné fungování pracovního prostoru.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **login.microsoftonline.com** | Authentication |
| **management.azure.com** | Slouží k získání informací o pracovním prostoru. |
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
| **\*. notebooks.azure.net** | Vyžaduje poznámkové bloky v Azure Machine Learning Studiu. |
| **\*. file.core.windows.net** | Vyžadováno průzkumníkem souborů v aplikaci Azure Machine Learning Studio. |
| **\*. dfs.core.windows.net** | Vyžadováno průzkumníkem souborů v aplikaci Azure Machine Learning Studio. |
| **graph.windows.net** | Vyžadováno pro poznámkové bloky |

> [!TIP]
> Pokud plánujete použití federované identity, postupujte podle [osvědčených postupů pro zabezpečení Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) článku.

## <a name="python-hosts"></a>Hostitelé Pythonu

Hostitelé v této části se používají k instalaci balíčků Pythonu. Jsou požadovány během vývoje, školení a nasazení. 

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Používá se k instalaci výchozích balíčků. |
| **\*. anaconda.org** | Slouží k získání dat úložiště. |
| **pypi.org** | Slouží k vypsání závislostí z výchozího indexu, pokud existují, a index není přepsán uživatelským nastavením. Pokud je index přepsán, je nutné také povolte **\* . pythonhosted.org** . |

## <a name="r-hosts"></a>Hostitelé R

Hostitelé v této části se používají k instalaci balíčků R. Jsou požadovány během vývoje, školení a nasazení.

> [!IMPORTANT]
> Sada R SDK pro Azure Machine Learning interně používá balíčky Pythonu. Proto musíte taky umožnit hostitelům Pythonu přes bránu firewall.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **cloud.r-project.org** | Používá se při instalaci balíčků CRAN. |

## <a name="azure-government-region"></a>Oblast Azure Government

Požadované adresy URL pro oblasti Azure Government

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | Oblast US-Arizona |
| **usgovvirginia.api.ml.azure.us** | Oblast US-Virginia |

## <a name="next-steps"></a>Další kroky

* [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Zabezpečení experimentů a úloh odvozování v Azure ML ve virtuální síti Azure](how-to-network-security-overview.md)
