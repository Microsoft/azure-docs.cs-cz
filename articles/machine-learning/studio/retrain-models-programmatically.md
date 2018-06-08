---
title: Programově přeučit modely Machine Learning | Microsoft Docs
description: Zjistěte, jak programově přeučit modelu a aktualizovat webovou službu, která používá nově trénovaného modelu v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: b2090b39991363ee2a5b2e12945d97dc0fa9f2b2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835500"
---
# <a name="retrain-machine-learning-models-programmatically"></a>Programové přeučení modelů Azure Machine Learning
V tomto návodu se dozvíte, jak programově přeučit Azure Machine Learning webové služby pomocí jazyka C# a Služba Machine Learning Batch Execution.

Jakmile mají retrained modelu, následující postupy ukazují, jak aktualizovat model prediktivní webové služby:

* Pokud jste nasadili Classic webovou službu na portálu webové služby Machine Learning, přečtěte si téma [Přeučování webové služby Classic](retrain-a-classic-web-service.md). 
* Pokud jste nasadili novou webovou službu, najdete v části [Přeučování novou webovou službu pomocí rutin Machine Learning Management](retrain-new-web-service-using-powershell.md).

Přehled procesu retraining najdete v tématu [Přeučování Model strojového učení](retrain-machine-learning-model.md).

