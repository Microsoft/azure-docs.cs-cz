---
Název: Obsloužených Machine Learning Studio modelů titleSuffix: Azure Machine Learning Studio Popis: Zjistěte, jak prostřednictvím kódu programu programovém přeučení modelů pomocí C# a provedení dávky služby Machine Learning service.
Services: machine learningu ms.service: ms.component strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04/19/2017
---
# <a name="retrain-azure-machine-learning-studio-models-programmatically"></a>Programově přeučit modely Azure Machine Learning Studio
V tomto návodu se dozvíte, jak prostřednictvím kódu programu přeučování webové služby Azure Machine Learning Studio pomocí C# a provedení dávky služby Machine Learning service.

Jakmile máte retrained modelu, následující postupy ukazují, jak aktualizace modelu v prediktivní webové služby:

* Pokud jste nasadili webové služby Classic na portálu webových služeb Machine Learning, přečtěte si téma [Přeučování webové služby Classic](retrain-a-classic-web-service.md). 
* Pokud jste nasadili novou webovou službu, přečtěte si téma [Přeučování nové webové služby pomocí rutin pro správu ve službě Machine Learning](retrain-new-web-service-using-powershell.md).

Přehled procesu retraining, naleznete v tématu [Přeučování Model strojového učení](retrain-machine-learning-model.md).

