---
title: Co se stalo s aplikací Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning je integrované řešení pro datové vědy, které slouží k modelování a nasazování aplikací ML v cloudovém měřítku. Funkce Workbench byla vyřazena.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: c800901ae139b16e75613b0cc8de3a9485186896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521369"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co se stalo s aplikací Azure Machine Learning Workbench?

Aplikace Azure Machine Learning Workbench a některé další úvodní funkce byly zastaralé a nahrazené vydáním **září 2018** , aby bylo možné zlepšit [architekturu](concept-azure-machine-learning-architecture.md).

Pro zlepšení prostředí vydaná verze obsahuje mnoho důležitých aktualizací, které se na základě zpětné vazby od zákazníků zobrazí. Základní funkce z experimentu se spouští do nasazení modelu se nezměnily. Teď ale můžete pomocí robustní <a href="/python/api/overview/azure/ml/intro" target="_blank">sady Python SDK</a>, R SDK a [Azure CLI](reference-azure-machine-learning-cli.md) provádět úlohy a kanály pro strojové učení.

Většina artefaktů, které byly vytvořeny ve starší verzi Azure Machine Learning, jsou uloženy ve vašem místním nebo cloudovém úložišti. Tyto artefakty se vám nikdy neztratí.

V tomto článku se dozvíte, co se změnilo a jak ovlivňuje stávající práci s Azure Machine Learning Workbench a jeho rozhraními API.

>[!Warning]
>Tento článek není pro uživatele Azure Machine Learning Studio. Je určena pro Azure Machine Learning zákazníky, kteří mají nainstalovanou aplikaci Workbench (Preview) nebo mají účty Preview a správy modelů.


## <a name="what-changed"></a>Co se změnilo?

Nejnovější vydaná verze Azure Machine Learning obsahuje následující funkce:
+ [Zjednodušený model prostředků Azure](concept-azure-machine-learning-architecture.md).
+ [Nové uživatelské rozhraní portálu](how-to-track-experiments.md) pro správu experimentů a cílů výpočtů.
+ Nová, komplexnější <a href="/python/api/overview/azure/ml/intro" target="_blank">sada Python SDK</a>.
+ Nové rozšířené [rozšíření Azure CLI](reference-azure-machine-learning-cli.md) pro Machine Learning.

[Architektura](concept-azure-machine-learning-architecture.md) byla přepracována pro snadné použití. Místo několika prostředků a účtů Azure potřebujete jenom [pracovní prostor služby Azure Machine Learning](concept-workspace.md). Pracovní prostory můžete vytvářet rychle na webu [Azure Portal](how-to-manage-workspace.md). Když použijete pracovní prostor, více uživatelů může ukládat výpočetní a implementační cíle, modelové experimenty, image Docker, nasazené modely atd.

I když v aktuální verzi existují nové vylepšené klienty rozhraní CLI a sady SDK, aplikace Desktop Workbench byla vyřazena. Experimenty se dají spravovat na [řídicím panelu pracovního prostoru v Azure Machine Learning Studiu](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal). Řídicí panel můžete použít k zobrazení historie experimentu, ke správě výpočetních cílů připojených k pracovnímu prostoru, ke správě modelů a imagí Dockeru, a dokonce i k nasazení webových služeb.

<a name="timeline"></a>

## <a name="support-timeline"></a>Časový plán podpory

9. ledna 2019 podpora pro Machine Learning Workbench, účty Experimentování ve službě Azure Machine Learning a Správa modelů a jejich přidružená sada SDK a rozhraní příkazového řádku se ukončila.

