---
title: Správa a nasazování modelů ve službě Azure Machine Learning
description: Zjistěte, jak používat Azure Machine Learning nasazovat, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí Azure Machine Learning na místním počítači nebo z jiných zdrojů.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: 634f33b6d4ed6e272dfb3d1443b0afc63f822d43
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055879"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning"></a>Spravovat, nasazovat a sledovat modely Azure Machine Learning

V tomto článku se dozvíte, jak používat Azure Machine Learning nasazovat, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí Azure Machine Learning na místním počítači nebo z jiných zdrojů. 

[!["Služba azure Machine Learning průběžné integrace a nasazování (CI/CD) cyklu.](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="deployment-workflow"></a>Pracovní postup nasazení

Pracovní postup nasazení se skládá z: 
1. **Zaregistrujte model** v registru hostované ve vašem pracovním prostoru Azure Machine Learning
1. **Zaregistrovat image** , který dvojice model s hodnoticí skript a závislosti v přenosných kontejneru 
1. **Nasazení** image jako webovou službu v cloudu nebo do hraničních zařízení
1. **Monitorování a shromažďování dat**

Můžete provést jednotlivé kroky samostatně nebo jako součást příkazu pro jedno nasazení. 

Následující diagram znázorňuje kanál kompletního nasazení:

[![Nasazení kanálu](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

## <a name="step-1-model-registration"></a>Krok 1: Registrace modelu

Model registru uchovává informace o všech modelů v pracovním prostoru Azure Machine Learning.
Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky.

Nelze odstranit modely, které se používají v obrázku.

## <a name="step-2-image-registration"></a>Krok 2: Registrace bitové kopie

Bitové kopie umožňují spolehlivé model nasazení, spolu s všechny součásti potřebné k použití modelu. Bitová kopie obsahuje následující položky:

* Model
* Modul vyhodnocení
* Hodnoticí souborů nebo aplikace
* Všechny závislosti potřebné k určení skóre modelu

Image můžete také zahrnout součásti sady SDK pro protokolování a monitorování. Protokoly data sady SDK slouží k vyladění nebo přeučování modelu, včetně vstup a výstup modelu.

Azure Machine Learning podporuje oblíbených architektur, ale obecně můžete pracovat libovolné architektury, které mohou být nainstalovány nástrojem pip.

Při vytvoření pracovního prostoru, takže ostatní několik dalších prostředků Azure použil tento pracovní prostor.
Všechny objekty používané k vytvoření této image se ukládají v účtu úložiště Azure ve vašem pracovním prostoru. Image se vytvoří a uloží v Azure Container Registry. Při vytváření bitové kopie, které jsou uloženy v registru image a může být dotazována k vyhledání bitové kopie můžete zadat další metadata značky.

## <a name="step-3-deployment"></a>Krok 3: nasazení

Registrovaný imagí můžete nasadit do cloudu nebo na hraničních zařízeních. Proces nasazení vytvoří všechny prostředky potřebné k monitorování, Vyrovnávání zatížení a automatické škálování vašeho modelu. Přístup k nasazeným službám dá se zabezpečit s ověřováním na základě certifikátů tím, že poskytuje prostředky zabezpečení během nasazení. Rovněž lze upgradovat stávajícího nasazení použít novější image.

Nasazení webové služby je také možné prohledávat. Můžete například vyhledat všechny nasazení konkrétní model nebo image.

Image můžete nasadit do těchto cílů v cloudu:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Azure počítače FPGA
* Zařízení Azure IoT Edge

[Další informace o kde můžete nasadit](how-to-deploy-and-where.md).

Jak je vaše služba nasazená, odvozování požadavku je automaticky s vyrovnáváním zatížení a splňovat všechny špičky na vyžádání je škálování clusteru. Ve službě Azure Application Insights přidružený k vaší pracovní prostor se zaznamenávají telemetrická data o vaší služby.

## <a name="step-4-monitoring-models-and-data-collection"></a>Krok 4: Monitorování modely a shromažďování dat

Sada SDK pro zachycení protokolování a datový model je k dispozici, takže můžete monitorovat vstup, výstup a další relevantní data z vašeho modelu. Data se ukládají jako objekty blob v účtu úložiště Azure pro váš pracovní prostor.

Použití sady SDK obsahující váš model, importujete sady SDK do hodnoticího skriptu nebo aplikace. Sada SDK pak můžete protokolovat data, jako jsou parametry, výsledky nebo podrobnosti vstupu.

Pokud se rozhodnete [Povolit shromažďování dat modelu](how-to-enable-data-collection.md) pokaždé, když nasazujete bitovou kopii, jsou automaticky zřídí informace potřebné k zaznamenání dat, jako je například přihlašovací údaje k úložišti objektů blob v osobní.

> [!Important]
> Microsoft nezobrazují dat shromážděných z vašeho modelu. Data se odesílají přímo do účtu úložiště Azure pro váš pracovní prostor.

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning.
