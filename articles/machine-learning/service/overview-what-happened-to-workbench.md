---
title: Co se stalo s Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: Další informace o co se stalo s Machine Learning Workbench aplikace, co se změnilo ve službě Azure Machine Learning a co je časové osy podpory.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 614c4b9f8cb6b748385d86d20cbec15928642d30
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753298"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Co se děje, Machine Learning Workbench ve službě Azure Machine Learning?

Aplikaci Azure Machine Learning Workbench a některé počáteční funkce byly zastaralé a ve verzi. září 2018 aby způsob, jak zdokonalené [architektura](concept-azure-machine-learning-architecture.md). Pro zlepšení možností verze obsahuje mnoho významných aktualizací výzvě zpětné vazby od zákazníků. Základní funkce z dostupných spuštění experimentu do modelu nasazení se nezměnil. Teď, můžete ale použít robustní <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> a [rozhraní příkazového řádku Azure](reference-azure-machine-learning-cli.md) k provedení strojového učení úkoly a kanály.  

V tomto článku se dozvíte o co se změnilo, a o jejím dopadu na vaše existující dílo aplikaci Azure Machine Learning Workbench a jeho rozhraní API.

## <a name="what-changed"></a>Co se změnilo?

Nejnovější verze služby Azure Machine Learning obsahuje následující funkce:
+ A [zjednodušené prostředků Azure modelu](concept-azure-machine-learning-architecture.md).
+ A [nové uživatelské rozhraní portálu](how-to-track-experiments.md) ke správě své experimenty a cílových výpočetních prostředí.
+ Nové a další komplexní Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.
+ Nové rozšířit [rozšíření rozhraní příkazového řádku Azure](reference-azure-machine-learning-cli.md) pro machine learning.

[Architektura](concept-azure-machine-learning-architecture.md) byla přepracována pro snadné použití. Místo několika prostředků a účtů Azure potřebujete jenom [pracovní prostor služby Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace). Pracovní prostory můžete vytvářet rychle na webu [Azure Portal](quickstart-get-started.md). Použití pracovního prostoru, více mohou uživatelé ukládat školení a nasazení výpočetního cíle, model experimenty, Image Dockeru, nasazených modelů a tak dále.

