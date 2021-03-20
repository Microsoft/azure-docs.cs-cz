---
title: 'ML Studio (Classic): vytvoření více &ch koncových bodů modelu – Azure'
description: Pomocí PowerShellu můžete vytvořit několik modelů Machine Learning a koncových bodů webové služby se stejným algoritmem, ale s různými školicími datovými sadami.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 35b5fe4556f1d557d3fc0420e9069f2fb510eec4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520506"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Vytvoření více koncových bodů webové služby z jednoho experimentu pomocí ML Studio (Classic) a PowerShellu

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Tady je běžný problém strojového učení: chcete vytvořit mnoho modelů, které mají stejný pracovní postup školení a používat stejný algoritmus. Ale chcete, aby jako vstup měly jiné školicí datové sady. V tomto článku se dozvíte, jak to udělat ve velkém měřítku Azure Machine Learning Studio (Classic), a to pouze pomocí jednoho experimentu.

Řekněme například, že vlastníte globální pronájem kol. Chcete vytvořit regresní model pro předpověď požadavků na pronájem na základě historických dat. Máte 1 000 míst pronájmu po celém světě a shromáždili jste datovou sadu pro každé umístění. Zahrnují důležité funkce, jako je datum, čas, počasí a provoz, který je specifický pro každé umístění.

Model můžete vytvořit jednou pomocí sloučené verze všech datových sad ve všech umístěních. Každé umístění má ale jedinečné prostředí. Lepším řešením je proto vytvoření regresního modelu samostatně pomocí datové sady pro každé umístění. Každý vyškolený model pak může vzít v úvahu různé velikosti úložiště, objem, zeměpisná místa, naplnění provozu a prostředí, které je uživatelsky přívětivé.

To může být nejlepší přístup, ale nechcete vytvářet 1 000 experimentů v Azure Machine Learning Studio (Classic), přičemž každý z nich představuje jedinečné umístění. Kromě toho, že je úkol zahlcený, se také zdá být neefektivní, protože každý experiment by měl všechny stejné komponenty s výjimkou datové sady školení.

Naštěstí to můžete provést pomocí [rozhraní API pro přeškolení Azure Machine Learning Studio (Classic)](./retrain-machine-learning-model.md) a automatizací úlohy pomocí [prostředí PowerShell Azure Machine Learning Studio (Classic)](powershell-module.md).

