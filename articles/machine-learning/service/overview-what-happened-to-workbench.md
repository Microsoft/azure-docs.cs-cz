---
title: Co se stalo s Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Přečtěte si o tom, co se stalo s aplikací Machine Learning Workbench, co se změnilo ve službě Azure Machine Learning a jaká je časová osa podpory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 42c6671479c299c3229a3769a0410223274bc87f
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279073"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co se stalo s aplikací Azure Machine Learning Workbench?

Aplikace Azure Machine Learning Workbench a některé další úvodní funkce byly zastaralé a nahrazené vydáním září 2018, aby bylo možné zlepšit [architekturu](concept-azure-machine-learning-architecture.md).

Pro zlepšení prostředí vydaná verze obsahuje mnoho důležitých aktualizací, které se na základě zpětné vazby od zákazníků zobrazí. Základní funkce z experimentu se spouští do nasazení modelu se nezměnily. Teď ale můžete využít robustní <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sadu SDK</a> a rozhraní příkazového [řádku Azure](reference-azure-machine-learning-cli.md) , abyste mohli provádět úlohy a kanály pro strojové učení.

Většina artefaktů, které byly vytvořeny ve starší verzi služby Azure Machine Learning, jsou uloženy ve vašem místním nebo cloudovém úložišti. Tyto artefakty se vám nikdy neztratí.

V tomto článku se dozvíte, co se změnilo a jak ovlivňuje stávající práci s Azure Machine Learning Workbench a jeho rozhraními API.

>[!Warning]
>Tento článek není pro uživatele Azure Machine Learning Studio. Je pro zákazníky služby Azure Machine Learning, kteří mají nainstalované aplikace Workbench (preview) a/nebo mají experimentování a ve verzi preview účty správy modelů.


## <a name="what-changed"></a>Co se změnilo?

Nejnovější verze služby Azure Machine Learning Service obsahuje následující funkce:
+ [Zjednodušený model prostředků Azure](concept-azure-machine-learning-architecture.md).
+ [Nové uživatelské rozhraní portálu](how-to-track-experiments.md) pro správu experimentů a cílů výpočtů.
+ Nová, komplexnější <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sada Python SDK</a>.
+ Nové rozšířené [rozšíření Azure CLI](reference-azure-machine-learning-cli.md) pro Machine Learning.

[Architektura](concept-azure-machine-learning-architecture.md) byla přepracována pro snadné použití. Místo několika prostředků a účtů Azure potřebujete jenom [pracovní prostor služby Azure Machine Learning](concept-workspace.md). Pracovní prostory můžete vytvářet rychle na webu [Azure Portal](how-to-manage-workspace.md). Když použijete pracovní prostor, více uživatelů může ukládat výpočetní a implementační cíle, modelové experimenty, image Docker, nasazené modely atd.

