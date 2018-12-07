---
title: Co se stalo s aplikací Azure Machine Learning Workbench?
description: Další informace o co se stalo se aplikace Workbench aplikace, co se změnilo ve službě Azure Machine Learning a co je časové osy podpory.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b6df86d63e897d5cde34fb697ad2364c107a8fbe
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011129"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-service"></a>Co se děje do aplikace Workbench ve službě Azure Machine Learning?

Aplikace Workbench a některé počáteční funkce byly zastaralé a ve verzi. září 2018 aby způsob, jak zdokonalené [architektura](concept-azure-machine-learning-architecture.md). Tato verze obsahuje řadu významných aktualizací, které reagují na připomínky zákazníků, kteří požadovali lepší prostředí. Základní funkce zkušebně spuštěného nasazeného modelu se nezměnily, ale teď můžete k plnění úkolů a kanálů spojených se strojovým učením použít robustní sadu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> a nástroj [CLI](reference-azure-machine-learning-cli.md).  

V tomto článku se dozvíte o co se změnilo, a o jejím dopadu na vaše existující dílo aplikaci Azure Machine Learning Workbench a jeho rozhraní API.

## <a name="what-changed"></a>Co se změnilo?

Nejnovější verze služby Azure Machine Learning zahrnuje:
+ [Zjednodušený model prostředků Azure](concept-azure-machine-learning-architecture.md)
+ [Nové uživatelské rozhraní portálu](how-to-track-experiments.md) určené ke správě experimentů a výpočetních cílů
+ Novou rozsáhlejší sadu Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>
+ Nové dokonalejší [rozšíření Azure CLI](reference-azure-machine-learning-cli.md) pro strojové učení

[Architektura](concept-azure-machine-learning-architecture.md) byla přepracována s důrazem na snadnější použití. Místo několika prostředků a účtů Azure potřebujete jenom [pracovní prostor služby Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace).  Pracovní prostory můžete vytvářet rychle na webu [Azure Portal](quickstart-get-started.md).  Pracovní prostor může používat více uživatelů, kteří do něj můžou ukládat cvičné i nasazené výpočetní cíle, experimentální modely, image Dockeru, nasazené modely a další objekty.