> [!NOTE]
> Chcete-li spustit ukázku rychleji, snižte počet umístění od 1 000 do 10. Stejné principy a postupy se ale vztahují i na 1 000 umístění. Pokud ale chcete vytvářet výuku z 1 000 datových sad, můžete chtít spustit následující skripty PowerShellu paralelně. Postup je nad rámec tohoto článku, ale můžete najít příklady prostředí PowerShell s více vlákny na internetu.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Nastavení experimentu školení
Použijte příklad [experimentu pro školení](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) , který je v [Cortana Intelligence Gallery](https://gallery.azure.ai). Otevřete tento experiment v pracovním prostoru [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) .

> [!NOTE]
> Aby bylo možné postupovat podle tohoto příkladu, můžete chtít použít standardní pracovní prostor místo bezplatného pracovního prostoru. Můžete vytvořit jeden koncový bod pro každého zákazníka – celkem 10 koncových bodů, který vyžaduje standardní pracovní prostor, protože bezplatný pracovní prostor je omezený na 3 koncové body.
> 
> 

Experiment používá modul **Import dat** k importu *customer001.csv* dat školení z účtu služby Azure Storage. Předpokládejme, že jste shromáždili školicí datové sady ze všech umístění půjčovny kol a uložili je do stejného umístění úložiště objektů BLOB s názvy souborů, které jsou v rozsahu od *rentalloc001.csv* do *rentalloc10.csv*.

![Modul čtečky importuje data z objektu blob Azure.](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Všimněte si, že modul **výstupu webové služby** byl přidán do modulu **vlak model** .
Pokud je tento experiment nasazen jako webová služba, koncový bod spojený s tímto výstupem vrátí školený model ve formátu souboru. ilearner.

Všimněte si také, že jste nastavili parametr webové služby, který definuje adresu URL, kterou používá modul **Import dat** . To umožňuje použít parametr k určení jednotlivých školicích datových sad pro výuku modelu pro každé umístění.
Existují i jiné způsoby, jak to provést. K získání dat z databáze v Azure SQL Database můžete použít dotaz SQL s parametrem webové služby. Nebo můžete použít vstupní modul  **webové služby** k předání datové sady webové službě.

![Školený výstup modulu modelu do výstupního modulu webové služby](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nyní spusťte tento experiment školení s použitím výchozí hodnoty *rental001.csv* jako školicí datovou sadu. Pokud zobrazíte výstup modulu **vyhodnocení** (klikněte na výstup a vyberte **vizualizovat**), uvidíte, že získáte dát výkon *AUC* = 0,91. V tuto chvíli jste připraveni nasadit webovou službu z tohoto zkušebního experimentu.

## <a name="deploy-the-training-and-scoring-web-services"></a>Nasazení webových služeb školení a bodování
Chcete-li nasadit webovou službu školení, klikněte na tlačítko **nastavit webovou službu** pod plátnem experimentu a vyberte **nasadit webovou službu**. Zavolejte tuto webovou službu školení pro nájem kol.

Teď je potřeba nasadit webovou službu bodování.
Provedete to tak, že kliknete na tlačítko **nastavit webovou službu** pod plátnem a vyberete **prediktivní webovou službu**. Tím se vytvoří experiment pro bodování.
Je nutné provést několik menších úprav, aby fungovaly jako webové služby. Odebere sloupec Label "CNT" ze vstupních dat a omezí výstup pouze na ID instance a odpovídající předpovězenou hodnotu.

Pokud to chcete uložit sami, můžete otevřít [prediktivní experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) v galerii, která už je připravená.

Pokud chcete nasadit webovou službu, spusťte prediktivní experiment a pak klikněte na tlačítko **nasadit webovou službu** pod plátnem. Pojmenujte webovou službu bodování "bodování pronájmu kol".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Vytvoření 10 identických koncových bodů webové služby pomocí PowerShellu
Tato webová služba je dodávána s výchozím koncovým bodem. Ale nebudete tak mít zájem o výchozí koncový bod, protože ho nejde aktualizovat. K tomu, co potřebujete, je třeba vytvořit 10 dalších koncových bodů, jednu pro každé umístění. Můžete to provést pomocí PowerShellu.

Nejdřív nastavíte prostředí PowerShell:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Pak spusťte následující příkaz prostředí PowerShell:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

Nyní jste vytvořili 10 koncových bodů a všechny obsahovaly stejný vycvičený model vyškolený v *customer001.csv*. Můžete je zobrazit v Azure Portal.

![Zobrazit seznam vycvičených modelů na portálu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizace koncových bodů pro použití samostatných školicích datových sad pomocí prostředí PowerShell
Dalším krokem je aktualizace koncových bodů pomocí modelů jednoznačně vyškolených na jednotlivá data každého zákazníka. Ale nejdřív je potřeba tyto modely z webové služby pro **školení na nájemce** získat. Pojďme se vrátit k webové službě **školení na nájemce na kolo** . Abyste mohli vydávat 10 různých modelů, je potřeba zavolat svůj koncový bod BES na 10 s 10 různými školicími datovými sadami. K tomu použijte rutinu prostředí PowerShell **InovkeAmlWebServiceBESEndpoint** .

Také budete muset zadat přihlašovací údaje pro účet úložiště objektů blob do služby `$configContent` . Konkrétně v polích, `AccountName` `AccountKey` a `RelativeLocation` . `AccountName`Může to být jeden z názvů vašich účtů, jak je vidět na **Azure Portal** (karta *úložiště* ). Jakmile kliknete na účet úložiště, najdete ho `AccountKey` tak, že v dolní části stisknete tlačítko **Správa přístupových klíčů** a zkopírujete *Primární přístupový klíč*. `RelativeLocation`Je cesta relativní k úložišti, kde bude uložen nový model. Například cesta `hai/retrain/bike_rental/` v následujícím skriptu odkazuje na kontejner s názvem `hai` a `/retrain/bike_rental/` jsou podsložky. V současné době nemůžete vytvářet podsložky prostřednictvím uživatelského rozhraní portálu, ale existuje [několik Azure Storage Explorer](../../storage/common/storage-explorers.md) , které vám to umožňují. Doporučujeme vytvořit v úložišti nový kontejner pro uložení nových proučených modelů (soubory. iLearner) následujícím způsobem: na stránce úložiště klikněte na tlačítko **Přidat** v dolní části a pojmenujte ho `retrain` . V souhrnu se potřebné změny následujícího skriptu týkají `AccountName` , `AccountKey` a `RelativeLocation` (: `"retrain/model' + $seq + '.ilearner"` ).

```powershell
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
```

> [!NOTE]
> Pro tuto operaci je BES koncový bod jediným podporovaným režimem. Záznamy o prostředcích se nedají použít k produkci vycvičených modelů.
> 
> 

Jak vidíte výše, místo sestavování 10 různých souborů JSON konfigurace úlohy BES dynamicky vytvoříte konfigurační řetězec. Pak ho zavoláte do parametru *jobConfigString* rutiny **InvokeAmlWebServceBESEndpoint** . Není nutné uchovávat kopii na disku.

Pokud vše proběhne správně, po chvíli byste měli vidět 10. iLearner soubory od *model001. iLearner* do *model010. iLearner* v účtu úložiště Azure. Nyní jste připraveni aktualizovat koncové body webové služby 10 bodování pomocí těchto modelů pomocí rutiny **patch-AmlWebServiceEndpoint** prostředí PowerShell. Nezapomeňte znovu, abyste mohli opravovat jenom nevýchozí koncové body, které jste programově vytvořili dříve.

```powershell
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
```

To by mělo běžet poměrně rychle. Až se spuštění dokončí, úspěšně se vytvoří 10 prediktivních koncových bodů webové služby. Každá z nich bude obsahovat vyškolený model jednoznačně vyškolený pro datovou sadu specifickou pro umístění pronájmu, a to vše z jednoho školicího experimentu. Pokud to chcete ověřit, můžete zkusit zavolat tyto koncové body pomocí rutiny **InvokeAmlWebServiceRRSEndpoint** a poskytnout jim stejná vstupní data. Měli byste očekávat, že uvidíte různé výsledky předpovědi, protože modely jsou vyškolené pomocí různých školicích sad.

## <a name="full-powershell-script"></a>Úplný skript PowerShellu
Zde je uveden seznam úplného zdrojového kódu:

```powershell
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
```