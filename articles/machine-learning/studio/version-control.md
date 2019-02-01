---
title: Správa životního cyklu aplikací
titleSuffix: Azure Machine Learning Studio
description: Použít osvědčené postupy správy životního cyklu aplikací v nástroji Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 10/27/2016
ms.openlocfilehash: 26e469076e16f57300cf3e385620a723ddf51a4c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510719"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Správa životního cyklu aplikací v nástroji Azure Machine Learning Studio
Azure Machine Learning Studio je nástroj pro vývoj experimenty machine learning, které se mají zprovoznit v cloudové platformy Azure. Je třeba Visual Studio IDE a škálovatelné cloudové služby sloučena do jedné platformy. Standardní postupy Application Lifecycle Management (ALM) ze správy verzí můžete začlenit různých prostředků a na automatické spuštění a nasazení do Azure Machine Learning Studio. Tento článek popisuje některé z možností a přístupů.

## <a name="versioning-experiment"></a>Správa verzí experimentu
Existují dva způsoby doporučenou verzi své experimenty. Můžete spoléhat na předdefinované historie spuštění, nebo exportovat experiment ve formátu JSON tak, aby Správa externě. Každý přístup má s jeho výhody a nevýhody.

### <a name="experiment-snapshots-using-run-history"></a>Snímky experimentu pomocí historie spuštění
V modelu spuštění Azure Machine Learning Studio učení experiment neměnné snímek experimentu do kategorie se odešle Plánovač úloh pokaždé, když kliknete na **spustit** v editoru testu. Chcete-li zobrazit tento seznam snímků, klikněte na tlačítko **historie běhů** na panelu příkazů v okně editoru experimentu.

![Tlačítko pro spuštění historie](./media/version-control/runhistory.png)

Můžete otevřít snímek v uzamčeném režimu kliknutím na název experimentu v době, kdy byla odeslána experiment spustit a snímek byl pořízen. Všimněte si, že pouze první položku v seznamu, který představuje aktuální experiment, je ve stavu upravovat. Všimněte si, že jednotlivých snímků může být v různých stav také uvádí, včetně dokončení (částečné spustit), nebyl úspěšný, nezdařilo se (částečné spustit), nebo koncept.

![Seznam historie spuštění](./media/version-control/runhistorylist.png)

Po jejím otevření, můžete uložit snímek experimentu jako nový experiment a následně ho změnit. Pokud snímek experiment obsahuje prostředky, jako jsou trénované modely transformací a datových sad, které se mají aktualizované verze, zachová snímku odkazy na původní verzi, při pořízení snímku. Při uložení uzamčené snímku nový experiment, Azure Machine Learning Studio zjistí existenci novější verze těchto prostředků a automaticky je aktualizuje v nový experiment.

Pokud odstraníte experiment, odstraní se všechny snímky tohoto testu.

