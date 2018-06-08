---
title: Co je nového v Azure Machine Learning | Microsoft Docs
description: Nové funkce, které jsou k dispozici v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 61bea7fde96b239a50ec25a702a73ecfb62ce717
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836068"
---
# <a name="whats-new-in-azure-machine-learning"></a>Novinky ve službě Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>2017 března verzi aktualizace Microsoft Azure Machine Learning poskytuje následující funkce:



* Vyhrazené kapacity pro Azure Machine Learning BES úlohy

    Počítač fondu Batch Learning zpracování používá [Azure Batch](../../batch/batch-technical-overview.md) služba poskytnout spravované zákazníkem škálování pro službu Azure Machine Learning dávky spuštění. Zpracování fondu batch můžete vytvořit fondy Azure Batch, na kterých můžete odesílat dávkové úlohy a jejich spuštění předvídatelný způsobem.

    Další informace najdete v tématu [služby Azure Batch pro Machine Learning úlohy](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Srpna 2016 verzi Microsoft Azure Machine Learning aktualizace poskytují následující funkce:
* Classic webové služby teď můžete spravovat v novém [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/) portál, který poskytuje jednom místě spravovat všechny aspekty webové služby.    
  * Které poskytuje webovou službu [Statistika využití](manage-new-webservice.md).
  * Zjednodušuje testování pomocí ukázkových dat volání Azure Machine Learning vzdáleného požadavku.
  * Poskytuje nové zkušební stránku služby Batch spuštění ukázkových dat a úlohy odeslání historie.
  * Nabízí jednodušší správu koncový bod.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Července 2016 verzi Microsoft Azure Machine Learning aktualizace poskytují následující funkce:
* Webové služby se nyní spravují jako prostředky Azure, které jsou spravované prostřednictvím [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) rozhraní, aby vám umožnil následující vylepšení:
  * Existují nové [rozhraní REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) k nasazení a správě Resource Manager na základě webové služby.
  * Nová [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/) portál, který poskytuje jednom místě spravovat všechny aspekty webové služby.
* Zahrnuje nový model nasazení založené na předplatném, oblasti služby webové služby pomocí Správce prostředků na základě rozhraní API využívat poskytovatele prostředků Resource Manageru pro webové služby.
* Zavádí nové [cenových plánů](https://azure.microsoft.com/pricing/details/machine-learning/) a naplánovat možnosti správy pomocí nového správce prostředků RP pro fakturaci.
  * Teď můžete [nasazení webové služby do několika oblastí](how-to-deploy-to-multiple-regions.md) bez nutnosti vytvoření odběru v každé oblasti.
* Poskytuje webovou službu [Statistika využití](manage-new-webservice.md).
* Zjednodušuje testování pomocí ukázkových dat volání Azure Machine Learning vzdáleného požadavku.
* Poskytuje nové zkušební stránku služby Batch spuštění ukázkových dat a úlohy odeslání historie.

Kromě toho Machine Learning Studio je aktualizovaná tak, aby umožňují nasadit do nového modelu webové služby nebo pokračovat v nasazování do klasického modelu webové služby. 

