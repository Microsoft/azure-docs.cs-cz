---
title: 'ML Studio (Classic): Správa životního cyklu aplikací – Azure'
description: Použití osvědčených postupů pro správu životního cyklu aplikací v Azure Machine Learning Studio (Classic)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518398"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Správa životního cyklu aplikací v Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (Classic) je nástroj pro vývoj experimentů strojového učení, které jsou provozované na cloudové platformě Azure. Vypadá to, že integrované vývojové prostředí (IDE) sady Visual Studio a Škálovatelná cloudová služba se sloučí do jediné platformy. Můžete začlenit standardní postupy správy životního cyklu aplikací (ALM) od různých prostředků k automatizovanému spouštění a nasazování do Azure Machine Learning Studio (Classic). Tento článek popisuje některé z možností a přístupů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experiment se správou verzí
Existují dva doporučené způsoby verze experimentů. Můžete buď spoléhat na vestavěnou historii spuštění, nebo exportovat experiment ve formátu JSON, abyste ho mohli externě spravovat. Každý přístup se dodává s jeho specialisty a nevýhody.

### <a name="experiment-snapshots-using-run-history"></a>Experimentování snímků pomocí historie spuštění
V modelu spuštění výukového experimentu pro Azure Machine Learning Studio (Classic) se do plánovače úloh odesílá neproměnlivý snímek experimentu pokaždé, když v editoru experimentů kliknete na **Spustit** . Chcete-li zobrazit tento seznam snímků, klikněte na panelu příkazů v zobrazení Editor experimentů na příkaz **Spustit historii** .

![Tlačítko historie spuštění](./media/version-control/runhistory.png)

Snímek můžete otevřít v uzamčeném režimu kliknutím na název experimentu v době, kdy byl experiment odeslán a byl proveden snímek. Všimněte si, že pouze první položka v seznamu, která představuje aktuální experiment, je ve upravitelném stavu. Všimněte si také, že každý snímek může být v různých stavových stavech, včetně dokončeného (částečného spuštění), selhání, selhání (částečného spuštění) nebo konceptu.

![Spustit seznam historie](./media/version-control/runhistorylist.png)

Po otevření můžete experiment snímků uložit jako nový experiment a pak ho upravit. Pokud snímek experimentu obsahuje prostředky, jako jsou například školené modely, transformace nebo datové sady, které mají aktualizované verze, snímek při pořízení snímku zachová odkazy na původní verzi. Pokud uložíte uzamčený snímek jako nový experiment, Azure Machine Learning Studio (Classic) zjistí existenci novější verze těchto assetů a automaticky je aktualizuje v novém experimentu.

Při odstranění experimentu se odstraní všechny snímky tohoto experimentu.

