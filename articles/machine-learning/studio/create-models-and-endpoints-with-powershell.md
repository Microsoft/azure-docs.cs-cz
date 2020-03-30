---
title: Vytvoření více koncových bodů pro model
titleSuffix: ML Studio (classic) - Azure
description: Pomocí prostředí PowerShell můžete vytvořit více modelů Machine Learning a koncových bodů webové služby se stejným algoritmem, ale s různými trénovacími datovými sadami.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218177"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Použití PowerShellu k vytvoření modelů Studio (klasické) a koncových bodů webové služby z jednoho experimentu

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Zde je běžný problém strojového učení: Chcete vytvořit mnoho modelů, které mají stejný pracovní postup školení a používat stejný algoritmus. Ale chcete, aby měli různé trénovací datové sady jako vstup. Tento článek ukazuje, jak to provést ve velkém měřítku v Azure Machine Learning Studio (klasické) pomocí pouze jeden experiment.

Řekněme například, že vlastníte globální franšízový obchod s půjčovnou kol. Chcete vytvořit regresní model předpovědět poptávku po pronájmu na základě historických dat. Máte 1000 míst pronájmu po celém světě a shromáždili jste datovou sadu pro každou lokalitu. Zahrnují důležité funkce, jako je datum, čas, počasí a provoz, které jsou specifické pro každé místo.

Model můžete trénovat jednou pomocí sloučené verze všech datových sad ve všech umístěních. Ale každá z vašich lokalit má jedinečné prostředí. Takže lepší přístup by trénovat regresní model samostatně pomocí datové sady pro každé umístění. Tímto způsobem by každý trénovaný model mohl vzít v úvahu různé velikosti obchodů, objem, geografii, populaci, dopravní prostředí šetrné k cyklistům a další.

To může být nejlepší přístup, ale nechcete vytvořit 1 000 školení experimenty v Azure Machine Learning Studio (klasické) s každým z nich představuje jedinečné umístění. Kromě toho, že ohromující úkol, také se zdá neefektivní, protože každý experiment by měl všechny stejné součásti s výjimkou trénovací datové sady.

Naštěstí toho můžete dosáhnout pomocí [rozhraní API pro rekvalifikaci Azure Machine Learning Studio (klasické)](/azure/machine-learning/studio/retrain-machine-learning-model) a automatizace úlohy pomocí [prostředí PowerShell (classic) Služby Azure Machine Learning Studio .](powershell-module.md)

