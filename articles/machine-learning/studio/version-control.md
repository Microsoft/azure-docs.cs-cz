---
title: ALM v Azure Machine Learning | Microsoft Docs
description: Použít osvědčených postupů pro správu životního cyklu aplikací v nástroji Azure Machine Learning Studio
keywords: Správa verzí Azure ML, Správa životního cyklu aplikací, ALM, AML,
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.openlocfilehash: 49d1a228132cc220b30091481bb542623b1e222d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835861"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Správa životního cyklu aplikací v nástroji Azure Machine Learning Studio
Azure Machine Learning Studio je nástroj pro vývoj experimenty strojové učení, které jsou operationalized v platformě Azure cloud. Je třeba Visual Studio IDE a škálovatelné cloudové služby sloučena do jednoho platformy. Standardní postupy Application Lifecycle Management (ALM) z verze můžete začlenit do Azure Machine Learning Studio různé prostředky pro nasazení a automatické spuštění. Tento článek popisuje některé z možností a přístupy.

## <a name="versioning-experiment"></a>Správa verzí experimentu
Existují dva způsoby doporučenou verzi experimentů. Můžete buď závisí na předdefinované historie spouštění nebo exportovat experimentu ve formátu JSON, která externě k její správě. Každý přístup se dodává s jeho výhody a nevýhody.

### <a name="experiment-snapshots-using-run-history"></a>Snímky experimentu pomocí spustit historie
Ve model spuštění Azure Machine Learning Studio učení experimentu neměnné kopii experimentu odeslání do plánovače úloh vždy, když kliknete na tlačítko **spustit** v editoru experimentu. Chcete-li zobrazit tento seznam snímky, klikněte na tlačítko **historii běhů** na panelu příkazů v okně editor experimentu.

![Historie tlačítku spuštění](./media/version-control/runhistory.png)

Můžete pak pořízení snímku v režimu uzamčen klepnutím na název experimentu v době, kdy byla odeslána experiment spustit a snímku otevřete. Všimněte si, že pouze první položka v seznamu, který představuje aktuální experiment, je ve stavu upravovat. Všimněte si, že každý snímek může být v různých stavu také stavy i, včetně dokončení se nezdařilo (Partial spustit), se nezdařilo (Partial spustit), nebo koncept.

![Seznam pro spuštění historie](./media/version-control/runhistorylist.png)

Po jejím otevření, můžete uložit experimentu snímků jako nový experiment a upravit ho. Pokud snímku experiment obsahuje prostředky, například trénované modely, transformací nebo datových sad, které byly aktualizovány verzí, zachová snímku odkazy na původní verze při pořízení snímku. Pokud uložíte uzamčeném snímku jako nový experiment, Azure Machine Learning Studio zjistí existenci na novější verzi tyto prostředky a automaticky je aktualizace v nový experiment.

Pokud odstraníte experiment, se odstraní všechny snímky tohoto testu.

