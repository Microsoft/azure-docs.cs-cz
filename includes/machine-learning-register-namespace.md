---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445191"
---
* Při vytváření nového pracovního prostoru můžete buď automaticky vytvořit služby, které pracovní prostor potřebuje, nebo použít existující služby. Pokud chcete používat __existující služby z jiného předplatného Azure__ , než je pracovní prostor, musíte v předplatném, které tyto služby obsahuje, zaregistrovat obor názvů Azure Machine Learning. Například vytvořením pracovního prostoru v předplatném A, který používá účet úložiště z předplatného B, musí být obor názvů Azure Machine Learning zaregistrován v předplatném B před tím, než budete moci použít účet úložiště v pracovním prostoru.

    Poskytovatel prostředků pro Azure Machine Learning je __Microsoft. MachineLearningService__. Informace o tom, jak zjistit, jestli je zaregistrované a jak se zaregistrovat, najdete v článku [poskytovatelé a typy prostředků Azure](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > To platí jenom pro prostředky poskytnuté během vytváření pracovního prostoru. Účty Azure Storage, Azure Container Registry, Azure Key Vault a Application Insights.