I když existují nové vylepšené rozhraní příkazového řádku a sady SDK klienty v aktuální verzi, samotná aplikace klasické pracovní plochy aplikace workbench je zastaralý. Teď můžete monitorovat své experimenty v [řídicího panelu pracovního prostoru na webu Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Řídicí panel můžete použít k zobrazení historie experimentu, ke správě výpočetních cílů připojených k pracovnímu prostoru, ke správě modelů a imagí Dockeru, a dokonce i k nasazení webových služeb.

## <a name="how-do-i-migrate"></a>Jak provést migraci?

Většina artefakty, které byly vytvořeny ve starší verzi služby Azure Machine Learning jsou uloženy ve vlastní místní nebo cloudové úložiště. Tyto artefakty se vám nikdy neztratí. Pokud je chcete migrovat, musíte je znovu zaregistrovat v aktualizované službě Azure Machine Learning. Informace o tom, co a jak můžete migrovat najdete v tomto [článku o migraci](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Časový plán podpory

Stále můžete účtům služby experimentování ve službě Machine Learning a správa modelů a Machine Learning Workbench aplikace po. září 2018. Postupně v tři až čtyři měsíců po vydání, který se odebere podporu pro následující prostředky. Dokumentaci ke starým funkcím stále najdete v [části Prostředky](../desktop-workbench/tutorial-classifying-iris-part-1.md) na konci obsahu.

|Vyřazení&nbsp;fáze|Podrobnosti o podpoře dřívějších funkcí|
|:---:|----------------|
|4. prosince 2018|Možnost vytvářet účty experimentování ve službě Azure Machine Learning a správa modelů ve službě na webu Azure Portal a rozhraní příkazového řádku byla ukončena. Skončila také možnost vytvářet prostředí Machine Learning compute z rozhraní příkazového řádku. Pokud máte existující účet, rozhraní příkazového řádku a aplikaci desktop Machine Learning Workbench pokračovat v práci v této fázi.|
|9. ledna 2019|Podpora pro všechno ostatní končí toto datum. Příklady zbývajících rozhraní API a desktopové aplikace Machine Learning Workbench.|

[Začněte s migrací](how-to-migrate.md) ještě dnes. Všechny nejnovější funkce jsou dostupné při použití nové <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, [rozhraní příkazového řádku](reference-azure-machine-learning-cli.md)a [portál](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Co se stane s historií spuštění?

Historie spuštění bude přístupné po nějakou dobu. Až budete připravení přejít na aktualizovanou verzi služby Azure Machine Learning, můžete exportovat tyto historie spuštění, pokud chcete zachovat kopii.

Historie spuštění, se nazývají **experimenty** v aktuální verzi. Můžete shromažďovat váš model experimenty a prozkoumat pomocí sady SDK, rozhraní příkazového řádku nebo na webu Azure portal.

Řídicí panel pracovního prostoru na portálu je podporovaná jenom prohlížeče Microsoft Edge, Chrome a Firefox:

[ ![Online portálu](./media/overview-what-happened-to-workbench/image001.png)] (. / media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Můžu dál připravovat data?

Existující soubory přípravy dat nejsou přenosné na nejnovější verzi, protože už nemáme Machine Learning Workbench. I přesto ale můžete připravovat data k modelování.  

S datovými sadami libovolné velikosti, můžete použít [sady SDK služby Azure Machine Learning Data Prep](http://aka.ms/data-prep-sdk) k rychlé přípravě vaše data před modelování napsáním kódu v Pythonu. 

Můžete postupovat podle [v tomto kurzu](tutorial-data-prep.md) Další informace o tom, jak pomocí Azure Machine Learning Data Prep SDK.

## <a name="will-projects-persist"></a>Vydrží mi moje projekty?

Neztratíte žádný kód ani práci. Ve starší verzi jsou projekty cloudové entity s místním adresářem. V nejnovější verzi připojíte místní adresáře do služby Azure Machine Learning Workspace s použitím místní konfigurační soubor. Najdete v článku [diagram architektury nejnovější](concept-azure-machine-learning-architecture.md).

Velká část obsahu projektu se již v místním počítači. Proto stačí na něj odkazovat ve vašem kódu pro připojení k vašemu pracovnímu prostoru a vytvoření konfiguračního souboru v tomto adresáři. Zjistěte, jak [migrovat existující projekty](how-to-migrate.md#projects).

Zjistěte, jak [začít pracovat v Pythonu s hlavním SDK](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Jak Moje registrované modely a Image?
 
Modely, které jste zaregistrovali v registru starý model se musí migrovat na nový pracovní prostor, pokud chcete dál používat. K migraci vašich modelů [stáhněte modely a zaregistrovat je znovu](how-to-migrate.md) v nový pracovní prostor. 

Image vytvořené ve starém registru imagí je potřeba znovu vytvořit v novém pracovním prostoru, abyste je mohli dál používat. Tyto Image můžete znovu vytvořit pomocí následujících [konfigurace a vytvoření bitové kopie](how-to-deploy-and-where.md#configureimage) oddíly. 

## <a name="what-about-deployed-web-services"></a>Co se stane s nasazenými webovými službami?

Modely, které jste nasadili jako webové služby pomocí svého účtu správy modelů Machine Learning budou fungovat tak dlouho, dokud se podporuje Azure Container Service. Tyto webové služby bude fungovat i po ukončení podpory pro účty správy modelů Machine Learning. Jakmile skončí podpora starého rozhraní příkazového řádku (CLI), skončí i možnost správy těchto webových služeb.

V novější verzi mají být nasazeny modely jako webové služby do clusteru Azure Container Instances nebo Azure Kubernetes Service (AKS). Můžete také nasadit do FPGA a Azure IoT Edge. Další informace najdete v článku na [nasazení a kde](how-to-deploy-and-where.md). Aniž byste museli změnit bodovací soubory, závislosti a schémat, můžete znovu nasaďte vlastní modely pomocí nové sady SDK nebo rozhraní příkazového řádku. 

## <a name="what-about-the-old-sdk-and-cli"></a>Co staré SDK a rozhraní příkazového řádku?

Ano, budou dál fungovat až do ledna. Viz předchozí [časová osa](#timeline). Doporučujeme spouštět vytváření nových experimentů a modelů s nejnovější SDK nebo rozhraní příkazového řádku.

Když použijete sadu SDK pro Python v nejnovější verzi, můžete pracovat se službou Azure Machine Learning v jakémkoli prostředí Pythonu. Přečtěte si, jak nainstalovat nejnovější sadu <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>. Můžete také použít aktualizovaná [rozšíření rozhraní příkazového řádku pro Azure Machine Learning](reference-azure-machine-learning-cli.md) s bohatou sadu `az ml` příkazy pro interakci se službou v jakémkoli příkazového řádku prostředí, včetně Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Jak Azure Machine Learning pro Visual Studio Code?

V této nejnovější verzi byly Azure Machine Learning pro Visual Studio Code rozšířit a vylepšení pro práci s předchozím nové funkce.

[ ![Azure Machine Learning pro Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (. / media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co se stane s doménovými balíčky?

Doména balíčky [pro počítačové zpracování obrazu, analýzu textu a předvídání](../desktop-workbench/reference-python-package-overview.md) nelze použít s nejnovější verzí služby Azure Machine Learning. Ale můžete pořád vytvářet a trénování pro počítačové zpracování obrazu, textu a předvídání modely s nejnovější Python Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>. Zjistěte, jak migrovat existující modely sestavené pomocí pro počítačové zpracování obrazu, analýzu textu a předvídání balíčky, obraťte se na [ AML-Packages@microsoft.com ](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Další postup

Další informace o [nejnovější architektura pro službu Azure Machine Learning](concept-azure-machine-learning-architecture.md). Zkuste použijte jeden z kurzů nebo rychlých startů:

* [Co je služba Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Rychlý start: Vytvoření pracovního prostoru s využitím Pythonu](quickstart-get-started.md)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