Pokud chcete začít s vaší stávající webové služby založené na nový Azure Resource Manageru, přečtěte si téma [Přeučování existující prediktivní webové služby](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Vytvořit výukový experiment
V tomto příkladu budete používat "vzorku 5: Trénování, testování, vyhodnotit pro binární klasifikace: Pro dospělé datové sady"z ukázky Microsoft Azure Machine Learning. 

Vytvoření testu:

1. Přihlaste se k Microsoft Azure Machine Learning Studio. 
2. V pravém dolním rohu řídicího panelu, klikněte na **nový**.
3. Microsoft Samples vyberte ukázkový 5.
4. Přejmenování experimentu v horní části plátna experimentu, vyberte název experimentu "vzorku 5: Trénování, testování, vyhodnotit pro binární klasifikace: Pro dospělé Dataset".
5. Typ modelu sčítání.
6. V dolní části na plátno experimentu klikněte na tlačítko **spustit**.
7. Klikněte na tlačítko **Set Up webová služba** a vyberte **Přetrénování webová služba**. 

Následující uvádí počáteční experimentu.
   
   ![Počáteční experimentu.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Vytvořit prediktivní experiment a publikovat jako webovou službu
Dále vytvoříte Predicative experimentu.

1. V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby** a vyberte **prediktivní webová služba**. Toto uloží modelu jako Trénovaného modelu a přidá webovou službu vstupní a výstupní moduly. 
2. Klikněte na **Run** (Spustit). 
3. Po dokončení spuštění experimentu klikněte na tlačítko **nasazení webové služby [Classic]** nebo **nasazení [nové] webová služba**.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Školení experiment nasadit jako webovou službu školení
Chcete-li přeučování trénovaného modelu, musíte je nasadit výukového experimentu, který jste vytvořili jako webovou službu Retraining. Potřebuje tuto webovou službu *výstup webové služby* připojeným modulům *[Train Model] [ train-model]* modulu, abyste mohli vytvářet nové školení modely.

1. Vraťte se do výukového experimentu, klikněte na ikonu experimenty v levém podokně a pak klikněte na experiment s názvem modelu sčítání.  
2. Do vyhledávacího pole Hledat položky Experiment zadejte webovou službu. 
3. Přetáhněte *vstup webové služby* do experimentu plátno a propojte svůj výstup do *vyčištění chybějících dat* modulu.  Tím se zajistí, že retraining data budou zpracovány stejně jako váš původním trénovací data.
4. Přetáhněte dva *webovou službu výstup* modulů na plátno experimentu. Propojte výstup modulu *trénování modelu* modulu jeden a výstup *Evaluate Model* modulu do jiné. Výstup webové služby pro **Train Model** získáváme nové trénovaného modelu. Výstup připojené k **Evaluate Model** vrátí výstupní tímto modulem, který je výsledky výkonu.
5. Klikněte na **Run** (Spustit). 

Dále je nutné nasadit výukového experimentu jako webovou službu, která vytváří trénovaného modelu a výsledky vyhodnocení modelu. K tomu jsou vaše další sadu akcí závisí na tom, jestli pracujete s webové služby Classic nebo novou webovou službu.  

**Klasické webové služby**

V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby** a vyberte **nasazení webové služby [Classic]**. Webová služba **řídicí panel** se zobrazí s klíčem rozhraní API a na stránce nápovědy rozhraní API pro spuštění dávky. Pouze metody provedení dávky služby lze použít pro vytvoření Trénované modely.

**Nové webové služby**

V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby** a vyberte **nasazení [nové] webová služba**. Webové služby Azure Machine Learning Web Services portálu se otevře na stránku nasazení webové služby. Zadejte název pro webovou službu a zvolte plán plateb a potom klikněte na tlačítko **nasadit**. Pouze metodu provedení dávky služby lze použít pro vytvoření Trénované modely

V obou případech po experimentu dokončí, výsledné pracovní postup by měl vypadat takto:

![Výsledný postupu po spuštění.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Přeučování s novými daty pomocí BES modelu
V tomto příkladu použijete C# k vytvoření aplikace přeučení. Můžete také použít ukázkový kód Pythonu nebo r. k provedení této úlohy.

Chcete-li volat rozhraní Retraining API:

1. Vytvoření C# konzolovou aplikaci v sadě Visual Studio: **Nové** > **projektu** > **Visual C#**   >  **klasická plocha Windows**  >   **Aplikace konzoly (.NET Framework)**.
2. Přihlaste se k portálu webové služby Machine Learning.
3. Pokud pracujete s webovou službou Classic, klikněte na tlačítko **klasické webové služby**.
   1. Klikněte na webovou službu, kterou pracujete.
   2. Klikněte na výchozí koncový bod.
   3. Klikněte na tlačítko **využívat**.
   4. V dolní části **využívání** stránku, **ukázkový kód** klikněte na tlačítko **Batch**.
   5. Přejděte ke kroku 5 tohoto postupu.
4. Pokud pracujete s novou webovou službu, klikněte na tlačítko **webových služeb**.
   1. Klikněte na webovou službu, kterou pracujete.
   2. Klikněte na tlačítko **využívat**.
   3. V dolní části spotřebě stránku, **ukázkový kód** klikněte na tlačítko **Batch**.
5. Zkopírujte ukázkový C# kód pro spuštění dávky a vložte ho do souboru Program.cs a ujistěte se, obor názvů zůstane beze změny.

Přidejte balíček Nuget Microsoft.AspNet.WebApi.Client uvedená v komentářích. Přidat odkaz na Microsoft.WindowsAzure.Storage.dll, nejprve potřebujete nainstalovat klientské knihovny pro úložiště služby Microsoft Azure. Další informace najdete v tématu [služby úložiště systému Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Aktualizovat deklarace apikey
Vyhledejte **apikey** deklarace.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V **informace o základní spotřeby** část **využívání** stránky, vyhledejte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage
Ukázkový kód BES nahraje soubor z místního disku (například "C:\temp\CensusIpnput.csv") do služby Azure Storage, procesy a zapisuje výsledky zpět do služby Azure Storage.  

Tento úkol provést, musíte načíst informace název, klíč a kontejner účtu úložiště pro váš účet úložiště z portálu Azure classic a odpovídající hodnoty aktualizace v kódu. 

1. Přihlaste se k portálu Azure classic.
2. V levém navigačním sloupci klikněte na tlačítko **úložiště**.
3. V seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
4. V dolní části stránky klikněte na tlačítko **spravovat přístupové klíče**.
5. Zkopírujte a uložte **primární přístupový klíč** a zavřete dialogové okno. 
6. V horní části stránky klikněte na tlačítko **kontejnery**.
7. Vybrat existující kontejner nebo vytvořte novou a uložit název.

Vyhledejte *StorageAccountName*, *StorageAccountKey*, a *StorageContainerName* deklarace a aktualizujte hodnoty jste si uložili z webu Azure portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Také musíte zajistit, že se že vstupní soubor je k dispozici v umístění, které zadáte v kódu. 

### <a name="specify-the-output-location"></a>Určení umístění výstupu
Při zadávání umístění výstupu do datové části požadavku, přípona souboru zadaný v *RelativeLocation* musí být zadán jako ilearner. 

Prohlédněte si následující příklad:

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
> Názvy umístění výstupu se může lišit od těch v tomto názorném postupu podle pořadí, ve které jste přidali modulů výstup webové služby. Protože nastavení tohoto výukového experimentu s dva výstupy výsledky zahrnují informace o umístění úložiště pro oba z nich.  
> 
> 

![Přeškolení výstupu][6]

Obrázek 4: Přeškolení výstup.

## <a name="evaluate-the-retraining-results"></a>Vyhodnoťte Retraining výsledky
Při spuštění aplikace výstup obsahuje adresu URL a SAS token potřebné pro přístup k výsledky hodnocení.

Zobrazí se výsledky výkonu retrained modelu tím, že zkombinujete *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu pro *output2* (jak je znázorněno na předchozím obrázku retraining výstupní) a vložením úplnou adresu URL do adresního řádku prohlížeče.  

Zkontrolujte výsledky a určit, zda se nově trénovaného modelu dostatečně dobře funguje nahradit stávající.

Kopírovat *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výstupní výsledky, použijeme je během procesu přeučení.

## <a name="next-steps"></a>Další postup
Pokud jste nasadili službu prediktivními webovými kliknutím **nasazení webové služby [Classic]**, naleznete v tématu [Přeučování webové služby Classic](retrain-a-classic-web-service.md).

Pokud jste nasadili službu prediktivními webovými kliknutím **nasazení [nové] webová služba**, naleznete v tématu [Přeučování nové webové služby pomocí rutin pro správu ve službě Machine Learning](retrain-new-web-service-using-powershell.md).

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