Všechny nejnovější možnosti jsou k dispozici pomocí této <a href="/python/api/overview/azure/ml/intro" target="_blank">sady SDK</a>, rozhraní příkazového [řádku](reference-azure-machine-learning-cli.md)a [portálu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Co se stane s historií spuštění?

Starší historie spuštění už nejsou dostupné, jak se vaše běhy pořád zobrazují v nejnovější verzi.

Historie spuštění se teď nazývají **experimenty**. Můžete shromáždit experimenty modelu a prozkoumat je pomocí sady SDK, rozhraní příkazového řádku nebo Azure Machine Learning studia.

Řídicí panel pracovního prostoru na portálu se podporuje jenom v prohlížečích Microsoft Edge, Chrome a Firefox:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Spusťte školení modelů a sledování historií spuštění pomocí nových rozhraní příkazového řádku a sady SDK. Postup najdete v tématu [výuka modelů pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Vydrží mi moje projekty?

Neztratíte žádný kód ani práci. Ve starší verzi jsou projekty cloudové entity s místním adresářem. V nejnovější verzi se místní adresáře připojovat k Azure Machine Learningmu pracovnímu prostoru pomocí místního konfiguračního souboru. Podívejte se na [diagram nejnovější architektury](concept-azure-machine-learning-architecture.md).

Většina obsahu projektu již byla na vašem místním počítači. Takže stačí vytvořit konfigurační soubor v tomto adresáři a odkázat ho na něj v kódu, abyste se připojili k vašemu pracovnímu prostoru. Chcete-li pokračovat v používání místního adresáře, který obsahuje soubory a skripty, zadejte název adresáře v příkazu ["experiment. Submit"](/python/api/azureml-core/azureml.core.experiment.experiment) v Pythonu nebo pomocí `az ml project attach` příkazu CLI.  Například:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Vytvořte pracovní prostor](how-to-manage-workspace.md) , abyste mohli začít.

## <a name="what-about-my-registered-models-and-images"></a>K čemu se zaregistrovaly modely a image?

Modely, které jste zaregistrovali ve starém registru modelu, musí být migrovány do nového pracovního prostoru, pokud je chcete dále používat. Pokud chcete migrovat modely, Stáhněte si modely a znovu je Zaregistrujte v novém pracovním prostoru.

Image, které jste vytvořili v původním registru imagí, nejde přímo migrovat do nového pracovního prostoru. Ve většině případů je možné model nasadit bez nutnosti vytvářet image. V případě potřeby můžete vytvořit obrázek pro model v novém pracovním prostoru. Další informace najdete v tématu [Správa, registrace, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co se stane s nasazenými webovými službami?

Teď, když už skončila podpora pro staré rozhraní příkazového řádku, už nebudete moct znovu nasazovat modely ani spravovat webové služby, které jste původně nasadili s vaším účtem Správa modelů. Tyto webové služby budou ale i nadále fungovat, dokud je podpora Azure Container Service (ACS) stále podporovaná.

V nejnovější verzi se modely nasazují jako webové služby na Azure Container Instances (ACI) nebo clustery Azure Kubernetes Service (AKS). Můžete také nasadit na FPGA a Azure IoT Edge.

Další informace najdete v těchto článcích:
+ [Kde a jak nasadit modely](how-to-deploy-and-where.md)
+ [Kurz: nasazení modelů pomocí Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Další kroky

Seznamte se s [nejnovější architekturou pro Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Pokud chcete zobrazit přehled služby, přečtěte si téma [co je Azure Machine Learning?](overview-what-is-azure-ml.md).

Vytvořte svůj první experiment s upřednostňovanou metodou:

  + [Použití vlastního prostředí](tutorial-1st-experiment-sdk-setup-local.md)
  + [Použití poznámkových bloků Pythonu](tutorial-1st-experiment-sdk-setup.md)
  + [Použít R Markdown](https://github.com/Azure/azureml-sdk-for-r) 
  + [Použití automatizovaného strojového učení](tutorial-designer-automobile-price-train-score.md) 
  + [Použití možností přetažení &ho návrháře](tutorial-first-experiment-automated-ml.md) 
  + [Použít rozšíření ML pro rozhraní příkazového řádku](tutorial-train-deploy-model-cli.md)
