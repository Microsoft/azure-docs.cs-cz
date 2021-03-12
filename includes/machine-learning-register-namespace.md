---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623562"
---
* Při vytváření nového pracovního prostoru můžete buď automaticky vytvořit služby, které pracovní prostor potřebuje, nebo použít existující služby. Pokud chcete používat __existující služby z jiného předplatného Azure__ , než je pracovní prostor, musíte v předplatném, které tyto služby obsahuje, zaregistrovat obor názvů Azure Machine Learning. Například vytvořením pracovního prostoru v předplatném A, který používá účet úložiště z předplatného B, musí být obor názvů Azure Machine Learning zaregistrován v předplatném B před tím, než budete moci použít účet úložiště v pracovním prostoru.

    Poskytovatel prostředků pro Azure Machine Learning je __Microsoft. MachineLearningServices__. Informace o tom, jak zjistit, jestli je zaregistrované a jak se zaregistrovat, najdete v článku [poskytovatelé a typy prostředků Azure](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > To platí jenom pro prostředky poskytnuté během vytváření pracovního prostoru. Účty Azure Storage, Azure Container Registry, Azure Key Vault a Application Insights.
