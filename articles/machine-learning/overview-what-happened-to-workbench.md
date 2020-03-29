---
title: Co se stalo s aplikací Workbench?
titleSuffix: Azure Machine Learning
description: Zjistěte, co je možné bez aplikace Workbench a jaká je časová osa podpory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 03/05/2020
ms.openlocfilehash: 9b49eb6237346a76903202a118331383c5a8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944259"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co se stalo s aplikací Azure Machine Learning Workbench?

Aplikace Azure Machine Learning Workbench a některé další rané funkce byly zastaralé a nahrazeny ve verzi **ze září 2018,** aby se uvolnilo místo pro vylepšenou [architekturu](concept-azure-machine-learning-architecture.md).

Chcete-li zlepšit vaše zkušenosti, vydání obsahuje mnoho významných aktualizací, které jsou vyvolány zpětnou vazbou od zákazníků. Základní funkce od spuštění experimentu až po nasazení modelu se nezměnily. Ale teď můžete použít robustní <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a>, R SDK a [Azure CLI](reference-azure-machine-learning-cli.md) k provádění úloh strojového učení a kanály.

Většina artefaktů, které byly vytvořeny v dřívější verzi Azure Machine Learning jsou uloženy ve vašem vlastním místním nebo cloudovém úložišti. Tyto artefakty se vám nikdy neztratí.

V tomto článku se dozvíte, co se změnilo a jak ovlivňuje vaši již existující práci s Pracovní mašlí Azure Machine Learning workbench a jeho api.

>[!Warning]
>Tento článek není pro uživatele Azure Machine Learning Studio. Je to pro zákazníky Azure Machine Learning, kteří nainstalovali aplikaci Workbench (preview) nebo mají experimentování a účty pro správu modelu preview.


## <a name="what-changed"></a>Co se změnilo?

Nejnovější verze Azure Machine Learning obsahuje následující funkce:
+ [Zjednodušený model prostředků Azure](concept-azure-machine-learning-architecture.md).
+ [Nové portálové ui](how-to-track-experiments.md) pro správu experimentů a výpočetních cílů.
+ Nová, komplexnější sada Python <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>.
+ Nové rozšířené [rozšíření Azure CLI](reference-azure-machine-learning-cli.md) pro strojové učení.

[Architektura](concept-azure-machine-learning-architecture.md) byla přepracována pro snadné použití. Místo několika prostředků a účtů Azure potřebujete jenom [pracovní prostor služby Azure Machine Learning](concept-workspace.md). Pracovní prostory můžete vytvářet rychle na webu [Azure Portal](how-to-manage-workspace.md). Pomocí pracovního prostoru může více uživatelů ukládat výpočetní cíle školení a nasazení, experimenty s modely, image Dockeru, nasazené modely a tak dále.

Přestože v aktuální verzi jsou noví vylepšení klienti CLI a SDK, samotná desktopová aplikace workbench byla vyřazena. Experimenty lze spravovat na [řídicím panelu pracovního prostoru ve studiu Azure Machine Learning](how-to-track-experiments.md#view-the-experiment-in-the-web-portal). Řídicí panel můžete použít k zobrazení historie experimentu, ke správě výpočetních cílů připojených k pracovnímu prostoru, ke správě modelů a imagí Dockeru, a dokonce i k nasazení webových služeb.

<a name="timeline"></a>

## <a name="support-timeline"></a>Časový plán podpory

9. ledna 2019 skončila podpora pro pracovní plocha machine learningu, účty Azure Machine Learning Experimentation a Model Management a jejich přidružené sady SDK a CLI.

Všechny nejnovější funkce jsou k dispozici pomocí této <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sady SDK</a>, [rozhraní příkazového příkazu](reference-azure-machine-learning-cli.md)a [portálu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Co se stane s historií spuštění?

Starší historie spuštění již nejsou přístupné, jak můžete stále vidět vaše běží v nejnovější verzi.

Historie spouštění se nyní nazývají **experimenty**. Experimenty modelu můžete shromažďovat a zkoumat je pomocí sady SDK, CLI nebo studia Azure Machine Learning.

Řídicí panel pracovního prostoru portálu je podporován pouze v prohlížečích Microsoft Edge, Chrome a Firefox:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Začněte trénovat své modely a sledování historie spuštění pomocí nového cli a sady SDK. Můžete se dozvědět, jak s [tutorial: trénování modely s Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Vydrží mi moje projekty?

Neztratíte žádný kód ani práci. Ve starší verzi jsou projekty cloudové entity s místním adresářem. V nejnovější verzi připojíte místní adresáře k pracovnímu prostoru Azure Machine Learning pomocí místního konfiguračního souboru. Podívejte se na [diagram nejnovější architektury](concept-azure-machine-learning-architecture.md).

Velká část obsahu projektu již byla v místním počítači. Takže stačí vytvořit konfigurační soubor v tomto adresáři a odkazovat na něj v kódu pro připojení k pracovnímu prostoru. Chcete-li pokračovat v používání místního adresáře obsahujícího soubory a skripty, zadejte název `az ml project attach` adresáře v příkazu [Pythonu experiment.submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) nebo pomocí příkazu PŘÍKAZU příkazu CLI.  Například:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Vytvořte pracovní prostor,](how-to-manage-workspace.md) abyste mohli začít.

## <a name="what-about-my-registered-models-and-images"></a>A co moje registrované modely a obrázky?

Modely, které jste zaregistrovali ve starém registru modelu, musí být migrovány do nového pracovního prostoru, pokud je chcete nadále používat. Chcete-li migrovat modely, stáhněte si modely a znovu je zaregistrujte do nového pracovního prostoru.

Bitové kopie, které jste vytvořili ve starém registru bitových kopií, nelze přímo migrovat do nového pracovního prostoru. Ve většině případů lze model nasadit bez nutnosti vytvářet bitovou kopii. V případě potřeby můžete vytvořit obraz pro model v novém pracovním prostoru. Další informace naleznete v [tématu Správa, registrace, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co se stane s nasazenými webovými službami?

Nyní, když byla ukončena podpora starého uživatelského účtu, již nelze znovu nasadit modely nebo spravovat webové služby, které jste původně nasadili pomocí účtu správy modelů. Tyto webové služby však budou nadále fungovat tak dlouho, dokud je služba Azure Container Service (ACS) stále podporována.

V nejnovější verzi se modely nasazují jako webové služby do clusterů Azure Container Instances (ACI) nebo Azure Kubernetes Service (AKS). Můžete také nasadit do FPGA a Azure IoT Edge.

Další informace naleznete v těchto článcích:
+ [Kde a jak nasadit modely](how-to-deploy-and-where.md)
+ [Kurz: Nasazení modelů pomocí Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Další kroky

Seznamte se s [nejnovější architekturou pro Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Přehled služby najdete v článku [Co je Azure Machine Learning?](overview-what-is-azure-ml.md).

Vytvořte svůj první experiment s preferovanou metodou:
  + [Použití poznámkových bloků Pythonu](tutorial-1st-experiment-sdk-setup.md)
  + [Použít R Markdown](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení](tutorial-designer-automobile-price-train-score.md) 
  + [Použití možností přetahování & návrháře](tutorial-first-experiment-automated-ml.md) 
  + [Použití rozšíření ML pro cli](tutorial-train-deploy-model-cli.md)