### <a name="exportimport-experiment-in-json-format"></a>Experiment pro export/import ve formátu JSON
Snímky historie spuštění udržují neproměnlivou verzi experimentu v Azure Machine Learning Studio (Classic) pokaždé, když se odešle ke spuštění. Můžete také uložit místní kopii experimentu a vrátit ji se změnami do svého oblíbeného systému správy zdrojového kódu, jako je například Team Foundation Server a později znovu vytvořit experiment z tohoto místního souboru. K tomu můžete použít [Azure Machine Learning PowerShell](https://aka.ms/amlps) rutin [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) a [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) .

Soubor JSON je textová reprezentace grafu experimentu, která může obsahovat odkaz na prostředky v pracovním prostoru, jako je například datová sada nebo trained model. Neobsahuje serializovanou verzi assetu. Pokud se pokusíte importovat dokument JSON zpátky do pracovního prostoru, musí již existovat odkazované prostředky se stejnými ID assetů, na které se odkazuje v experimentu. V opačném případě nemůžete získat přístup k importovanému experimentu.

## <a name="versioning-trained-model"></a>Model vyškolený pro správu verzí
Vyškolený model v Azure Machine Learning Studio (Classic) je serializován do formátu označovaného jako soubor iLearner ( `.iLearner` ) a je uložen v účtu služby Azure Blob Storage, který je přidružený k pracovnímu prostoru. Jedním ze způsobů, jak získat kopii souboru iLearner, je prostřednictvím rozhraní API pro přeškolení. [Tento článek](./retrain-machine-learning-model.md) vysvětluje, jak funguje rozhraní API pro přeškolení. Postup vysoké úrovně:

1. Nastavte experiment pro školení.
2. Přidejte výstupní port webové služby do modulu výuka modelu nebo modul, který vytváří trained model, jako je například předparametr ladění modelu nebo vytvoření modelu R.
3. Spusťte experiment pro školení a pak ho nasaďte jako webovou službu pro školení modelů.
4. Zavolejte koncový bod BES webové služby školení a zadejte požadovaný název souboru iLearner a umístění účtu úložiště objektů blob, kde bude uložený.
5. Vyshromáždění vytvořeného souboru iLearner po dokončení volání BES

Dalším způsobem, jak načíst soubor iLearner, je prostřednictvím PowerShellu rutiny [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). To může být jednodušší, pokud chcete získat kopii souboru iLearner bez nutnosti přeškolit model programově.

Až budete mít soubor iLearner obsahující trained model, můžete využít vlastní strategii správy verzí. Tato strategie může být jednoduchá, protože jako konvence pojmenování používá příponu typu pre/přípona a právě opouští soubor iLearner v úložišti objektů BLOB nebo ho kopíruje nebo importuje do systému správy verzí.

Uložený soubor iLearner se pak dá použít k vyhodnocování prostřednictvím nasazených webových služeb.

## <a name="versioning-web-service"></a>Webová služba správy verzí
Můžete nasadit dva typy webových služeb z Azure Machine Learning Studio (klasický) experiment. Klasická webová služba je úzce spojena s experimentem a pracovním prostorem. Nová webová služba používá rozhraní Azure Resource Manager a již není spojena s původním experimentem nebo pracovním prostorem.

### <a name="classic-web-service"></a>Klasická webová služba
Chcete-li používat verzi klasické webové služby, můžete využít konstrukci koncových bodů webové služby. Tady je typický tok:

1. Z prediktivního experimentu nasadíte novou klasickou webovou službu, která obsahuje výchozí koncový bod.
2. Vytvoříte nový koncový bod s názvem EP2, který zpřístupňuje aktuální verzi modelu experiment/trained.
3. Vrátíte se zpátky a aktualizujete prediktivní a vycvičený model experimentování.
4. Můžete znovu nasadit prediktivní experiment, který pak aktualizuje výchozí koncový bod. Nemění se ale EP2.
5. Vytvoříte další koncový bod s názvem EP3, který zpřístupňuje novou verzi experimentu a trained model.
6. V případě potřeby se vraťte ke kroku 3.

V průběhu času může být ve stejné webové službě vytvořeno mnoho koncových bodů. Každý koncový bod představuje kopii experimentu k určitému bodu v čase, který obsahuje verzi výukového modelu k určitému bodu v čase. Pak můžete použít externí logiku k určení, který koncový bod se má zavolat, což efektivně znamená výběr verze trained model pro spuštění bodování.

Můžete také vytvořit mnoho identických koncových bodů webové služby a poté opravit různé verze souboru iLearner do koncového bodu, abyste dosáhli podobného efektu. [Tento článek](create-models-and-endpoints-with-powershell.md) podrobně vysvětluje, jak to provést.

### <a name="new-web-service"></a>Nová webová služba
Pokud vytvoříte novou Azure Resource Manager webovou službu, konstrukce koncového bodu již nebude k dispozici. Místo toho můžete vygenerovat soubory WSD (Web Service Definition) ve formátu JSON z prediktivního experimentu pomocí rutiny [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell rutiny nebo pomocí rutiny [*Export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell rutiny z nasazené webové služby založené na správce prostředků.

Až budete mít exportovaný soubor WSD a správu verzí, můžete ho nasadit taky jako novou webovou službu v jiném plánu webové služby v jiné oblasti Azure. Stačí se ujistit, že jste zadali správnou konfiguraci účtu úložiště i nové ID plánu Web Service. Chcete-li opravit v různých souborech iLearner, můžete upravit soubor WSD a aktualizovat odkaz na umístění pro vyškolený model a nasadit ho jako novou webovou službu.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizace provádění a nasazování experimentů
Důležitým aspektem ALM je umožnit automatizaci procesu provádění a nasazení aplikace. V Azure Machine Learning Studio (Classic) to můžete provést pomocí [modulu PowerShellu](https://aka.ms/amlps). Tady je příklad komplexních kroků, které jsou důležité pro standardní proces spuštění nebo nasazení ALM pomocí [modulu PowerShellu pro Azure Machine Learning Studio (Classic)](https://aka.ms/amlps). Každý krok je propojený s jedním nebo více rutiny prostředí PowerShell, které můžete použít k provedení tohoto kroku.

1. [Nahrajte datovou sadu](https://github.com/hning86/azuremlps#upload-amldataset).
2. Do pracovního prostoru zkopírujte experiment z [pracovního prostoru](https://github.com/hning86/azuremlps#copy-amlexperiment) nebo z [Galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)nebo [naimportujte](https://github.com/hning86/azuremlps#import-amlexperimentgraph) [exportovaný](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment z místního disku.
3. [Aktualizujte datovou sadu](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) v experimentu školení.
4. [Spusťte experiment školení](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Zvyšte úroveň poučeného modelu](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopírovat prediktivní experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) do pracovního prostoru.
7. [Aktualizujte školený model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) ve prediktivní experimentu.
8. [Spusťte prediktivní experiment](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Nasazení webové služby](https://github.com/hning86/azuremlps#new-amlwebservice) z prediktivního experimentu.
10. Otestujte [záznamy prostředků](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) nebo [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) koncového bodu webové služby.

## <a name="next-steps"></a>Další kroky
* Stáhněte modul [PowerShellu pro Azure Machine Learning Studio (Classic)](https://aka.ms/amlps) a začněte AUTOMATIZOVAT úlohy Alm.
* Naučte se [vytvářet a spravovat velký počet modelů ml pomocí jediného experimentu](create-models-and-endpoints-with-powershell.md) prostřednictvím PowerShellu a rozhraní API pro přeškolení.
* Přečtěte si další informace o [nasazení Azure Machine Learning webových služeb](deploy-a-machine-learning-web-service.md).