I když v aktuální verzi existují nové vylepšené klienty rozhraní CLI a sady SDK, aplikace Desktop Workbench byla vyřazena. Experimenty se dají spravovat na [řídicím panelu pracovního prostoru v Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Řídicí panel můžete použít k zobrazení historie experimentu, ke správě výpočetních cílů připojených k pracovnímu prostoru, ke správě modelů a imagí Dockeru, a dokonce i k nasazení webových služeb.

<a name="timeline"></a>

## <a name="support-timeline"></a>Časový plán podpory

9\. ledna 2019 podpora pro Machine Learning Workbench, účty Experimentování ve službě Azure Machine Learning a Správa modelů a jejich přidružená sada SDK a rozhraní příkazového řádku skončila.

Všechny nejnovější možnosti jsou k dispozici pomocí této <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sady SDK</a>, rozhraní příkazového [řádku](reference-azure-machine-learning-cli.md)a [portálu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Co se stane s historií spuštění?

Starší historie spuštění už nejsou dostupné, jak se vaše běhy pořád zobrazují v nejnovější verzi.

Historie spuštění se teď nazývají **experimenty**. Můžete shromáždit experimenty modelu a prozkoumat je pomocí sady SDK, rozhraní příkazového řádku nebo Azure Portal.

Řídicí panel pracovního prostoru na portálu se podporuje jenom v prohlížečích Microsoft Edge, Chrome a Firefox:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Začněte trénování modelů a sledování historie spuštění pomocí nového rozhraní příkazového řádku a sady SDK. Zjistíte, jak se [kurz: trénování modelů pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="can-i-still-prep-data"></a>Můžu dál připravovat data?

Již existující soubory pro přípravu dat nejsou přenosné na nejnovější verzi, protože už Machine Learning Workbench ještě nemáte. Můžete ale stále připravit jakoukoli datovou sadu pro modelování.

S datovými sadami libovolné velikosti můžete použít [balíček pro přípravu dat pro Azure Machine Learning](https://aka.ms/data-prep-sdk) k rychlé přípravě dat před modelováním pomocí psaní kódu Pythonu.

## <a name="will-projects-persist"></a>Vydrží mi moje projekty?

Neztratíte žádný kód ani práci. Ve starší verzi jsou projekty cloudové entity s místním adresářem. V nejnovější verzi se místní adresáře připojovat k pracovnímu prostoru služby Azure Machine Learning pomocí místního konfiguračního souboru. Podívejte se na [diagram nejnovější architektury](concept-azure-machine-learning-architecture.md).

Většina obsahu projektu již byla na vašem místním počítači. Takže stačí vytvořit konfigurační soubor v tomto adresáři a odkázat ho na něj v kódu, abyste se připojili k vašemu pracovnímu prostoru. Chcete-li pokračovat v používání místního adresáře, který obsahuje soubory a skripty, zadejte název adresáře v příkazu ["experiment. Submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) v Pythonu nebo `az ml project attach` pomocí příkazu CLI.  Příklad:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Vytvořte pracovní prostor](how-to-manage-workspace.md) , abyste mohli začít.

## <a name="what-about-my-registered-models-and-images"></a>K čemu se zaregistrovaly modely a image?

Modely, které jste zaregistrovali v registru starý model se musí migrovat na nový pracovní prostor, pokud chcete dál používat. Pokud chcete migrovat modely, Stáhněte si modely a znovu je Zaregistrujte v novém pracovním prostoru.

Image, které jste vytvořili v původním registru imagí, nejde přímo migrovat do nového pracovního prostoru. Ve většině případů je možné model nasadit bez nutnosti vytvářet image. V případě potřeby můžete vytvořit obrázek pro model v novém pracovním prostoru. Další informace najdete v tématu [Správa, registrace, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co se stane s nasazenými webovými službami?

Teď, když už skončila podpora pro staré rozhraní příkazového řádku, už nebudete moct znovu nasazovat modely ani spravovat webové služby, které jste původně nasadili s vaším účtem Správa modelů. Tyto webové služby však bude i nadále fungovat tak dlouho, dokud je Azure Container Service (ACS) stále podporovány.

V nejnovější verzi jsou nasazení modelů jako webové služby do clusteru Azure Container Instances (ACI) nebo Azure Kubernetes Service (AKS). Můžete také nasadit na FPGA a Azure IoT Edge.

Další informace najdete v těchto článcích:
+ [Kde a jak nasadit modely](how-to-deploy-and-where.md)
+ [Kurz: Nasazení modelů pomocí služby Azure Machine Learning Service](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>Co je to stará sada SDK a rozhraní příkazového řádku?

Ano, budou i nadále fungovat až do ledna. Podívejte se na předchozí [časovou osu](#timeline). Doporučujeme začít vytvářet nové experimenty a modely pomocí nejnovější sady SDK nebo rozhraní příkazového řádku.

Pomocí nové sady Python SDK v nejnovější verzi můžete komunikovat se službou Azure Machine Learning v jakémkoli prostředí Pythonu. Přečtěte si, jak nainstalovat nejnovější sadu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>. Můžete také použít aktualizované [rozšíření Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) s bohatou sadou `az ml` příkazů pro interakci se službou v jakémkoli prostředí příkazového řádku, včetně Azure Cloud Shell.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Co Visual Studio Code Tools for AI?

V této nejnovější verzi bylo rozšíření přejmenováno na Azure Machine Learning pro Visual Studio Code a bylo rozšířeno a vylepšeno pro práci s předchozími novými funkcemi.

[![Azure Machine Learning pro Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co se stane s doménovými balíčky?

Balíčky domény pro počítačové zpracování obrazu, Text Analytics a prognózy se nedají použít s nejnovější verzí Azure Machine Learning. Můžete však stále vytvářet a vyvíjet modely pro počítačové zpracování obrazu, text a prognózy pomocí nejnovější <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sady SDK</a>pro Azure Machine Learning Python. Pokud se chcete dozvědět, jak migrovat již existující modely vytvořené pomocí počítačových Vision, textových analýz a balíčků prognóz, kontaktujte [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Další postup

Přečtěte si o [nejnovější architektuře služby Azure Machine Learning Service](concept-azure-machine-learning-architecture.md).

Přehled služby najdete v článku [co je služba Azure Machine Learning?](overview-what-is-azure-ml.md).

Vytvoření prvního experimentu pomocí dvoufázové příručky pro [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md) a [výuka vašeho prvního modelu](tutorial-1st-experiment-sdk-train.md)

Podrobnější informace o tomto pracovním postupu najdete v [kurzu pro celou délku](tutorial-train-models-with-aml.md) , který obsahuje podrobné kroky pro školení a nasazení modelů pomocí služby Azure Machine Learning.