Aktuální verze sice nabízí nové vylepšené klienty CLI a SDK, ale vlastní počítačová aplikace Workbench je zastaralá. Teď můžete k monitorování svých experimentů použít [řídicí panel pracovního prostoru na webovém portálu Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Řídicí panel můžete použít k zobrazení historie experimentu, ke správě výpočetních cílů připojených k pracovnímu prostoru, ke správě modelů a imagí Dockeru, a dokonce i k nasazení webových služeb.

## <a name="how-do-i-migrate"></a>Jak provést migraci?

Většina artefaktů vytvořených v předchozí verzi služby Azure Machine Learning je uložených ve vašem místním nebo cloudovém úložišti. Tyto artefakty se vám nikdy neztratí. Pokud je chcete migrovat, musíte je znovu zaregistrovat v aktualizované službě Azure Machine Learning. Informace o tom, co a jak můžete migrovat najdete v tomto [článku o migraci](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Časový plán podpory

Své účty pro experimentování a správu modelů můžete i s aplikací Workbench používat i po září 2018. Podpora těchto prostředků bude postupně končit 3–4 měsíce po tomto vydání. Dokumentaci ke starým funkcím stále najdete v [části Prostředky](../desktop-workbench/tutorial-classifying-iris-part-1.md) na konci obsahu.

|Vyřazení&nbsp;fáze|Podrobnosti o podpoře dřívějších funkcí|
|:---:|----------------|
|4. prosince 2018|Schopnost vytvářet _účet experimentování ve službě Azure Machine Learning_ a _účet služby Správa modelů_ na webu Azure Portal a rozhraní příkazového řádku byla ukončena. Skončila také možnost vytvářet prostředí Compute ML z rozhraní příkazového řádku. Pokud máte účet, bude rozhraní příkazového řádku i desktopová aplikace Workbench v této fázi dále fungovat.|
|9. ledna 2019|Podpora pro všechno ostatní, včetně zbývající rozhraní API a desktopové aplikace Workbench skončí k tomuto datu.|

[Začněte s migrací](how-to-migrate.md) ještě dnes. Všechny nejnovější funkce jsou k dispozici v nové sadě <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, v [rozhraní příkazového řádku](reference-azure-machine-learning-cli.md) a na [portálu](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Co se stane s historií spuštění?

Historie spuštění bude ještě chvíli přístupná. Až budete připraveni přejít na aktualizovanou verzi služby Azure Machine Learning, můžete historii spuštění exportovat, pokud si chcete nechat její kopii.

Historie spuštění se v aktuální verzi označuje jako _experimenty_. Experimenty, které se týkají vašeho modelu, můžete shromažďovat a zkoumat je pomocí sady SDK, rozhraní příkazového řádku nebo webového portálu.

Řídicí panel pracovního prostoru portálu je podporovaný jenom v prohlížečích Edge, Chrome a Firefox.

[ ![Online portal](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Můžu dál připravovat data?

Předchozí soubory s připravenými daty se do nejnovější verze nedají přenést, protože už nemáme aplikaci Workbench. I přesto ale můžete připravovat data k modelování.  

Pokud máte menší sady dat, můžete použít <a href="https://aka.ms/aml-sdk" target="_blank">sadu SDK pro přípravu dat Azure Machine Learning</a> a rychle s ní připravit data před modelováním. 

Stejnou sadu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> můžete použít i pro větší datové sady. K přípravě velkých datových sad použijte Azure Databricks. 

## <a name="will-projects-persist"></a>Vydrží mi moje projekty?

Neztratíte žádný kód ani práci. Ve starší verzi jsou projekty cloudové entity s místním adresářem. V nejnovější verzi můžete místní adresáře připojit k pracovnímu prostoru služby Azure Machine Learning pomocí místního konfiguračního souboru. [Prohlédněte si diagram nejnovější architektury](concept-azure-machine-learning-architecture.md).

Vzhledem k tomu, že většina projektového obsahu už je na místním počítači, stačí v adresáři vytvořit konfigurační soubor a odkazovat na něj ve svém kódu, aby se připojil k vašemu pracovnímu prostoru. [Přečtěte si, jak migrovat stávající projekty.](how-to-migrate.md#projects)

Přečtěte si, jak začít [v Pythonu používat hlavní sadu SDK](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>Co se stane s mými zaregistrovanými modely a imagemi?
 
Modely, které jste zaregistrovali v registru starý model se musí migrovat na nový pracovní prostor, pokud chcete dál používat. Uděláte to tak, že si [modely stáhnete a znovu zaregistrujete](how-to-migrate.md) v novém pracovním prostoru. 

Image vytvořené ve starém registru imagí je potřeba znovu vytvořit v novém pracovním prostoru, abyste je mohli dál používat. Postup najdete v části o [vytvoření image dockeru](how-to-deploy-to-aci.md#configure-an-image). 

## <a name="what-about-deployed-web-services"></a>Co se stane s nasazenými webovými službami?

Modely, které jste nasadili jako webové služby pomocí účtu pro správu modelů, budou dále fungovat, dokud bude podporovaná služba Azure Container Service (ACS). Tyto webové služby budou fungovat i po ukončení podpory účtů pro správu modelů. Jakmile skončí podpora starého rozhraní příkazového řádku (CLI), skončí i možnost správy těchto webových služeb.

V novější verzi jsou modely nasazené jako webové služby v clusterech [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) nebo [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS). Můžete je také [nasadit do zařízení FPGA a IoT Edge](how-to-deploy-and-where.md). Své modely můžete znovu nasadit pomocí nové sady SDK nebo rozhraní příkazového řádku. Nemusíte přitom měnit bodovací soubory, závislosti ani schémata. 

## <a name="what-about-the-old-sdk--cli"></a>Co se stane se starou sadou SDK a rozhraním příkazového řádku?

Ano, budou i nadále fungovat až do ledna (viz [časová osa](#timeline) výše). K vytváření nových experimentů a modelů doporučujeme použít nejnovější sadu SDK a/nebo rozhraní příkazového řádku.

Nová sada Python SDK, která je v nejnovější verzi, umožňuje pracovat se službou Azure Machine Learning v jakémkoli prostředí Pythonu. Přečtěte si, jak nainstalovat nejnovější sadu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.  V [aktualizovaném rozšíření Azure CLI pro strojové učení](reference-azure-machine-learning-cli.md) také můžete používat bohatou sadu příkazů `az ml`, které umožňují pracovat se službou v libovolném prostředí příkazového řádku, včetně cloudového prostředí webu Azure Portal.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Jak Azure Machine Learning pro Visual Studio Code?

V této nejnovější verzi byly Azure Machine Learning pro Visual Studio (VS) kód rozbalen a vylepšení pro práci s výše uvedené nové funkce.

[ ![Azure Machine Learning pro Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png) ] (. / media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co se stane s doménovými balíčky?

Doménové balíčky pro [počítačové zpracování obrazu, analýzu textu a prognózování](../desktop-workbench/reference-python-package-overview.md) nemůžete používat s nejnovější verzí služby Azure Machine Learning. K vytváření a trénování modelů pro počítačové zpracování obrazu, analýzu textu a prognózování můžete používat nejnovější verzi sady Azure Machine Learning Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>. Pokud potřebujete další informace o migraci předchozích modelů vytvořených pomocí balíčků pro počítačové zpracování obrazu, analýzu textu a prognózování, kontaktujte nás na [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Další postup

Přečtěte si o [nejnovější architektuře služby Azure Machine Learning](concept-azure-machine-learning-architecture.md) a vyzkoušejte některý z našich rychlých startů nebo kurzů:

* [Co je služba Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Rychlý start: Vytvoření pracovního prostoru v Pythonu](quickstart-get-started.md)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
