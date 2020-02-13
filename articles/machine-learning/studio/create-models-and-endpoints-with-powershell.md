---
title: Vytvoření více koncových bodů pro model
titleSuffix: ML Studio (classic) - Azure
description: Použití Powershellu k vytvoření více modelů Machine Learning a webových koncových bodů služby se stejný algoritmus, ale s jinou cvičných datových sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 9d94b6627348d08c76fa040255ba0856a2ffa945
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168811"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Použití PowerShellu k vytvoření modelů studia (Classic) a koncových bodů webové služby z jednoho experimentu

Tady je běžný problém machine learning: Chcete vytvořit mnoho modelů, které mají stejný pracovní postup školení a použít stejný algoritmus. Ale být vhodné tak, aby měly různé cvičných datových sad jako vstup. V tomto článku se dozvíte, jak to udělat ve velkém měřítku Azure Machine Learning Studio (Classic), a to pouze pomocí jednoho experimentu.

Řekněme například, že vlastníte firma franšízová pronájem globální kolo. Chcete sestavit regresní model k predikci poptávky pronájem na základě historických dat. Máte 1 000 pronájem umístění po celém světě a jste jste shromáždili datovou sadu pro každé umístění. Patří mezi ně důležité funkce, jako je datum, čas, počasí a provoz, které jsou specifické pro každé umístění.

Může trénování modelu jednou pomocí sloučené verze všech sad dat ve všech umístěních. Ale každý umístění má jedinečné prostředí. Lepším řešením proto by se k natrénování modelu regrese samostatně pomocí datovou sadu pro každé umístění. Tímto způsobem každý trénovaného modelu může vzít v úvahu velikosti jiném úložišti, svazek, zeměpisné oblasti, naplnění, kolo přívětivá provoz prostředí a další.

To může být nejlepší přístup, ale nechcete vytvářet 1 000 experimentů v Azure Machine Learning Studio (Classic), přičemž každý z nich představuje jedinečné umístění. Kromě toho tak velmi náročný úkol, je zřejmě neefektivní od každého testu bude mít stejné komponenty s výjimkou trénovací datové sady.

Naštěstí to můžete provést pomocí [rozhraní API pro přeškolení Azure Machine Learning Studio (Classic)](/azure/machine-learning/studio/retrain-machine-learning-model) a automatizací úlohy pomocí [prostředí PowerShell Azure Machine Learning Studio (Classic)](powershell-module.md).

