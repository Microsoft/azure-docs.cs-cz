---
Název: Vytvoření více modelů z jednoho experimentu titleSuffix Studio: Azure Machine Learning Studio Popis: Použití Powershellu k vytvoření více modelů Machine Learning a webových koncových bodů služby se stejný algoritmus, ale s jinou cvičných datových sad.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04/04/2017
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Použití Powershellu k vytvoření Studio modely a koncových bodů webové služby z jednoho experimentu

Tady je běžný problém machine learning: Chcete vytvořit mnoho modelů, které mají stejný pracovní postup školení a použít stejný algoritmus. Ale být vhodné tak, aby měly různé cvičných datových sad jako vstup. Tento článek ukazuje, jak to udělat ve velkém měřítku v Azure Machine Learning Studio pomocí pouze jednoho experimentu.

Řekněme například, že vlastníte firma franšízová pronájem globální kolo. Chcete sestavit regresní model k predikci poptávky pronájem na základě historických dat. Máte 1 000 pronájem umístění po celém světě a jste jste shromáždili datovou sadu pro každé umístění. Patří mezi ně důležité funkce, jako je datum, čas, počasí a provoz, které jsou specifické pro každé umístění.

Může trénování modelu jednou pomocí sloučené verze všech sad dat ve všech umístěních. Ale každý umístění má jedinečné prostředí. Lepším řešením proto by se k natrénování modelu regrese samostatně pomocí datovou sadu pro každé umístění. Tímto způsobem každý trénovaného modelu může vzít v úvahu velikosti jiném úložišti, svazek, zeměpisné oblasti, naplnění, kolo přívětivá provoz prostředí a další.

Který může být nejlepším řešením, ale nechcete, aby k vytváření experimentů 1 000 školení ve službě Azure Machine Learning s každou z nich představující jedinečné umístění. Kromě toho tak velmi náročný úkol, je zřejmě neefektivní od každého testu bude mít stejné komponenty s výjimkou trénovací datové sady.

Naštěstí to můžete provést pomocí [přetrénování rozhraní API Azure Machine Learning](retrain-models-programmatically.md) a automatizace úloh s [Azure Machine Learning PowerShell](powershell-module.md).

