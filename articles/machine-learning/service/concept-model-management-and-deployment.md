---
title: Spravovat a registrovat, nasadit a monitorovat modelů ML
titleSuffix: Azure Machine Learning service
description: Další informace o použití služby Azure Machine Learning nasazovat, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí služby Azure Machine Learning, na místním počítači nebo z jiných zdrojů.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 467af0f04708c9c6758531fb1cd71d79e9ddd6d7
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302965"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Spravovat, nasazovat a sledovat modely pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak pomocí služby Azure Machine Learning nasadit, spravovat a monitorovat vlastní modely a neustále je vylepšovat. Můžete nasadit modely, které natrénovaný pomocí Azure Machine Learning na místním počítači nebo z jiných zdrojů. 

Následující diagram znázorňuje kompletního nasazení pracovního postupu: [ ![Pracovní postup nasazení pro Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Pracovní postup nasazení zahrnuje následující kroky:
1. **Zaregistrujte model** v registru hostované ve vašem pracovním prostoru služby Azure Machine Learning
1. **Zaregistrovat image** , který dvojice model s hodnoticí skript a závislosti v přenosných kontejneru 
1. **Nasazení** image jako webovou službu v cloudu nebo do hraničních zařízení
1. **Monitorování a shromažďování dat**
1. **Aktualizace** nasazení používat novou bitovou kopii.

Každý krok lze provést, samostatně nebo jako součást příkazu pro jedno nasazení. Kromě toho můžete integrovat do nasazení **pracovních postupů CI/CD** jak je znázorněno na tomto obrázku.

[ !["Služba azure Machine Learning průběžné integrace a nasazování (CI/CD) cyklu.](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Krok 1: Registrace modelu

Registrace modelu umožňuje ukládat a verzí vašich modelů v cloudu Azure, ve vašem pracovním prostoru. Model registru usnadňuje uspořádat a sledovat, trénované modely.
 
Registrované modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky. Služba Azure Machine Learning podporuje modely uložit pomocí jakéhokoli modelu, který lze načíst pomocí Python 3. 

Nelze odstranit modely, které se používají v obrázku.

Další informace najdete v části registrace modelu z [nasazovat modely](how-to-deploy-and-where.md#registermodel).

Příklad registrace uložené ve formátu pickle modelu naleznete v tématu [kurzu: Trénování modelu klasifikace obrázků](tutorial-deploy-models-with-aml.md).

Informace o používání modely ONNX, najdete v článku [ONNX a Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentu.

## <a name="step-2-register-image"></a>Krok 2: Zaregistrovat image

Bitové kopie umožňují spolehlivé model nasazení, spolu s všechny součásti potřebné k použití modelu. Bitová kopie obsahuje následující položky:

* Model
* Modul vyhodnocení
* Hodnoticí souborů nebo aplikace
* Všechny závislosti potřebné k určení skóre modelu

Image můžete také zahrnout součásti sady SDK pro protokolování a monitorování. Protokoly data sady SDK slouží k vyladění nebo přeučování modelu, včetně vstup a výstup modelu.

Azure Machine Learning podporuje oblíbených architektur, ale obecně můžete pracovat libovolné architektury, které mohou být nainstalovány nástrojem pip.

Při vytvoření pracovního prostoru, takže ostatní několik dalších prostředků Azure použil tento pracovní prostor.
Všechny objekty používané k vytvoření této image se ukládají v účtu úložiště Azure ve vašem pracovním prostoru. Image se vytvoří a uloží v Azure Container Registry. Při vytváření bitové kopie, které jsou uloženy v registru image a může být dotazována k vyhledání bitové kopie můžete zadat další metadata značky.

Další informace naleznete v tématu Konfigurace a registrace oddíl bitové kopie z [nasazovat modely](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Krok 3: Nasazení bitové kopie

Registrovaný imagí můžete nasadit do cloudu nebo na hraničních zařízeních. Proces nasazení vytvoří všechny prostředky potřebné k monitorování, Vyrovnávání zatížení a automatické škálování vašeho modelu. Přístup k nasazeným službám dá se zabezpečit s ověřováním na základě certifikátů tím, že poskytuje prostředky zabezpečení během nasazení. Rovněž lze upgradovat stávajícího nasazení použít novější image.

Nasazení webové služby je také možné prohledávat. Můžete například vyhledat všechny nasazení konkrétní model nebo image.

[ ![Odvozování cíle](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Image můžete nasadit do těchto cílů nasazení v cloudu:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Azure počítače FPGA
* Zařízení Azure IoT Edge

Jak je vaše služba nasazená, odvozování požadavku je automaticky s vyrovnáváním zatížení a splňovat všechny špičky na vyžádání je škálování clusteru. [Telemetrická data o vaší služby se dají zachytit](how-to-enable-app-insights.md) ve službě Azure Application Insights přidružený k pracovního prostoru.

Další informace najdete v tématu v části nasazení [nasazovat modely](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Krok 4: Modely monitorování a shromažďování dat

Sada SDK pro zachycení protokolování a datový model je k dispozici, takže můžete monitorovat vstup, výstup a další relevantní data z vašeho modelu. Data se ukládají jako objekty blob v účtu úložiště Azure pro váš pracovní prostor.

Použití sady SDK obsahující váš model, importujete sady SDK do hodnoticího skriptu nebo aplikace. Sada SDK pak můžete protokolovat data, jako jsou parametry, výsledky nebo podrobnosti vstupu.

Pokud se rozhodnete povolit shromažďování dat modelu pokaždé, když nasazujete bitovou kopii, jsou automaticky zřídí informace potřebné k zaznamenání dat, jako je například přihlašovací údaje k úložišti objektů blob v osobní.

> [!Important]
> Microsoft nezobrazují dat shromážděných z vašeho modelu. Data se odesílají přímo do účtu úložiště Azure pro váš pracovní prostor.

Další informace najdete v tématu [povolení shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Krok 5: Aktualizace nasazení

Aktualizace modelu se zaregistruje automaticky. Podobně registrace novou bitovou kopii se neaktualizuje automaticky nasazení, které byly vytvořeny z předchozí verze Image. Místo toho musí ručně zaregistrujte model, zaregistrujte image a pak aktualizujte modelu. Další informace najdete v tématu aktualizovat část [nasazovat modely](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning.

Další informace o vytváření klientských aplikací a služeb, které [využívat model nasadit jako webovou službu](how-to-consume-web-service.md).
