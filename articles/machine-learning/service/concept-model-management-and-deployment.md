---
title: Správa a nasazování modelů ve službě Azure Machine Learning
description: Další informace o použití služby Azure Machine Learning nasazovat, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí služby Azure Machine Learning, na místním počítači nebo z jiných zdrojů.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: afba483172bc34b9d54afc3af755f0967affc875
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239162"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Spravovat, nasazovat a sledovat modely pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak pomocí služby Azure Machine Learning nasadit, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí Azure Machine Learning na místním počítači nebo z jiných zdrojů. 

Následující diagram znázorňuje kompletního nasazení pracovního postupu: [ ![pracovní postup nasazení pro Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Pracovní postup nasazení zahrnuje následující kroky:
1. **Zaregistrujte model** v registru hostované ve vašem pracovním prostoru služby Azure Machine Learning
1. **Zaregistrovat image** , který dvojice model s hodnoticí skript a závislosti v přenosných kontejneru 
1. **Nasazení** image jako webovou službu v cloudu nebo do hraničních zařízení
1. **Monitorování a shromažďování dat**

Každý krok lze provést, samostatně nebo jako součást příkazu pro jedno nasazení. Kromě toho můžete integrovat do nasazení **pracovních postupů CI/CD** jak je znázorněno na tomto obrázku.

[ !["Služba azure Machine Learning průběžné integrace a nasazování (CI/CD) cyklu.](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>Krok 1: Zaregistrujte model

Model registru uchovává informace o všech modelů v pracovním prostoru služby Azure Machine Learning.
Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky.

Nelze odstranit modely, které se používají v obrázku.

## <a name="step-2-register-image"></a>Krok 2: Registrace image

Bitové kopie umožňují spolehlivé model nasazení, spolu s všechny součásti potřebné k použití modelu. Bitová kopie obsahuje následující položky:

* Model
* Modul vyhodnocení
* Hodnoticí souborů nebo aplikace
* Všechny závislosti potřebné k určení skóre modelu

Image můžete také zahrnout součásti sady SDK pro protokolování a monitorování. Protokoly data sady SDK slouží k vyladění nebo přeučování modelu, včetně vstup a výstup modelu.

Azure Machine Learning podporuje oblíbených architektur, ale obecně můžete pracovat libovolné architektury, které mohou být nainstalovány nástrojem pip.

Při vytvoření pracovního prostoru, takže ostatní několik dalších prostředků Azure použil tento pracovní prostor.
Všechny objekty používané k vytvoření této image se ukládají v účtu úložiště Azure ve vašem pracovním prostoru. Image se vytvoří a uloží v Azure Container Registry. Při vytváření bitové kopie, které jsou uloženy v registru image a může být dotazována k vyhledání bitové kopie můžete zadat další metadata značky.

## <a name="step-3-deploy-image"></a>Krok 3: Nasazení bitové kopie

Registrovaný imagí můžete nasadit do cloudu nebo na hraničních zařízeních. Proces nasazení vytvoří všechny prostředky potřebné k monitorování, Vyrovnávání zatížení a automatické škálování vašeho modelu. Přístup k nasazeným službám dá se zabezpečit s ověřováním na základě certifikátů tím, že poskytuje prostředky zabezpečení během nasazení. Rovněž lze upgradovat stávajícího nasazení použít novější image.

Nasazení webové služby je také možné prohledávat. Můžete například vyhledat všechny nasazení konkrétní model nebo image.

[ ![Odvozování cíle](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Image můžete nasadit následující [cíle nasazení](how-to-deploy-and-where.md) v cloudu:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Azure počítače FPGA
* Zařízení Azure IoT Edge

Jak je vaše služba nasazená, odvozování požadavku je automaticky s vyrovnáváním zatížení a splňovat všechny špičky na vyžádání je škálování clusteru. [Telemetrická data o vaší služby](https://docs.microsoft.com/python/api/azureml-telemetry/azureml.telemetry?view=azure-ml-py) ve službě Azure Application Insights přidružený k vaší pracovní prostor se dají zachytit.

## <a name="step-4-monitor-models-and-collect-data"></a>Krok 4: Monitorování modely a shromažďování dat

Sada SDK pro zachycení protokolování a datový model je k dispozici, takže můžete monitorovat vstup, výstup a další relevantní data z vašeho modelu. Data se ukládají jako objekty blob v účtu úložiště Azure pro váš pracovní prostor.

Použití sady SDK obsahující váš model, importujete sady SDK do hodnoticího skriptu nebo aplikace. Sada SDK pak můžete protokolovat data, jako jsou parametry, výsledky nebo podrobnosti vstupu.

Pokud se rozhodnete [Povolit shromažďování dat modelu](how-to-enable-data-collection.md) pokaždé, když nasazujete bitovou kopii, jsou automaticky zřídí informace potřebné k zaznamenání dat, jako je například přihlašovací údaje k úložišti objektů blob v osobní.

> [!Important]
> Microsoft nezobrazují dat shromážděných z vašeho modelu. Data se odesílají přímo do účtu úložiště Azure pro váš pracovní prostor.

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning.