### <a name="exportimport-experiment-in-json-format"></a>Export a import experiment ve formátu JSON
Snímky historie spuštění zachovat neměnné verzi experiment v nástroji Azure Machine Learning Studio pokaždé, když se odešle ke spuštění. Můžete také uložit místní kopii experimentu a vrácení se změnami do systému správy vaše oblíbené zdroje, jako je Team Foundation Server a později znovu vytvořit experiment z místního souboru. Můžete použít [Azure Machine Learning PowerShell](https://aka.ms/amlps) rutin [ *Export-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [  *Import-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) to udělat.

Soubor JSON je textovou reprezentaci řetězce grafem experimentu, který může obsahovat odkaz na prostředky v pracovním prostoru jako datová sada nebo trénovaného modelu. Serializovaná verze prostředku neobsahuje. Při pokusu o import dokumentu JSON zpět do pracovního prostoru, musí již existovat odkazované prostředky s identifikátory, které jsou odkazovány v experimentu na stejný prostředek. Jinak nelze přistupovat k importované experimentu.

## <a name="versioning-trained-model"></a>Správa verzí trénovaného modelu
Trénovaného modelu ve službě Azure Machine Learning je serializován do formátu, známé jako soubor s příponou iLearner (`.iLearner`) a je uložena v účtu úložiště objektů Blob v Azure, který je přidružený k pracovnímu prostoru. Jeden způsob, jak získat kopii soubor iLearner, který je prostřednictvím rozhraní retraining API. [Tento článek](retrain-models-programmatically.md) vysvětluje, jak funguje rozhraní retraining API. Postup vysoké úrovně:

1. Nastavení vašeho výukového experimentu.
2. Přidáte pro modul trénování modelu nebo modul, který vytváří trénovaného modelu, jako je ladění Hyperparameter modelu nebo vytvořit Model R výstupní port webové služby.
3. Spuštění výukového experimentu a pak ho nasadíme jako webovou službu trénování modelu.
4. Vyvolat BES koncový bod webové služby, vzdělávání a zadejte název souboru požadovaného iLearner a umístění účtu úložiště objektů Blob, kde bude uložena.
5. Získejte soubor vyprodukované iLearner, který po dokončení BES volání.

Dalším způsobem, jak načíst soubor iLearner, který je rutinu Powershellu [ *stahování AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). To může být jednodušší, pokud chcete získat kopii soubor iLearner, který bez nutnosti přeučování modelu.

Jakmile budete mít soubor iLearner, který obsahuje trénovaného modelu, můžete pak použít strategii správy verzí. Strategie může být stejně jednoduché jako použití předem/příponový jako zásady vytváření názvů a právě byste museli opustit soubor iLearner, který ve službě Blob storage nebo kopírování/naimportujete do systému správy verzí.

Soubor uložený iLearner, který lze vyhodnocování prostřednictvím nasazené webové služby.

## <a name="versioning-web-service"></a>Webová služba správy verzí
Nasadíte dva typy webových služeb z experimentu Azure Machine Learning. Klasické webové služby je úzce párována experiment, stejně jako pracovní prostor. Nové webové služby pomocí rozhraní Azure Resource Manageru a je už párována s původní experiment nebo v pracovním prostoru.

### <a name="classic-web-service"></a>Klasické webové služby
Verze klasickou webovou službou můžete využít konstrukce koncový bod webové služby. Zde je typický tok:

1. Z prediktivního experimentu nasadíte nový klasickou webovou službou, která obsahuje výchozí koncový bod.
2. Můžete vytvořit nový koncový bod s názvem ep2, která poskytuje aktuální verzi experiment/Trénink modelu.
3. Přejděte zpět a aktualizovat prediktivní experiment a trénovaného modelu.
4. Můžete znovu nasadit prediktivní experiment, který se pak aktualizujte výchozí koncový bod. Ale to nijak nezmění ep2.
5. Můžete vytvořit další koncový bod s názvem ep3, která poskytuje novou verzi experiment a trénovaného modelu.
6. Vraťte se ke kroku 3 v případě potřeby.

V průběhu času může mít mnoho koncové body vytvořené ve stejném webové službě. Každý koncový bod představuje kopii experimentu obsahující bodu v čase verzi trénovaného modelu bodu v čase. Pak můžete použít externí logiku k určení koncového bodu pro volání, což účinně znamená, že výběr verze vytrénovaného modelu pro vyhodnocení spuštění.

Můžete také vytvořit mnoho koncových bodů stejné webové služby a poté opravu různými verzemi soubor iLearner, který na koncový bod k dosažení podobný vliv. [Tento článek](create-models-and-endpoints-with-powershell.md) podrobně vysvětluje, jak provést tuto akci.

### <a name="new-web-service"></a>Nové webové služby
Pokud vytvoříte novou službu založené na Azure Resource Manageru, konstrukce koncového bodu již není k dispozici. Místo toho můžete vygenerovat definici (WSD) souborů webové služby, ve formátu JSON z prediktivní experiment s využitím [Export AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) použít rutinu Powershellu nebo pomocí [ *Export AzureRmMlWebservice* ](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice?view=azurermps-6.6.0) rutinu Powershellu ze služby nasazené webové využívající Resource Manager.

Až budete mít exportovaný soubor WSD a verze řídit, můžete taky nasadit WSD jako nové webové služby v plánu jiné webové služby v jiné oblasti Azure. Ujistěte se, že zadáte konfiguraci účtu úložiště správné, jakož i nové ID webové služby plánu. O opravu v různých iLearner soubory, můžete upravit soubor WSD a aktualizovat odkaz na umístění trénovaného modelu a nasadit ho jako nové webové služby.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizace nasazení a spuštění experimentu
Abyste mohli automatizovat proces nasazení aplikace a zpracování je důležitou součástí ALM. Ve službě Azure Machine Learning, lze provést pomocí [modulu PowerShell](https://aka.ms/amlps). Tady je příklad začátku do konce kroky, které jsou relevantní pro standardní ALM automatizovat proces spuštění a nasazení pomocí [modulu Powershellu pro Azure Machine Learning Studio](https://aka.ms/amlps). Každý krok je propojen s jeden nebo více rutin Powershellu, které můžete použít k provedení tohoto kroku.

1. [Nahrát datovou sadu](https://github.com/hning86/azuremlps#upload-amldataset).
2. Zkopírujte výukového experimentu do pracovního prostoru z [pracovní prostor](https://github.com/hning86/azuremlps#copy-amlexperiment) nebo z [Galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), nebo [importovat](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [exportovat](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment z místního disk.
3. [Aktualizace datové sady](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v výukového experimentu.
4. [Spuštění výukového experimentu](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Zvýšit úroveň trénovaného modelu](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Zkopírujte prediktivní experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) do pracovního prostoru.
7. [Aktualizovat trénovaného modelu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v prediktivní experiment.
8. [Spusťte experiment prediktivní](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Nasazení webové služby](https://github.com/hning86/azuremlps#new-amlwebservice) z prediktivního experimentu.
10. Test webové službě [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) nebo [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) koncového bodu.

## <a name="next-steps"></a>Další postup
* Stáhněte si [Azure Machine Learning Studio PowerShell](https://aka.ms/amlps) modulu a spuštění automatizovat úkoly ALM.
* Zjistěte, jak [vytvářet a spravovat velký počet modelů ML pomocí pouze jednoho experimentu](create-models-and-endpoints-with-powershell.md) prostřednictvím Powershellu a přetrénování rozhraní API.
* Další informace o [nasazování webových služeb Azure Machine Learning](publish-a-machine-learning-web-service.md).
