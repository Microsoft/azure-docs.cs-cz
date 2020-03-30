---
title: Správa životního cyklu aplikací
titleSuffix: ML Studio (classic) - Azure
description: Použití doporučených postupů správy životního cyklu aplikací v Azure Machine Learning Studio (klasické)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 3f22ce3b1fb750e33e35d35ee1fe5ad1893abcfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204132"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Správa životního cyklu aplikací v Azure Machine Learning Studio (klasická)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (classic) je nástroj pro vývoj experimentů strojového učení, které jsou zprovozněny v cloudové platformě Azure. Je to jako IDE Visual Studio a škálovatelné cloudové služby sloučeny do jedné platformy. Do Azure Machine Learning Studio (klasické) můžete začlenit standardní postupy správy životního cyklu aplikací (ALM) od správy verzí různých datových zdrojů až po automatizované spuštění a nasazení. Tento článek popisuje některé možnosti a přístupy.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experiment správy verzí
Existují dva doporučené způsoby, jak experimenty používat. Můžete se spolehnout na předdefinovanou historii spuštění nebo exportovat experiment ve formátu JSON, abyste jej mohli spravovat externě. Každý přístup přichází s jeho klady a zápory.

### <a name="experiment-snapshots-using-run-history"></a>Experimentovat snímky pomocí historie běhu
V modelu spuštění Azure Machine Learning Studio (klasické) učení experimentu neměnný snímek experimentu se zobrazí do plánovače úloh **vždy,** když kliknete spustit v editoru experimentu. Chcete-li zobrazit tento seznam snímků, klepněte na panelu příkazů v zobrazení editoru experimentu na **položku Spustit historii.**

![Tlačítko Spustit historii](./media/version-control/runhistory.png)

Snímek v uzamčeném režimu pak můžete otevřít kliknutím na název experimentu v době, kdy byl experiment odeslán ke spuštění a snímek byl pořízen. Všimněte si, že pouze první položka v seznamu, která představuje aktuální experiment, je ve stavu Upravitelné. Všimněte si také, že každý snímek může být také v různých stavech, včetně dokončeného (částečné spuštění), neúspěšného, neúspěšného (částečné spuštění) nebo konceptu.

![Spustit seznam Historie](./media/version-control/runhistorylist.png)

Po otevření můžete experiment snímků uložit jako nový experiment a potom jej upravit. Pokud snímek experimentu obsahuje prostředky, jako jsou trénované modely, transformace nebo datové sady, které mají aktualizované verze, snímek zachová odkazy na původní verzi při pořízení snímku. Pokud uložíte uzamčený snímek jako nový experiment, Azure Machine Learning Studio (klasické) zjistí existenci novější verze těchto prostředků a automaticky je aktualizuje v novém experimentu.

Pokud experiment odstraníte, budou odstraněny všechny snímky tohoto experimentu.

