---
title: Migrace do služby Azure Machine Learning
description: Zjistěte, jak upgradovat nebo migrovat ze starší verze pozdní verze služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 9778d348cf49d4066f034931dc350a1f4a608ad2
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236580"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Migrace na nejnovější verzi služby Azure Machine Learning 

**Pokud jste nainstalovali aplikaci Workbench (preview) a/nebo mají experimentování a správa modelů ve verzi preview účty, pomocí tohoto článku můžete migrovat na nejnovější verzi.**  Pokud nemáte k dispozici ve verzi preview Workbench nainstalovaná, nebo experimentování a/nebo účet služby Správa modelů, není potřeba cokoli migrovat.

## <a name="what-can-i-migrate"></a>Co mohu migrovat?
Většiny artefaktů, které jsou vytvořené v první verze preview služby Azure Machine Learning se ukládají do vlastní místní nebo cloudové úložiště. Tyto artefakty nebude zmizí. Pokud chcete migrovat, znovu zaregistrujte artefakty, které se aktualizace služby Azure Machine Learning. 

Následující tabulka a článek vysvětluje, co můžete dělat s vašimi stávajícími prostředky a prostředky před nebo po přesunem na nejnovější verzi služby Azure Machine Learning. Můžete také dále nechystáte nějakou dobu používat předchozí verzi a vaše prostředky ([naleznete v tématu přechod časová osa podpory](overview-what-happened-to-workbench.md#timeline)).

|Prostředek nebo prostředek ze starší verze|Můžu migrovat?|Akce|
|-----------------|:-------------:|-------------|
|Strojové učení modelů (jako místní soubory)|Ano|Žádné. Funguje stejně jako předtím.|
|Model závislosti & schémata (jako místní soubory)|Ano|Žádné. Funguje stejně jako předtím.|
|Projekty|Ano|[Připojení místní složce na nový pracovní prostor](#projects).|
|Historie spuštění|Ne|[Ke stažení](#history) nějakou dobu.|
|Soubory pro přípravu dat|Ne|[Příprava sady dat libovolné velikosti](#dataprep) modelování pomocí nové Azure Machine Learning Prep sady SDK pro Data nebo pomocí Azure Databricks.|
|Cílových výpočetních prostředí|Ne|Jejich registrace v nový pracovní prostor.|
|Registrované modely|Ne|Opětovná registrace modelu za nový pracovní prostor.|
|Registrované manifestů|Ne|Žádné. Manifesty jako koncept v nový pracovní prostor už neexistuje.|
|Registrovaný imagí|Ne|Znovu vytvořte image Dockeru nasazení v rámci nový pracovní prostor.|
|Nasazené webové služby|Ne|Žádné. Budete i nadále fungovat jako-je <br/>nebo [nasadit je znovu pomocí nejnovější verze](#services).|
|Experimentování a <br/>Účty správy modelů|Ne|[Vytvoření pracovního prostoru](#resources) místo.|
|Strojové učení, rozhraní příkazového řádku a sady SDK|Ne|Pomocí nové [rozhraní příkazového řádku](reference-azure-machine-learning-cli.md) a [SDK](http://aka.ms/aml-sdk) pro novou práci.|


Další informace o [co se změnilo v této verzi](overview-what-happened-to-workbench.md)?

>[!Warning]
>Tento článek není pro uživatele Azure Machine Learning Studio. Je pro zákazníky služby Azure Machine Learning, kteří mají nainstalované aplikace Workbench (preview) a/nebo mají experimentování a ve verzi preview účty správy modelů.

<a name="resources"></a>

## <a name="azure-resources"></a>Prostředky Azure

Prostředí nejde migrovat na nejnovější verzi služby Azure Machine Learning výpočetní prostředky, jako jsou účty služby experimentování ve službě, účty správy modelů a machine learningu. Zobrazit [časová osa](overview-what-happened-to-workbench.md#timeline) na jak dlouho by vaše prostředky budou nadále fungovat.

Začínáme s nejnovější verzí tím, že vytvoříte pracovní prostor služby Azure Machine Learning service v [webu Azure portal](quickstart-get-started.md). Řídicí panel pracovního prostoru na portálu se podporuje jenom prohlížeče Edge, Chrome a Firefox.

Tento pracovní prostor je prostředek nejvyšší úrovně služby a umožňuje využívat všechny nejnovější funkce služby Azure Machine Learning. Další informace o tomto [pracovní prostor a architektura](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projekty

Namísto toho, aby vaše projekty v pracovním prostoru v cloudu, projekty jsou nyní adresáře na místním počítači v nejnovější verzi. Zobrazit diagram [nejnovější architektura](concept-azure-machine-learning-architecture.md). 

Dál používat místní adresář obsahující soubory a skripty, zadejte název adresáře ["experiment.submit"](http://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python příkaz nebo pomocí příkazu az ml projektu připojení rozhraní příkazového řádku.

Příklad:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Nasazené webové služby

Migrace webových služeb, znovu nasadíte modely do nového cíle nasazení pomocí nové sady SDK nebo rozhraní příkazového řádku. Není nutné změny původní soubor vyhodnocení, model závislosti souborů, souboru prostředí a soubory schémat. 

V nejnovější verzi, jsou nasazené modely jako webové služby do [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) nebo [Azure Kubernetes Service](how-to-deploy-to-aks.md) clusterů (AKS). 

Další informace najdete v těchto článcích:
+ [Nasazení do služby ACI](how-to-deploy-to-aci.md)
+ [Nasazení do AKS](how-to-deploy-to-aks.md)
+ [Kurz: Nasazení modelů pomocí služby Azure Machine Learning](tutorial-deploy-models-with-aml.md)

Když [odborné pomoci pro předchozí konců rozhraní příkazového řádku](overview-what-happened-to-workbench.md#timeline), nebudete moci spravovat webové služby původně nasazené pomocí svého účtu správy modelů. Tyto webové služby však bude i nadále fungovat tak dlouho, dokud je Azure Container Service (ACS) stále podporovány.

<a name="history"></a>

## <a name="run-history-records"></a>Záznamy historie spuštění

Zatímco nelze přidat do existující historie spuštění v rámci původní pracovní prostor, můžete to taky historie, je nutné používat předchozí rozhraní příkazového řádku. Když [odborné pomoci pro předchozí konců rozhraní příkazového řádku](overview-what-happened-to-workbench.md#timeline), nebudete moct exportovat už tyto historie spuštění.

Začněte trénování modelů a sledování historie spuštění pomocí nového rozhraní příkazového řádku a sady SDK. Zjistíte, jak se [kurz: trénování modelů pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).

Export historie spuštění s předchozím rozhraní příkazového řádku:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Příprava souborů dat
Příprava souborů dat nepřenosných bez aplikace Workbench. Ale můžete pořád Příprava modelování, pomocí nové Azure Machine Learning Prep sady SDK pro Data libovolné velikosti datové sady nebo pomocí Azure Databricks pro velkých datových sad.  [Další informace o získání přípravy dat SDK](how-to-data-prep.md). 

## <a name="next-steps"></a>Další postup

Rychlý start ukazuje, jak vytvořit pracovní prostor, vytvoření projektu, spusťte skript a prozkoumejte historii spuštění tohoto skriptu s nejnovější verzí služby Azure Machine Learning, vyzkoušejte [Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

Podrobnější poznatky o tomto pracovním postupu postupujte podle komplexně kurz, který obsahuje podrobné návody k trénování a nasazení modelů pomocí služby Azure Machine Learning. 

> [!div class="nextstepaction"]
> [Kurz: Trénovat a nasazovat modely](tutorial-train-models-with-aml.md)