> [!NOTE]
> Chcete-li, aby vaše ukázka běžela rychleji, snižte počet umístění z 1 000 na 10. Ale stejné zásady a postupy platí pro 1000 míst. Pokud však chcete trénovat z 1 000 datových sad, můžete chtít spustit následující skripty prostředí PowerShell paralelně. Jak to udělat, že je nad rámec tohoto článku, ale můžete najít příklady PowerShell multi-threading na Internetu.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Nastavení tréninkového experimentu
Použijte ukázkový [tréninkový experiment,](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) který je v [Galerii cortany inteligence](https://gallery.azure.ai). Otevřete tento experiment ve vašem pracovním prostoru [Azure Machine Learning Studio (klasické).](https://studio.azureml.net)

> [!NOTE]
> Chcete-li postupovat spolu s tímto příkladem, můžete použít standardní pracovní prostor spíše než volný pracovní prostor. Pro každého zákazníka vytvoříte jeden koncový bod – celkem 10 koncových bodů – a to vyžaduje standardní pracovní prostor, protože volný pracovní prostor je omezen na 3 koncové body.
> 
> 

Experiment používá modul **Import dat** k importu trénovací datové sady *customer001.csv* z účtu úložiště Azure. Předpokládejme, že jste shromáždili trénovací datové sady ze všech míst půjčovny kol a uložili je do stejného umístění úložiště objektů blob s názvy souborů od *rentalloc001.csv* až po *rentalloc10.csv*.

![Modul Čtečky importuje data z objektu blob Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Všimněte si, že výstupní modul **webové služby** byl přidán do modulu **Model vlaku.**
Když je tento experiment nasazen jako webová služba, koncový bod přidružený k tomuto výstupu vrátí trénovaný model ve formátu souboru ILearner.

Všimněte si také, že nastavíte parametr webové služby, který definuje adresu URL, kterou používá modul **Import dat.** To umožňuje použít parametr k určení jednotlivých trénovacích datových sad pro trénování modelu pro každé umístění.
Jsou i jiné způsoby, jak jsi to mohl udělat. K získání dat z databáze SQL Azure můžete použít dotaz SQL s parametrem webové služby. Nebo můžete použít vstupní modul **webové služby** k předání datové sady webové službě.

![Výstupy modulu Trénovaného modelu do výstupního modulu webové služby](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nyní spusťme tento trénovací experiment pomocí výchozí hodnoty *rental001.csv* jako trénovací datové sady. Pokud si prohlížíte výstup modulu **Vyhodnocení** (klikněte na výstup a vyberte **Možnost Visualize**), uvidíte, že máte slušný výkon *AUC* = 0,91. V tomto okamžiku jste připraveni nasadit webovou službu z tohoto trénovacího experimentu.

## <a name="deploy-the-training-and-scoring-web-services"></a>Nasazení školení a vyhodnocování webových služeb
Chcete-li nasadit školicí webovou službu, klepněte na tlačítko **Nastavit webovou službu** pod plátnem experimentu a vyberte **možnost Nasadit webovou službu**. Volejte tuto webovou službu "Bike Rental Training".

Nyní je třeba nasadit vyhodnocování webové služby.
Chcete-li to provést, klepněte pod plátnem na **položku Nastavit webovou službu** a vyberte možnost **Prediktivní webová služba**. Tím se vytvoří bodovací experiment.
Je třeba provést několik drobných úprav, aby to fungovalo jako webová služba. Odeberte sloupec popisku "cnt" ze vstupních dat a omezte výstup pouze na id instance a odpovídající předpokládanou hodnotu.

Chcete-li si tuto práci ušetřit, můžete otevřít [prediktivní experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) v galerii, která již byla připravena.

Chcete-li nasadit webovou službu, spusťte prediktivní experiment a potom klepněte na tlačítko **Nasadit webovou službu** pod plátnem. Název bodování webové služby "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Vytvoření 10 identických koncových bodů webové služby pomocí prostředí PowerShell
Tato webová služba je dodávána s výchozím koncovým bodem. Ale nemáte zájem o výchozí koncový bod, protože jej nelze aktualizovat. Co musíte udělat, je vytvořit 10 dalších koncových bodů, jeden pro každé umístění. Můžete to udělat s PowerShell.

Nejprve nastavíte prostředí Prostředí PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Potom spusťte následující příkaz Prostředí PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nyní jste vytvořili 10 koncových bodů a všechny obsahují stejný trénovaný model vyškolený na *customer001.csv*. Můžete je zobrazit na webu Azure Portal.

![Zobrazit seznam trénovaných modelů na portálu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizace koncových bodů pro použití samostatných trénovacích datových sad pomocí Prostředí PowerShell
Dalším krokem je aktualizace koncových bodů pomocí modelů, které jsou jedinečně trénované na jednotlivých datech každého zákazníka. Ale nejprve je třeba vyrábět tyto modely z **půjčovny kol školení** webové služby. Vraťme se k webové službě **Bike Rental Training.** Je třeba volat jeho BES koncový bod 10 krát s 10 různých trénovacích datových sad, aby bylo možné vytvořit 10 různých modelů. K tomu použijte rutinu prostředí **InovkeAmlWebServiceBESEndpoint** PowerShell.

Budete také muset zadat přihlašovací údaje pro váš `$configContent`účet úložiště objektů blob do . Konkrétně na polích `AccountName` `AccountKey`, `RelativeLocation`a . Může `AccountName` to být jeden z názvů vašich účtů, jak je vidět na **kartě Azure Portal** *(Storage).* Jakmile kliknete na účet `AccountKey` úložiště, jeho lze nalézt stisknutím tlačítka **Spravovat přístupové klávesy** v dolní části a zkopírováním *primárního přístupového klíče*. Je `RelativeLocation` cesta vzhledem k úložišti, kde bude uložen nový model. Cesta `hai/retrain/bike_rental/` v následujícím skriptu například odkazuje `hai`na `/retrain/bike_rental/` kontejner s názvem a jsou podsložky. V současné době nelze vytvořit podsložky prostřednictvím portálu ui, ale existuje [několik Průzkumníků úložiště Azure,](../../storage/common/storage-explorers.md) které vám umožní tak učinit. Doporučujeme vytvořit nový kontejner ve vašem úložišti pro uložení nových trénovaných modelů (.iLearner soubory) takto: na `retrain`stránce úložiště, klikněte na tlačítko **Přidat** v dolní části a pojmenujte ji . Souhrnně lze shrnout, že nezbytné změny `AccountName` `AccountKey`následujícího `RelativeLocation` skriptu se budou vystihovat do , , a (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> Koncový bod BES je jediným podporovaným režimem pro tuto operaci. RRS nelze použít k výrobě trénovaných modelů.
> 
> 

Jak můžete vidět výše, namísto vytváření 10 různých bes konfigurace úlohjson soubory, můžete dynamicky vytvořit config řetězec místo. Pak jej předejte parametru *jobConfigString* rutiny **InvokeAmlWebServceBESEndpoint.** Opravdu není třeba uchovávat kopii na disku.

Pokud vše půjde dobře, po chvíli byste měli vidět 10 .iLearner soubory, od *model001.ilearner* na *model010.ilearner*, ve vašem účtu úložiště Azure. Nyní jste připraveni aktualizovat koncové body webové služby 10 s 10 bodování pomocí těchto modelů pomocí rutiny **Patch-AmlWebServiceEndpoint** PowerShell. Nezapomeňte znovu, že můžete opravit pouze non-výchozí koncové body, které programově vytvořené dříve.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

To by mělo běžet poměrně rychle. Po dokončení provádění budete úspěšně vytvořeny 10 koncových bodů prediktivní webové služby. Každý z nich bude obsahovat trénovaný model jedinečně trénovaný na datové sadě specifické pro místo pronájmu, to vše z jednoho trénovacího experimentu. Chcete-li to ověřit, můžete zkusit volat tyto koncové body pomocí rutiny **InvokeAmlWebServiceRRSEndpoint,** která jim poskytuje stejná vstupní data. Měli byste očekávat, že různé výsledky předpovědi, protože modely jsou trénované s různými sadami školení.

## <a name="full-powershell-script"></a>Úplný skript prostředí PowerShell
Zde je seznam úplného zdrojového kódu:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