### <a name="exportimport-experiment-in-json-format"></a>Experiment exportu a importu ve formátu JSON
Snímky historie spuštění zachovat neměnné verzi experimentu v Azure Machine Learning Studio (klasické) pokaždé, když je odeslána ke spuštění. Můžete také uložit místní kopii experimentu a zkontrolovat ji do svého oblíbeného systému správy zdrojového kódu, například Team Foundation Server, a později znovu vytvořit experiment z tohoto místního souboru. K dosažení tohoto cíle můžete použít příkazy [Prostředí Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [*Import-AmlExperimentGraph.*](https://github.com/hning86/azuremlps#import-amlexperimentgraph)

Soubor JSON je textová reprezentace grafu experimentu, který může obsahovat odkaz na datové zdroje v pracovním prostoru, jako je například datová sada nebo trénovaný model. Neobsahuje serializovanou verzi datového zdroje. Pokud se pokusíte importovat dokument JSON zpět do pracovního prostoru, odkazované prostředky musí již existovat se stejnými ID majetku, na které se odkazuje v experimentu. V opačném případě nelze získat přístup k importovanému experimentu.

## <a name="versioning-trained-model"></a>Trénovaný model pro správu verzí
Trénovaný model v Azure Machine Learning Studio (classic) je serializován`.iLearner`do formátu známého jako soubor iLearner ( ) a je uložen ý v účtu úložiště objektů blob Azure přidružené k pracovnímu prostoru. Jedním ze způsobů, jak získat kopii souboru iLearner je prostřednictvím retraining API. [Tento článek](/azure/machine-learning/studio/retrain-machine-learning-model) vysvětluje, jak funguje rekvalifikační rozhraní API. Kroky na vysoké úrovni:

1. Nastavte si tréninkový experiment.
2. Přidejte výstupní port webové služby do modulu Train Model nebo modulu, který vytváří trénovaný model, například Hyperparametr Tune Model nebo Create R Model.
3. Spusťte tréninkový experiment a nasadit jej jako model školení webové služby.
4. Zavolejte koncový bod BES školicí webové služby a zadejte požadovaný název souboru iLearner a umístění účtu úložiště objektů Blob, kde bude uložen.
5. Sklizeň vyrobené iLearner soubor po dokončení volání BES.

Dalším způsobem, jak načíst soubor iLearner je prostřednictvím příkazu PowerShell [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). To může být jednodušší, pokud chcete získat kopii souboru iLearner bez nutnosti programově přeškolit model.

Poté, co máte soubor iLearner obsahující trénovaný model, můžete použít vlastní strategii správy verzí. Strategie může být stejně jednoduché jako použití pre/ postfix jako konvence pojmenování a právě opuštění souboru iLearner v úložišti objektů Blob nebo kopírování/import do systému správy verzí.

Uložený soubor iLearner pak lze použít pro vyhodnocování prostřednictvím nasazených webových služeb.

## <a name="versioning-web-service"></a>Webová služba správy verzí
Můžete nasadit dva typy webových služeb z experimentu Azure Machine Learning Studio (klasické). Klasická webová služba je úzce spojena s experimentem, stejně jako pracovní prostor. Nová webová služba používá architekturu Azure Resource Manager a už není spojena s původním experimentem nebo pracovním prostorem.

### <a name="classic-web-service"></a>Klasická webová služba
Chcete-li verzi klasické webové služby, můžete využít vytvoření koncového bodu webové služby. Zde je typický tok:

1. Z prediktivníexperiment, nasadit novou klasickou webovou službu, která obsahuje výchozí koncový bod.
2. Vytvoříte nový koncový bod s názvem ep2, který zveřejňuje aktuální verzi experimentu nebo trénovaný model.
3. Můžete se vrátit a aktualizovat prediktivní experiment a trénovaný model.
4. Znovu nasadíte prediktivní experiment, který pak aktualizuje výchozí koncový bod. Ale to nebude měnit ep2.
5. Můžete vytvořit další koncový bod s názvem ep3, který zpřístupňuje novou verzi experimentu a trénovaný model.
6. V případě potřeby se vraťte ke kroku 3.

V průběhu času může mít mnoho koncových bodů vytvořených ve stejné webové službě. Každý koncový bod představuje kopii experimentu v okamžiku, která obsahuje verzi trénovaného modelu v čase. Potom můžete použít externí logiku k určení, který koncový bod volat, což efektivně znamená výběr verze trénovaného modelu pro spuštění vyhodnocování.

Můžete také vytvořit mnoho identických koncových bodů webové služby a potom opravit různé verze souboru iLearner do koncového bodu, abyste dosáhli podobného efektu. [Tento článek](create-models-and-endpoints-with-powershell.md) vysvětluje podrobněji, jak toho dosáhnout.

### <a name="new-web-service"></a>Nová webová služba
Pokud vytvoříte novou webovou službu založenou na Azure Resource Manager, konstrukce koncového bodu už není dostupná. Místo toho můžete generovat soubory definice webové služby (WSD) ve formátu JSON, z prediktivního experimentu pomocí příkazu [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell nebo pomocí příkazu [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell z nasazené webové služby založené na Správci prostředků.

Po exportu souboru WSD a správy verzí můžete také nasadit wsd jako novou webovou službu v jiném plánu webové služby v jiné oblasti Azure. Jen se ujistěte, že jste poskytli správnou konfiguraci účtu úložiště, stejně jako nové ID plánu webové služby. Chcete-li opravit v různých souborech iLearner, můžete upravit soubor WSD a aktualizovat odkaz na umístění trénovaného modelu a nasadit jej jako novou webovou službu.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizace provádění a nasazování experimentu
Důležitým aspektem ALM je možnost automatizovat proces provádění a nasazení aplikace. V Azure Machine Learning Studio (klasické), můžete to provést pomocí [modulu PowerShell](https://aka.ms/amlps). Tady je příklad komplexních kroků, které jsou relevantní pro standardní proces automatického spuštění a nasazení ALM pomocí [modulu PowerShell azure machine learningového studia (klasické).](https://aka.ms/amlps) Každý krok je propojen s jedním nebo více příkazy prostředí PowerShell, které můžete použít k dosažení tohoto kroku.

1. [Nahrajte datovou sadu](https://github.com/hning86/azuremlps#upload-amldataset).
2. Zkopírujte trénovací experiment do pracovního prostoru z [pracovního prostoru](https://github.com/hning86/azuremlps#copy-amlexperiment) nebo z [galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)nebo [importujte](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [exportovaný](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment z místního disku.
3. [Aktualizujte datovou sadu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v trénovacím experimentu.
4. [Spusťte tréninkový experiment](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Propagujte trénovaný model](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Zkopírujte prediktivní experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) do pracovního prostoru.
7. [Aktualizujte trénovaný model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v prediktivním experimentu.
8. [Spusťte prediktivní experiment](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Nasaďte webovou službu](https://github.com/hning86/azuremlps#new-amlwebservice) z prediktivního experimentu.
10. Otestujte koncový bod [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) nebo [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) webové služby.

## <a name="next-steps"></a>Další kroky
* Stáhněte si modul [PowerShell u Azure Machine Learning Studio (klasické)](https://aka.ms/amlps) a začněte automatizovat úlohy ALM.
* Naučte se [vytvářet a spravovat velký počet modelů ML pomocí jediného experimentu](create-models-and-endpoints-with-powershell.md) prostřednictvím prostředí PowerShell a retraining API.
* Přečtěte si další informace o [nasazení webových služeb Azure Machine Learning](deploy-a-machine-learning-web-service.md).
