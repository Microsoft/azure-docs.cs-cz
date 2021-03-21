---
title: Open Source Integration pro Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se používat Open Source rozhraní pro strojové učení Pythonu k výuce, nasazování a správě komplexních řešení strojového učení v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223070"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integrace open source s Azure Machine Learning projekty

Proces strojového učení v Azure Machine Learning můžete pomocí Open Source knihoven a platforem strojového učení v Pythonu vyškolit, nasadit a spravovat.  Pomocí vývojářských nástrojů, jako jsou Jupyter notebooky a Visual Studio Code, můžete využívat stávající modely a skripty v Azure Machine Learning.  

V tomto článku se dozvíte víc o těchto Open Source knihovnách a platformách.

## <a name="train-open-source-machine-learning-models"></a>Výuka Open Source modelů strojového učení

Proces výuky Machine Learning zahrnuje použití algoritmů pro vaše data, aby bylo možné provést úlohu nebo vyřešit problém. V závislosti na problému můžete zvolit různé algoritmy, které nejlépe vyhovují vašim úlohám a datům. Další informace o různých větvích strojového učení najdete v článku o [podrobném učení vs Machine Learning](./concept-deep-learning-vs-machine-learning.md) a na [listu tahák s algoritmem strojového učení](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Zachování ochrany soukromí dat pomocí rozdílového soukromí

K výuce modelu strojového učení potřebujete data. Někdy jsou data citlivá a je důležité zajistit, aby byla data zabezpečená a soukromá. Rozdílová ochrana osobních údajů je technika zachování důvěrnosti informací v datové sadě. Další informace najdete v článku o [zachování ochrany osobních údajů v datech](concept-differential-privacy.md). 

Pomocí Open Source rozdílových nástrojů pro ochranu osobních údajů, jako je [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) , můžete chránit [soukromí dat](how-to-differential-privacy.md) v Azure Machine Learning řešení.

### <a name="classical-machine-learning-scikit-learn"></a>Klasický Machine Learning: scikit – informace

V případě školicích úloh týkajících se klasických algoritmů strojového učení, jako je klasifikace, clusteringu a regrese, je možné použít něco jako Scikit-učit se. Další informace o tom, jak se naučit model klasifikace květu, najdete v [článku Postup výuky pomocí Scikit](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Neuronové sítě: PyTorch, TensorFlow, Keras

Open Source algoritmy strojového učení známé jako neuronové Networks, což je podmnožina strojového učení, je užitečná pro školení modelů pro obsáhlý Learning v Azure Machine Learning.

Mezi open source architektury hloubkového učení a návody patří:

 *  [PyTorch](https://github.com/pytorch/pytorch): [školení modelu klasifikace imagí s hloubkovým učením pomocí učení přenosu](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [rozpoznávání ručně psaných číslic pomocí TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [vytvoření sítě neuronové pro analýzu imagí pomocí Keras](how-to-train-keras.md)

Školení modelu hloubkového učení od začátku často vyžaduje velké množství času, dat a výpočetních prostředků. Školicí proces můžete zástupcem pomocí učení přenosu. Učení přenosu je technika, která používá znalostní bázi získanou z řešení jednoho problému na jiný, ale související problém. To znamená, že můžete přebírat existující model pro použití. Další informace o učení pro přenos najdete v [článku o podrobném učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) .

### <a name="reinforcement-learning-ray-rllib"></a>Posílení učení: Ray RLLib

Posílení učení je umělá technika, která při používání akcí, států a využívání vede k navýšení analytických agentů. díky posílení studijních agentů se naučíte, jak provést sadu předdefinovaných akcí, které maximalizují určité ceny na základě aktuálního stavu jejich prostředí. 

Projekt [Ray RLLib](https://github.com/ray-project/ray) obsahuje sadu funkcí, které umožňují vysokou škálovatelnost během procesu školení. Iterativní proces je náročný na čas i prostředky, protože agenti učení pro posílení se snaží zjistit optimální způsob dosažení úkolu.  Ray RLLib také nativně podporuje architektury pro hloubkové učení, jako je TensorFlow a PyTorch.  

Pokud se chcete dozvědět, jak používat Ray RLLib s Azure Machine Learning, přečtěte si téma [Postup výuky výukového modelu](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Monitorování výkonu modelu: TensorBoard

Školení jednoho nebo více modelů vyžaduje vizualizaci a kontrolu požadovaných metrik, aby bylo zajištěno, že model funguje podle očekávání. TensorBoard můžete [použít v Azure Machine Learning ke sledování a vizualizaci metrik experimentu](./how-to-monitor-tensorboard.md) .

### <a name="frameworks-for-interpretable-and-fair-models"></a>Rozhraní pro interpretované a spravedlivé modely

Systémy strojového učení se používají v různých oblastech společnosti, jako jsou bankovnictví, vzdělávání a zdravotnictví. V takovém případě je důležité, aby tyto systémy platily pro předpovědi a doporučení, aby nedocházelo k nezamýšleným důsledkům.

Open Source architektury, jako je [InterpretML](https://github.com/interpretml/interpret/) a Fairlearn ( https://github.com/fairlearn/fairlearn) práce s Azure Machine Learning k vytváření transparentních a spravedlivéch modelů strojového učení.

Další informace o tom, jak vytvořit spravedlivé a interpretované modely, najdete v následujících článcích:

- [Interpretovatelnost modelů ve službě Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretace a vysvětlení modelů strojového učení](how-to-machine-learning-interpretability-aml.md)
- [Vysvětlete modely AutoML](how-to-machine-learning-interpretability-automl.md)
- [Zmírnění rovnosti v modelech strojového učení](concept-fairness-ml.md)
- [Použití Azure Machine Learning k nespravedlivosti modelu prostředků](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Nasazení modelu

Po vyškolení modelů a jejich přípravě na produkční prostředí je nutné zvolit způsob nasazení. Azure Machine Learning poskytuje různé cíle nasazení. Další informace naleznete v [článku WHERE a postup nasazení](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Standardizace formátů modelu pomocí ONNX

Po školení se obsah modelu, jako je naučené parametry, serializovat a uloží do souboru. Každé rozhraní má svůj vlastní formát serializace. Při práci s různými architekturami a nástroji to znamená, že je nutné nasadit modely podle požadavků rozhraní. Pro standardizaci tohoto procesu můžete použít formát Open neuronové Network Exchange (ONNX). ONNX je open source formát pro umělou analýzu modelů. ONNX podporuje interoperabilitu mezi platformami. To znamená, že můžete model vytvořit v jedné z mnoha oblíbených rozhraní pro strojové učení, jako je PyTorch, převést ho do formátu ONNX a spotřebovat model ONNX v jiném rozhraní jako ML.NET.

Další informace o ONNX a o tom, jak využívat modely ONNX, najdete v následujících článcích:

- [Vytváření a zrychlení modelů ML pomocí ONNX](concept-onnx.md)
- [Použití modelů ONNX v aplikacích .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Zabalení a nasazení modelů jako kontejnerů

Technologie kontejnerů, jako je Docker, jsou jedním ze způsobů, jak nasadit modely jako webové služby. Kontejnery poskytují platformu a nezávislá způsob, jak vytvářet a orchestrovat reprodukovatelná softwarová prostředí. S těmito základními technologiemi můžete k nasazení modelů strojového učení do [clusterů Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python)použít [předkonfigurovaná prostředí](./how-to-use-environments.md), [předem nakonfigurované image kontejneru](./how-to-deploy-custom-docker-image.md) nebo vlastní. Pro pracovní postupy náročné na grafické procesory můžete použít nástroje, jako je Triton odvození serveru NVIDIA, a [vytvořit předpovědi pomocí GPU](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Zabezpečená nasazení pomocí šifrování homomorphic

Zabezpečení nasazení je důležitou součástí procesu nasazení. Pokud chcete [nasadit šifrované služby Inferencing](how-to-homomorphic-encryption-seal.md), použijte `encrypted-inference` Open Source knihovnu Python. `encrypted inferencing`Balíček poskytuje vazby založené na [Microsoft Seal](https://github.com/Microsoft/SEAL), knihovně šifrování homomorphic.

## <a name="machine-learning-operations-mlops"></a>Operace Machine Learning (MLOps)

Machine Learning operací (MLOps), které se běžně domnívaly jako DevOps pro Machine Learning, vám umožní vytvářet komplexnější, odolné a reprodukovatelné pracovní postupy strojového učení. Další informace o MLOps najdete v [článku Co je MLOps](./concept-model-management-and-deployment.md) . 

Díky postupům DevOps, jako je kontinuální integrace (CI) a průběžné nasazování (CD), můžete automatizovat kompletní životní cyklus strojového učení a zachytit data zásad správného řízení. Můžete definovat svůj [kanál CI/CD Machine Learningu v akcích GitHubu](./how-to-github-actions-machine-learning.md) pro spouštění Azure Machine Learningch úloh školení a nasazení. 

Zachytávání závislostí softwaru, metrik, metadat, dat a modelů verzí je důležitou součástí procesu MLOps, aby bylo možné vytvářet transparentní, reprodukovatelnější a auditované kanály. Pro tuto úlohu můžete [použít MLFlow v Azure Machine Learning](how-to-use-mlflow.md) a také při [výuce modelů strojového učení v Azure Databricks](./how-to-use-mlflow-azure-databricks.md). [Modely MLflow můžete nasadit také jako webovou službu Azure](how-to-deploy-mlflow-models.md). 