> [!NOTE]
> Chcete-li ukázku rychleji, snižte počet umístění z 1 000 na 10. Ale stejné zásady a postupy platí i pro 1 000 umístění. Nicméně pokud chcete k trénování z datových sad, 1 000 můžete chtít při paralelním spuštění následujících skriptů prostředí PowerShell. Postup, který je nad rámec tohoto článku, ale najdete příklady prostředí PowerShell ukryta v síti Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Nastavit výukového experimentu
Použijte příklad [experimentu pro školení](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) , který je v [Cortana Intelligence Gallery](https://gallery.azure.ai). Otevřete tento experiment v pracovním prostoru [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) .

> [!NOTE]
> Pokud chcete postupovat podle tohoto příkladu, můžete použít standardní pracovní prostor, spíše než bezplatný pracovní prostor. Vytvořte jeden koncový bod pro každého zákazníka - celkem 10 koncových bodů – a standardní pracovní prostor, který vyžaduje, protože bezplatný pracovní prostor je omezená na 3 koncových bodů.
> 
> 

Experiment používá modul **Import dat** k importu datové sady školení *customer001. csv* z účtu úložiště Azure. Předpokládejme, že jste shromáždili školicí datové sady ze všech míst pronájmu kol a uložili je ve stejném umístění úložiště objektů BLOB s názvy souborů od *rentalloc001. csv* do *rentalloc10. csv*.

![Modul čtečky importuje data z objektu blob Azure.](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Všimněte si, že modul **výstupu webové služby** byl přidán do modulu **vlak model** .
Při nasazení tento experiment jako webové služby, koncový bod přidružený, že výstup ve formátu souboru .ilearner vrátí trénovaného modelu.

Všimněte si také, že jste nastavili parametr webové služby, který definuje adresu URL, kterou používá modul **Import dat** . To umožňuje zadat jednotlivé trénovací datové sady pro trénování modelu pro každé umístění, použijte parametr.
Existují jiné způsoby, může to uděláte. Dotaz SQL s parametrem webové služby můžete použít k získání dat z databáze SQL Azure. Nebo můžete použít vstupní modul **webové služby** k předání datové sady webové službě.

![Školený výstup modulu modelu do výstupního modulu webové služby](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teď spustíme tento experiment školení s použitím výchozí hodnoty *rental001. csv* jako školicí datovou sadu. Pokud zobrazíte výstup modulu **vyhodnocení** (klikněte na výstup a vyberte **vizualizovat**), uvidíte, že získáte dát výkon *AUC* = 0,91. V tuto chvíli jste připraveni nasadit webovou službu mimo tento výukový experiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>Nasazení trénování a vyhodnocování webové služby
Chcete-li nasadit webovou službu školení, klikněte na tlačítko **nastavit webovou službu** pod plátnem experimentu a vyberte **nasadit webovou službu**. Volání této webové služby "Kol pronájem školení".

Nyní je třeba nasadit hodnoticí webové služby.
Provedete to tak, že kliknete na tlačítko **nastavit webovou službu** pod plátnem a vyberete **prediktivní webovou službu**. Tím se vytvoří bodování experimentu.
Je potřeba provést několik drobné úpravy, aby to fungovalo jako webovou službu. Odebrat popisek sloupce "cnt" ze vstupních dat a omezují výstup pouze id instance a odpovídající předpovězené hodnoty.

Pokud to chcete uložit sami, můžete otevřít [prediktivní experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) v galerii, která už je připravená.

Pokud chcete nasadit webovou službu, spusťte prediktivní experiment a pak klikněte na tlačítko **nasadit webovou službu** pod plátnem. Název hodnoticí webové služby "Kol pronájem vyhodnocování".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Vytvoření 10 koncových bodů stejné webové služby pomocí prostředí PowerShell
Tato webová služba se dodává s výchozí koncový bod. Ale nejste zájem výchozí koncový bod, protože se nedá aktualizovat. Co je potřeba udělat, je vytvořit 10 další koncové body, jeden pro každé umístění. Můžete to provést v prostředí PowerShell.

Nejprve nastavíte prostředí PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Potom spusťte následující příkaz Powershellu:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Nyní jste vytvořili 10 koncových bodů a všechny obsahovaly stejný vycvičený model vyškolený na *customer001. csv*. Můžete je zobrazit na webu Azure Portal.

![Zobrazit seznam vycvičených modelů na portálu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizovat koncových bodů pro použití samostatné cvičných datových sad pomocí Powershellu
Dalším krokem je aktualizovat koncovým bodům modelů trénovaných jednoznačně na jednotlivé dat jednotlivých zákazníků. Ale nejdřív je potřeba tyto modely z webové služby pro **školení na nájemce** získat. Pojďme se vrátit k webové službě **školení na nájemce na kolo** . Je třeba volat její koncový bod BES 10krát s 10 různých cvičných datových sad cílem vytvořit 10 různých modelů. K tomu použijte rutinu prostředí PowerShell **InovkeAmlWebServiceBESEndpoint** .

Pro účet Blob Storage budete taky muset zadat přihlašovací údaje do `$configContent`. Konkrétně v polích `AccountName`, `AccountKey`a `RelativeLocation`. `AccountName` může být jeden ze svých názvů účtů, jak je vidět na **Azure Portal** (karta*úložiště* ). Po kliknutí na účet úložiště se jeho `AccountKey` dá najít tak, že v dolní části kliknete na tlačítko **Správa přístupových klíčů** a zkopírujete *Primární přístupový klíč*. `RelativeLocation` je cesta relativní k úložišti, kde bude uložen nový model. Například cesta `hai/retrain/bike_rental/` v následujícím skriptu odkazuje na kontejner s názvem `hai`a `/retrain/bike_rental/` jsou podsložky. V současné době nemůžete vytvářet podsložky prostřednictvím uživatelského rozhraní portálu, ale existuje [několik Azure Storage Explorer](../../storage/common/storage-explorers.md) , které vám to umožňují. Doporučujeme, abyste ve svém úložišti vytvořili nový kontejner pro uložení nových vyškolených modelů (soubory. iLearner) následujícím způsobem: na stránce úložiště klikněte na tlačítko **Přidat** v dolní části a pojmenujte ho `retrain`. V souhrnu se potřebné změny následujícího skriptu týkají `AccountName`, `AccountKey`a `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> Koncový bod BES je jediný režim podporovaný pro tuto operaci. RRS nelze použít pro produkční trénované modely.
> 
> 

Jak vidíte výše, místo vytváření 10 různých BES úlohy konfigurační soubory json, můžete dynamicky konfigurační řetězec místo toho vytvořte. Pak ho zavoláte do parametru *jobConfigString* rutiny **InvokeAmlWebServceBESEndpoint** . Ve skutečnosti není nutné udržovat kopie na disku.

Pokud vše proběhne správně, po chvíli byste měli vidět 10. iLearner soubory od *model001. iLearner* do *model010. iLearner*v účtu úložiště Azure. Nyní jste připraveni aktualizovat koncové body webové služby 10 bodování pomocí těchto modelů pomocí rutiny **patch-AmlWebServiceEndpoint** prostředí PowerShell. Mějte na paměti znovu, můžete pouze oprava jiné než výchozí koncové body, které prostřednictvím kódu programu vytvořili dříve.

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

To by měl spustit poměrně rychle. Po dokončení spuštění bude úspěšně jste vytvořili 10 koncových bodů prediktivní webové služby. Každý z nich bude obsahovat trénovaného modelu jednoznačně trénovaných na konkrétní datové sady do umístění pronájmů, vše z jednoho výukového experimentu. Pokud to chcete ověřit, můžete zkusit zavolat tyto koncové body pomocí rutiny **InvokeAmlWebServiceRRSEndpoint** a poskytnout jim stejná vstupní data. Měli byste očekávat zobrazíte různých predikované výsledky, protože jsou trénované modely s jinou školení sady.

## <a name="full-powershell-script"></a>Úplná skript prostředí PowerShell
Tady je seznam úplný zdrojový kód:

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