> [!NOTE]
> Chcete-li ukázku rychleji, snižte počet umístění z 1 000 na 10. Ale stejné zásady a postupy platí i pro 1 000 umístění. Nicméně pokud chcete k trénování z datových sad, 1 000 můžete chtít při paralelním spuštění následujících skriptů prostředí PowerShell. Postup, který je nad rámec tohoto článku, ale najdete příklady prostředí PowerShell ukryta v síti Internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Nastavit výukového experimentu
Použijte tento příklad [výukového experimentu](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) , který je [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Otevřete tento experiment v vaše [Azure Machine Learning Studio](https://studio.azureml.net) pracovního prostoru.

> [!NOTE]
> Pokud chcete postupovat podle tohoto příkladu, můžete použít standardní pracovní prostor, spíše než bezplatný pracovní prostor. Vytvořte jeden koncový bod pro každého zákazníka - celkem 10 koncových bodů – a standardní pracovní prostor, který vyžaduje, protože bezplatný pracovní prostor je omezená na 3 koncových bodů. Pokud máte jenom bezplatný pracovní prostor, stačí změňte skripty umožňující pouze obsah za umístění.
> 
> 

Experiment používá **importovat Data** modulu k importu trénovací datové sady *customer001.csv* z účtu služby Azure storage. Předpokládejme, máte shromážděné ze všech umístění pronájem kol cvičných datových sad a je uložená ve stejném umístění úložiště objektů blob s názvy souborů od *rentalloc001.csv* k *rentalloc10.csv*.

![image](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Všimněte si, že **webové služby výstup** modul byl přidán do **Train Model** modulu.
Při nasazení tento experiment jako webové služby, koncový bod přidružený, že výstup ve formátu souboru .ilearner vrátí trénovaného modelu.

Všimněte si také, že nastavíte parametr webové služby, který určuje adresu URL, která **Import dat** používá modul. To umožňuje zadat jednotlivé trénovací datové sady pro trénování modelu pro každé umístění, použijte parametr.
Existují jiné způsoby, může to uděláte. Dotaz SQL s parametrem webové služby můžete použít k získání dat z databáze SQL Azure. Nebo můžete použít **vstup webové služby** modul pro předávání v datové sadě k webové službě.

![image](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teď spustíme tohoto výukového experimentu s výchozí hodnotou *rental001.csv* jako trénovací datové sady. Je-li zobrazit výstup **vyhodnotit** modulu (klikněte na výstupní a vyberte **vizualizovat**), zobrazí se vám vrazíme výkon *AUC* = 0.91. V tuto chvíli jste připraveni nasadit webovou službu mimo tento výukový experiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>Nasazení trénování a vyhodnocování webové služby
Nasazení webové služby školení, klikněte na tlačítko **nastavení webové služby** tlačítko na plátno experimentu a vyberte **nasadit webovou službu**. Volání této webové služby "Kol pronájem školení".

Nyní je třeba nasadit hodnoticí webové služby.
Chcete-li to provést, klikněte na tlačítko **nastavení webové služby** dole na plátně a vyberte **prediktivní webová služba**. Tím se vytvoří bodování experimentu.
Je potřeba provést několik drobné úpravy, aby to fungovalo jako webovou službu. Odebrat popisek sloupce "cnt" ze vstupních dat a omezují výstup pouze id instance a odpovídající předpovězené hodnoty.

Sami si práci uložit, můžete otevřít [prediktivní experiment](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) v galerii, která je už připraven.

Nasadit webovou službu, spustit prediktivní experiment a klikněte **nasazení webové služby** tlačítko dole na plátně. Název hodnoticí webové služby "Kol pronájem vyhodnocování".

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

Nyní jste vytvořili 10 koncových bodů a všechny obsahují stejné Trénink modelů trénovaných na *customer001.csv*. Můžete je zobrazit na webu Azure Portal.

![image](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizovat koncových bodů pro použití samostatné cvičných datových sad pomocí Powershellu
Dalším krokem je aktualizovat koncovým bodům modelů trénovaných jednoznačně na jednotlivé dat jednotlivých zákazníků. Ale nejdřív je potřeba vytvořit tyto modely **kol pronájem školení** webové služby. Vraťme se k **kol pronájem školení** webové služby. Je třeba volat její koncový bod BES 10krát s 10 různých cvičných datových sad cílem vytvořit 10 různých modelů. Použití **InovkeAmlWebServiceBESEndpoint** to udělat pomocí rutiny Powershellu.

Budete taky muset zadat přihlašovací údaje pro svůj účet blob storage do `$configContent`. Konkrétně v polích `AccountName`, `AccountKey`, a `RelativeLocation`. `AccountName` Může být jedna z vašich názvy účtů, jak je vidět **webu Azure portal** (*úložiště* kartu). Po kliknutí na účet úložiště, jeho `AccountKey` najdete stisknutím kombinace kláves **spravovat přístupové klíče** tlačítko v dolní části a kopírování *primární přístupový klíč*. `RelativeLocation` Je cesta relativní vůči úložiště, kam se má uložit nový model. Například cesta `hai/retrain/bike_rental/` v následující skript odkazuje na kontejner s názvem `hai`, a `/retrain/bike_rental/` podsložky. V současné době nelze vytvořit podsložky přes uživatelské rozhraní portálu, ale existují [několik průzkumníky úložiště Azure](../../storage/common/storage-explorers.md) , které umožňují udělat. Doporučujeme vytvořit nový kontejner ve službě storage k ukládání nového trénované modely (soubory .iLearner) takto: na stránce úložiště, klikněte na tlačítko **přidat** tlačítko v dolní části a pojmenujte ho `retrain`. Stručně řečeno, potřebné změny, které tento skript se týkají `AccountName`, `AccountKey`, a `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Jak vidíte výše, místo vytváření 10 různých BES úlohy konfigurační soubory json, můžete dynamicky konfigurační řetězec místo toho vytvořte. Klikněte na informační kanál *jobConfigString* parametr **InvokeAmlWebServceBESEndpoint** rutiny. Ve skutečnosti není nutné udržovat kopie na disku.

Pokud všechno proběhne správně, po chvíli zobrazí se 10 .iLearner souborů z *model001.ilearner* k *model010.ilearner*, ve vašem účtu úložiště Azure. Nyní jste připraveni k aktualizaci 10 vyhodnocování koncových bodů webové služby pomocí těchto modelů pomocí **Patch-AmlWebServiceEndpoint** rutiny Powershellu. Mějte na paměti znovu, můžete pouze oprava jiné než výchozí koncové body, které prostřednictvím kódu programu vytvořili dříve.

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

To by měl spustit poměrně rychle. Po dokončení spuštění bude úspěšně jste vytvořili 10 koncových bodů prediktivní webové služby. Každý z nich bude obsahovat trénovaného modelu jednoznačně trénovaných na konkrétní datové sady do umístění pronájmů, vše z jednoho výukového experimentu. Chcete-li to ověřit, můžete zkusit volání těchto koncových bodů pomocí **InvokeAmlWebServiceRRSEndpoint** rutiny, přičemž jim poskytují stejnými vstupními daty. Měli byste očekávat zobrazíte různých predikované výsledky, protože jsou trénované modely s jinou školení sady.

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