Pokud chcete spustit s existující webovou službu na základě nové Azure Resource Manager, přečtěte si téma [Přeučování existující prediktivní webovou službu](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Vytvoření experimentu školení
V tomto příkladu budete používat "vzorku 5: Train, testovací, Evaluate pro binární klasifikaci: pro dospělé datovou sadu" z ukázky Microsoft Azure Machine Learning. 

Vytvoření experimentu:

1. Přihlaste se k platformě Microsoft Azure strojového učení Studio. 
2. V pravém horním rohu obrazovky řídicí panel, klikněte na tlačítko **nový**.
3. Microsoft Samples vyberte ukázkový 5.
4. Přejmenování experimentu v horní části plátna experimentu, vyberte název experimentu "vzorku 5: Train, testovací, Evaluate pro binární klasifikaci: pro dospělé datovou sadu".
5. Typ modelu úplné zjišťování.
6. V dolní části plátna experimentu, klikněte na tlačítko **spustit**.
7. Klikněte na tlačítko **nastavit webové služby** a vyberte **Retraining webové služby**. 

Následující obrázek znázorňuje počáteční experimentu.
   
   ![Počáteční experimentu.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Vytvořit prediktivní experiment a publikovat jako webovou službu
Dále vytvoříte Predicative experimentu.

1. V dolní části plátna experimentu, klikněte na tlačítko **nastavit webové služby** a vyberte **webové služby prediktivní**. To uloží model jako modulu Trained Model a přidá modulů vstup a výstup webové služby. 
2. Klikněte na **Run** (Spustit). 
3. Po dokončení běhu experimentu klikněte na tlačítko **nasazení webové služby [Classic]** nebo **nasazení [nové] webové služby**.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Výukový experiment nasadit jako webovou službu školení
Chcete-li přeučování trénovaného modelu, musíte je nasadit výukový experiment, kterou jste vytvořili jako webovou službu Retraining. Potřebuje této webové služby *výstup webové služby* připojené k modulu *[Train Model] [ train-model]* modul, abyste mohli vytvořit nový trénované modely.

1. Pokud chcete vrátit do výukový experiment, klikněte na ikonu experimenty v levém podokně a potom klikněte na tlačítko s názvem úplné zjišťování modelu experimentu.  
2. Do vyhledávacího pole položek experimentu hledání zadejte webovou službu. 
3. Přetáhněte *vstup webové služby* plátno modul do experimentu a propojte svůj výstup do *vyčištění chybějících dat* modulu.  Tím se zajistí, že retraining dat je zpracován stejným způsobem jako původní data školení.
4. Přetáhněte dva *webovou službu výstup* modulů na plátno experimentu. Připojit výstup *Train Model* modulu k jednomu a výstup *Evaluate Model* modulu na druhý. Výstup webové služby pro **Train Model** nám poskytuje nové naučeného modelu. Výstup připojené k **Evaluate Model** vrátí výstupu Tenhle modul, který je výsledky výkonu.
5. Klikněte na **Run** (Spustit). 

Dále je nutné nasadit výukový experiment jako webová služba, která vytváří modulu trained model a výsledky vyhodnocení modelu. K tomu, jsou závislé na tom, jestli pracujete s webovou službu Classic nebo novou webovou službu vaší další sadu akcí.  

**Classic webové služby**

V dolní části plátna experimentu, klikněte na tlačítko **nastavit webové služby** a vyberte **nasazení webové služby [Classic]**. Webová služba **řídicí panel** se zobrazí s klíč rozhraní API a na stránce nápovědy rozhraní API pro dávkové zpracování. Pouze metodu Batch Execution můžete použít pro vytvoření Trénované modely.

**Novou webovou službu**

V dolní části plátna experimentu, klikněte na tlačítko **nastavit webové služby** a vyberte **nasazení [nové] webové služby**. Otevře se na stránku webové služby nasadit portálu webové služby Azure Machine Learning webové služby. Zadejte název pro webovou službu a zvolte plán platby a potom klikněte na **nasadit**. Pouze Batch Execution metodu lze použít pro vytvoření Trénované modely

V obou případech po dokončení spuštění experimentu výsledné pracovního postupu by měl vypadat takto:

![Po spuštění výsledného workflow.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Přeučování modelu s použitím BES nová data
V tomto příkladu použijete C# k vytvoření retraining aplikace. Ukázkový kód Python nebo R můžete použít také k provedení této úlohy.

K volání rozhraní Retraining API:

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio: **nový** > **projektu** > **Visual C#** > **Windows Classic Desktop** > **konzolovou aplikaci (rozhraní .NET Framework)**.
2. Přihlaste se k portálu webová služba Machine Learning.
3. Pokud pracujete s webovou službou Classic, klikněte na tlačítko **Classic webové služby**.
   1. Klikněte na tlačítko webové služby, kterou pracujete.
   2. Klikněte na výchozí koncový bod.
   3. Klikněte na tlačítko **využívat**.
   4. V dolní části **spotřebě** stránky v **ukázkový kód** klikněte na tlačítko **Batch**.
   5. Přejděte ke kroku 5 tohoto postupu.
4. Pokud pracujete s novou webovou službu, klikněte na tlačítko **webové služby**.
   1. Klikněte na tlačítko webové služby, kterou pracujete.
   2. Klikněte na tlačítko **využívat**.
   3. Na konci spotřebovat, v stránky **ukázkový kód** klikněte na tlačítko **Batch**.
5. Ukázka kódu C# pro spuštění dávky zkopírujte a vložte jej do souboru Program.cs, ujistěte se, že obor názvů zůstává beze změn.

Přidejte balíček Nuget Microsoft.AspNet.WebApi.Client jako zadaný v komentářích. Pokud chcete přidat odkaz na Microsoft.WindowsAzure.Storage.dll, je nejdřív potřeba nainstalovat klientské knihovny pro úložiště služby Microsoft Azure. Další informace najdete v tématu [služby úložiště systému Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Aktualizace apikey deklarace
Vyhledejte **apikey** deklarace.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V **informace o základní spotřeby** části **spotřebě** stránky, vyhledejte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage
Ukázkový kód BES se uloží soubor z místního disku (například "C:\temp\CensusIpnput.csv") do služby Azure Storage, procesy a zapíše výsledky zpět do služby Azure Storage.  

K provedení této úlohy, musí získat informace název, klíč a kontejneru účtu úložiště pro váš účet úložiště z portálu Azure classic a aktualizace odpovídající hodnoty v kódu. 

1. Přihlaste se k portálu Azure classic.
2. Ve sloupci levém navigačním panelu klikněte na tlačítko **úložiště**.
3. Ze seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
4. V dolní části stránky klikněte na tlačítko **spravovat přístupové klíče**.
5. Zkopírujte a uložte **primární přístupový klíč** a zavřete tento dialog. 
6. V horní části stránky klikněte na tlačítko **kontejnery**.
7. Vyberte existující kontejner nebo vytvořte novou a uložit název.

Vyhledejte *StorageAccountName*, *StorageAccountKey*, a *StorageContainerName* deklarace a aktualizujte hodnoty jste uložili z portálu Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Můžete také zajistit, aby byl vstupní soubor k dispozici v umístění, které zadáte v kódu. 

### <a name="specify-the-output-location"></a>Zadejte umístění výstupu
Pokud zadáte umístění výstupu v datové části žádosti, příponu souboru zadaná v *RelativeLocation* musí být zadány jako ilearner. 

Podívejte se na následující příklad:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Názvy výstupních umístění se může lišit od těch v tomto návodu v pořadí, ve které jste přidali modulů výstup webové služby. Vzhledem k tomu, že jste nastavili tento výukový experiment s dvěma výstupy, výsledky budou zahrnovat informace o umístění úložiště pro oba dva.  
> 
> 

![Retraining výstup][6]

Obrázek 4: Retraining výstup.

## <a name="evaluate-the-retraining-results"></a>Vyhodnoťte Retraining výsledky
Při spuštění aplikace výstup obsahuje adresu URL a SAS token potřebné pro přístup k výsledky hodnocení.

Zobrazí výsledky retrained modelu tím, že zkombinujete *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstup pro *output2* (jak je znázorněno na předchozím obrázku výstup retraining) a vkládání úplnou adresu URL v adresním řádku prohlížeče.  

Podívejte se na výsledky, abyste zjistili, jestli pro nově cvičný model provádí dostatečně dobře nahradit stávající.

Kopírování *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výstup výsledků, použijeme je během procesu retraining.

## <a name="next-steps"></a>Další postup
Pokud jste nasadili prediktivní webovou službu kliknutím **nasazení webové služby [Classic]**, najdete v části [Přeučování webové služby Classic](retrain-a-classic-web-service.md).

Pokud jste nasadili prediktivní webovou službu kliknutím **nasazení [nové] webové služby**, najdete v části [Přeučování novou webovou službu pomocí rutin Machine Learning Management](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