### <a name="exportimport-experiment-in-json-format"></a>Export a import experimentu ve formátu JSON
Snímky historie spouštění ponechat neměnné verzi experimentu v nástroji Azure Machine Learning Studio pokaždé, když je odeslána ke spuštění. Můžete také uložit místní kopii experiment a vrácení se změnami do systému správy zdrojů oblíbených položek, jako je Team Foundation Server a později znovu vytvořit nový experiment z tohoto místního souboru. Můžete použít [Azure Machine Learning PowerShell](http://aka.ms/amlps) rutiny PowerShell [ *Export AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [ *Import AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) provést tuto akci.

Soubor JSON je textovou reprezentaci experimentu grafu, která by mohla obsahovat odkaz na prostředky v pracovním prostoru například datové sady nebo modulu trained model. Neobsahuje serializovaná verze prostředku. Pokud se pokusíte importovat dokumentu JSON zpět do pracovního prostoru, musí již existovat odkazované prostředky se stejným ID, které se odkazuje v rámci experimentu asset. V opačném případě nebude možné získat importované experimentu.

## <a name="versioning-trained-model"></a>Správa verzí trained model
Modulu trained model v Azure Machine Learning je serializován do formátu známé jako soubor iLearner (`.iLearner`) a je uložený v účtu úložiště objektů Blob v Azure, přidružené k pracovním prostoru. Jeden způsob, jak získat kopii reprezentuje soubor iLearner je přes rozhraní retraining API. [Tento článek](retrain-models-programmatically.md) vysvětluje, jak funguje rozhraní retraining API. Hlavní kroky:

1. Nastavte experimentu školení.
2. Přidáte do modulu Train Model nebo modul, který vytváří vyškolení modelu, například ladit Hyperparameter modelu nebo vytvořit Model R výstupní port webové služby.
3. Spusťte výukový experiment a poté ji nasadit jako webovou službu školení modelu.
4. Volání BES koncový bod webové služby, školení a zadejte požadované iLearner název souboru a umístění účtu úložiště objektů Blob, kde bude uložena.
5. Sbírání reprezentuje soubor iLearner vytvořené po dokončení volání BES.

Jiný způsob, jak načíst soubor iLearner je pomocí prostředí PowerShell [ *stažení AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). To může být jednodušší, pokud chcete získat kopii souboru iLearner, aniž by bylo nutné programově přeučit modelu.

Až budete mít soubor iLearner obsahující trénovaného modelu, můžete potom použít strategie správy verzí. Strategie může být stejně jednoduché jako použití pre/operátory jako zásady vytváření názvů a právě ponechat v úložišti objektů Blob reprezentuje soubor iLearner nebo kopírování nebo jeho import do systému správy verzí.

Reprezentuje soubor iLearner uložené lze vyhodnocování prostřednictvím nasazených webových služeb.

## <a name="versioning-web-service"></a>Správa verzí webové služby
Můžete nasadit dva typy webových služeb z Azure Machine Learning experimentu. Classic webové služby je úzce spojeny s experiment, jakož i v pracovním prostoru. Nová webová služba používá rozhraní Azure Resource Manager a je už doplněná s původní experimentu nebo v pracovním prostoru.

### <a name="classic-web-service"></a>Classic webové služby
Verzi classic webové služby můžete využít konstruktu koncový bod webové služby. Tady je obvyklý průběh:

1. Z experimentu prediktivní nasadíte novou classic webovou službu, která obsahuje výchozí koncový bod.
2. Můžete vytvořit nový koncový bod s názvem ep2, který zveřejňuje aktuální verzi experimentu/cvičení modelu.
3. Přejděte zpět a aktualizovat prediktivní experiment a trained model.
4. Můžete znovu nasadit prediktivní experiment, které aktualizují výchozí koncový bod. Ale to nijak nezmění ep2.
5. Můžete vytvořit další koncový bod s názvem ep3, která zpřístupňuje nové verze experiment a trained model.
6. Přejděte zpět ke kroku 3 v případě potřeby.

V čase můžete mít mnoho koncové body vytvořené ve stejné webové službě. Každý koncový bod představuje kopii v okamžiku experimentu obsahující bod v čase verzi trénovaného modelu. Pak můžete použít externí logika pro určení volání, které koncového bodu, což efektivně znamená výběr verzi trénovaného modelu pro výpočet skóre spustit.

Můžete také vytvořit mnoho koncových bodů identické webové služby a potom oprava různé verze souboru iLearner ke koncovému bodu k dosažení podobný vliv. [Tento článek](create-models-and-endpoints-with-powershell.md) podrobněji vysvětluje, jak provést tuto akci.

### <a name="new-web-service"></a>Novou webovou službu
Pokud vytvoříte nový na základě Azure Resource Manager webové služby, koncový bod konstrukce již není k dispozici. Místo toho můžete vygenerovat definice (WSD) souborů webové služby, ve formátu JSON, z vaší prediktivní experiment pomocí [Export AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) prostředí PowerShell., nebo pomocí [ *Export AzureRmMlWebservice* ](https://msdn.microsoft.com/library/azure/mt767935.aspx) prostředí PowerShell ze služby nasazené využívající Resource Manager.

Až budete mít exportovaný soubor WSD a verze řídit, můžete taky nasadit WSD jako novou webovou službu v plánu jiné webové služby v jiné oblasti Azure. Dejte pozor, že zadáte konfigurace účtu správné úložiště, jakož i nové ID plánu webové služby. Opravit v různých iLearner soubory, můžete upravit soubor WSD a aktualizovat odkaz na umístění pro cvičný model a nasaďte ho jako novou webovou službu.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizovat nasazení a spuštění experimentu
Důležitým aspektem ALM je možné automatizovat zpracování a proces nasazení aplikace. V Azure Machine Learning, lze provést pomocí [modulu PowerShell](http://aka.ms/amlps). Tady je příklad začátku do konce kroky, které jsou relevantní pro standardní ALM automatizované procesu provádění a nasazení pomocí [modul Azure Machine Learning Studio PowerShell](http://aka.ms/amlps). Každý krok je propojený na jeden nebo více rutiny prostředí PowerShell, které můžete použít k provedení tohoto kroku.

1. [Nahrání datové sady](https://github.com/hning86/azuremlps#upload-amldataset).
2. Zkopírujte výukový experiment do pracovního prostoru z [prostoru](https://github.com/hning86/azuremlps#copy-amlexperiment) nebo z [Galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), nebo [importovat](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [exportovat](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experimentovat z místního disku.
3. [Aktualizovat datovou sadu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v experimentu školení.
4. [Spusťte experiment školení](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Zvýšení úrovně pro cvičný model](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Zkopírujte prediktivní experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) do pracovního prostoru.
7. [Aktualizace pro cvičný model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v prediktivní experiment.
8. [Spusťte experiment prediktivní](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Nasazení webové služby](https://github.com/hning86/azuremlps#new-amlwebservice) z prediktivní experiment.
10. Test webové služby [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) nebo [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) koncový bod.

## <a name="next-steps"></a>Další postup
* Stažení [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) modulu a spusťte automatizovat úkoly ALM.
* Zjistěte, jak [vytvořit a spravovat velký počet ML modelů pomocí právě jeden experimentu](create-models-and-endpoints-with-powershell.md) pomocí prostředí PowerShell a retraining API.
* Další informace o [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).
