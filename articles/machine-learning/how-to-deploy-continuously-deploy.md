---
title: Průběžné nasazování modelů Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se průběžně nasazovat modely s rozšířením Azure Machine Learning DevOps. Automaticky vyhledávat a nasazovat nové verze modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609409"
---
# <a name="continuously-deploy-models"></a>Průběžné nasazování modelů

Tento článek ukazuje, jak používat průběžné nasazování ve službě Azure DevOps k automatické kontrole nových verzí registrovaných modelů a vložení těchto nových modelů do produkčního prostředí.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už zaregistrovali model v pracovním prostoru Azure Machine Learning. V [tomto kurzu](how-to-train-scikit-learn.md) najdete příklad školení a registrace modelu scikit-učení.

## <a name="continuously-deploy-models"></a>Průběžné nasazování modelů

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Rozšíření Machine Learning pro Azure DevOps můžete použít k aktivaci kanálu nasazení, když je nový model strojového učení zaregistrován v pracovním prostoru Azure Machine Learning.

1. Zaregistrujte se do [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up), který umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu nebo Cloud. (Všimněte si, že Azure Pipelines nejsou stejné jako [Machine Learning kanály](concept-ml-pipelines.md#compare).)

1. [Vytvořte projekt Azure DevOps.](/azure/devops/organizations/projects/create-project)

1. Nainstalujte [Machine Learning rozšíření pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Pomocí připojení služby nastavte připojení instančního objektu k vašemu pracovnímu prostoru Azure Machine Learning, abyste mohli přistupovat ke artefaktům. Přejít na nastavení projektu, vyberte **připojení služby** a pak vyberte **Azure Resource Manager**:

    [![Vyberte Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. V seznamu **úroveň oboru** vyberte možnost **AzureMLWorkspace** a potom zadejte zbývající hodnoty:

    ![Vybrat AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Pokud chcete model strojového učení průběžně nasadit pomocí Azure Pipelines, vyberte v části kanály možnost **verze**. Přidejte nový artefakt a pak vyberte artefakt **modelu AzureML** a připojení služby, které jste vytvořili dříve. Vyberte model a verzi pro aktivaci nasazení:

    [![Vybrat model AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Povolte na artefaktu modelu aktivační událost modelu. Když zapnete aktivační událost, pokaždé, když je v pracovním prostoru zaregistrovaná verze daného modelu (tj. nejnovější verze), se aktivuje kanál vydané verze Azure DevOps.

    [![Povolení triggeru modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Další kroky

Další příklady průběžného nasazování modelů ML najdete v následujících projektech na GitHubu.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)