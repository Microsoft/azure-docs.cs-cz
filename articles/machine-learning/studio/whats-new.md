---
title: Co je nového v nástroji Azure Machine Learning Studio? | Dokumenty Microsoft
description: Nové funkce, které jsou k dispozici v nástroji Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: c93bb9a2fa7991df265b4767585d920c137a436d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311139"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Co je nového v nástroji Azure Machine Learning Studio?

## <a name="october-2018"></a>Říjen 2018

Modul jazyka R v [spustit skript jazyka R](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) modul přidal nové verze R modulu runtime--Microsoft R Open (MRO) 3.4.4. MRO 3.4.4 je založená na open-source systému R CRAN 3.4.4 a proto je kompatibilní s balíčky, které funguje s touto verzí jazyka R.  Další informace o podporované balíčky R v článku "[balíčky R podporované službou Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)".

## <a name="march-2017"></a>. Března 2017 
Tato verze aktualizace pro Microsoft Azure Machine Learning poskytuje následující funkce:

* Kapacita vyhrazená pro úlohy BES Azure Machine Learning

    Machine Learning fondu služby Batch používá zpracování [Azure Batch](../../batch/batch-technical-overview.md) služba k poskytování spravovaného zákazníkem škálování pro spuštění služby Azure Machine Learning služby Batch. Zpracování fondu služby batch můžete vytvořit fondy Azure Batch, ve kterých můžete odesílat úlohy služby batch a je mít znovu spustit předvídatelným způsobem.

    Další informace najdete v tématu [služby Azure Batch pro úlohy strojového učení](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>. Srpna 2016 
Tato verze aktualizace pro Microsoft Azure Machine Learning poskytují následující funkce:
* Klasické webové služby se teď dají spravovat v novém [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) portál, který poskytuje jedno místo ke správě všech aspektů vaší webové služby.    
  * Webová služba poskytující [statistiky o využití](manage-new-webservice.md).
  * Zjednodušuje testování pomocí ukázkových dat volání Azure Machine Learning vzdálené-požadavku.
  * Poskytuje nové stránky testů služba Batch Execution historie odesílání vzorku dat a úlohy.
  * Nabízí jednodušší správu koncový bod.

## <a name="july-2016"></a>Červenec 2016 
Tato verze aktualizace pro Microsoft Azure Machine Learning poskytují následující funkce:
* Webové služby se teď spravují jako prostředky Azure, které jsou spravované prostřednictvím [Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) rozhraní umožňující následující vylepšení:
  * Existují nové [rozhraní REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) nasazovat a spravovat váš správce prostředků na základě webové služby.
  * Je tu nový [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) portál, který poskytuje jedno místo ke správě všech aspektů vaší webové služby.
* Zahrnuje nové rozhraní API využití zprostředkovatele prostředků Resource Manageru pro webové služby založené na pomocí Resource Manageru modelu pro nasazení na základě předplatného, ve více oblastech webové služby.
* Zavádí nové [cenové plány](https://azure.microsoft.com/pricing/details/machine-learning/) a naplánovat možnosti správy pomocí nového poskytovatele prostředků Resource Manageru pro fakturaci.
  * Teď můžete [nasazení webové služby do několika oblastí](how-to-deploy-to-multiple-regions.md) bez nutnosti vytvoření odběru v jednotlivých oblastech.
* Poskytuje webovou službu [statistiky o využití](manage-new-webservice.md).
* Zjednodušuje testování pomocí ukázkových dat volání Azure Machine Learning vzdálené-požadavku.
* Poskytuje nové stránky testů služba Batch Execution historie odesílání vzorku dat a úlohy.

Kromě toho nástroje Machine Learning Studio byl aktualizován, aby bylo možné nasadit na nový model webových služeb nebo pokračovat v nasazování do klasického modelu webové služby. 

