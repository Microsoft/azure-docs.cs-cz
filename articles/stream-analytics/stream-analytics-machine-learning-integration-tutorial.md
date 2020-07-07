---
title: Azure Stream Analytics integrace s Azure Machine Learning
description: Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning pomocí uživatelsky definované funkce.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80067095"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Provádění analýzy mínění s využitím Azure Stream Analytics a Azure Machine Learning Studio (Classic)

Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning Studio (Classic). Pomocí Machine Learning analytického modelu mínění z Cortana Intelligence Gallery Analyzujte streamovaná textová data a určíte skóre mínění v reálném čase. Pomocí Cortana Intelligence Suite můžete tuto úlohu provést, aniž byste se museli starat o složitými rozhraními vytváření modelu mínění Analytics.

> [!TIP]
> Pro zlepšení výkonu a spolehlivosti doporučujeme použít [Azure Machine Learning UDF](machine-learning-udf.md) místo Azure Machine Learning Studio (Classic) UDF.

To, co se naučíte v tomto článku, můžete použít ve scénářích, jako jsou tyto:

* Analýza mínění v reálném čase na streamování dat Twitteru.
* Analýza záznamů zákaznických chatů s pracovníky podpory
* Vyhodnocení komentářů na fórech, blogůch a videích 
* Mnoho dalších scénářů prediktivního bodování v reálném čase.

Ve scénáři reálného světa byste data získali přímo z datového proudu Twitteru. Aby se tento kurz zjednodušil, je zapsaný, takže úloha Stream Analytics získá tweety ze souboru CSV v úložišti objektů BLOB v Azure. Můžete vytvořit vlastní soubor CSV, nebo můžete použít ukázkový soubor CSV, jak je znázorněno na následujícím obrázku:

![Ukázkový tweety zobrazený v souboru CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Úloha Stream Analytics, kterou vytvoříte, používá model mínění Analytics jako uživatelsky definovanou funkci (UDF) pro ukázková textová data z úložiště objektů BLOB. Výstup (výsledek analýzy mínění) se zapíše do stejného úložiště objektů BLOB v jiném souboru CSV. 

Následující obrázek znázorňuje tuto konfiguraci. Jak je uvedeno, můžete pro realističtější scénář nahradit úložiště objektů BLOB streamovaná data Twitteru z Azure Event Hubs vstupu. Kromě toho můžete vytvořit vizualizaci agregované mínění v reálném čase [Microsoft Power BI](https://powerbi.microsoft.com/) .    

![Přehled integrace Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste provedli následující akce:

* Aktivní předplatné Azure.
* Soubor CSV s některými daty. Můžete si stáhnout soubor uvedený dříve z [GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), nebo můžete vytvořit vlastní soubor. V tomto článku se předpokládá, že používáte soubor z GitHubu.

Na nejvyšší úrovni, abyste dokončili úlohy popsané v tomto článku, proveďte následující akce:

1. Vytvořte účet úložiště Azure a kontejner úložiště objektů BLOB a nahrajte do kontejneru vstupní soubor ve formátu CSV.
3. Přidejte model mínění Analytics z Cortana Intelligence Gallery do pracovního prostoru Azure Machine Learning Studio (Classic) a nasaďte tento model jako webovou službu v pracovním prostoru Machine Learning.
5. Vytvořte úlohu Stream Analytics, která volá tuto webovou službu jako funkci, aby bylo možné určit mínění pro textové zadání.
6. Spusťte úlohu Stream Analytics a podívejte se na výstup.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Vytvoření kontejneru úložiště a nahrání vstupního souboru CSV
V tomto kroku můžete použít libovolný soubor CSV, například ten dostupný z GitHubu.

1. V Azure Portal klikněte na **vytvořit prostředek**  >  **úložiště**  >  **účet**úložiště.

2. Zadejte název ( `samldemo` v tomto příkladu). Název může obsahovat jenom malá písmena a číslice a musí být jedinečný v rámci Azure. 

3. Zadejte existující skupinu prostředků a zadejte umístění. Pro umístění doporučujeme, aby všechny prostředky vytvořené v tomto kurzu používaly stejné umístění.

    ![Zadejte podrobnosti účtu úložiště.](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. V Azure Portal vyberte účet úložiště. V okně účet úložiště klikněte na **kontejnery** a pak klikněte na ** + &nbsp; kontejner** . tím vytvoříte úložiště objektů BLOB.

    ![Vytvořit kontejner úložiště objektů BLOB pro vstup](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Zadejte název kontejneru ( `azuresamldemoblob` v příkladu) a ověřte, zda je **typ přístupu** nastaven na hodnotu **BLOB**. Až to budete mít, klikněte na **OK**.

    ![zadat podrobnosti kontejneru objektů BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. V okně **kontejnery** vyberte nový kontejner, který otevře okno pro daný kontejner.

7. Klikněte na **Odeslat**.

    ![Tlačítko nahrát pro kontejner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. V okně **nahrát objekt BLOB** nahrajte soubor **sampleinput.csv** , který jste si stáhli dříve. Jako **typ objektu BLOB**vyberte **objekt blob bloku** a nastavte velikost bloku na 4 MB, což pro tento kurz postačuje.

9. Klikněte na tlačítko **nahrát** v dolní části okna.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Přidání modelu mínění Analytics z Cortana Intelligence Gallery

Teď, když jsou ukázková data v objektu blob, můžete povolit model analýzy mínění v Cortana Intelligence Gallery.

1. Přejít na stránku [prediktivního modelu mínění Analytics](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) v Cortana Intelligence Gallery.  

2. **V nástroji Studio**klikněte na otevřít.  
   
   ![Stream Analytics Machine Learning otevřete Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Přihlaste se, abyste mohli přejít k pracovnímu prostoru. Vyberte umístění.

4. V dolní části stránky klikněte na **Spustit** . Proces se spustí, což trvá přibližně minutu.

   ![Spustit experiment v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po úspěšném spuštění procesu vyberte v dolní části stránky **nasadit webovou službu** .

   ![nasazení experimentu v Machine Learning Studio jako webové služby](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Pokud chcete ověřit, jestli je model mínění Analytics připravený k použití, klikněte na tlačítko **test** . Zadejte textové zadání, jako je například "Miluji Microsoft". 

   ![testovat experiment v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Pokud test funguje, zobrazí se výsledek podobný následujícímu příkladu:

   ![výsledky testů v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Ve sloupci **aplikace** klikněte na odkaz **Excel 2010 nebo starší sešit** a Stáhněte si excelový sešit. Sešit obsahuje klíč rozhraní API a adresu URL, které budete později potřebovat k nastavení Stream Analytics úlohy.

    ![Rychlý přehled Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Vytvoření Stream Analytics úlohy, která používá model Machine Learning

Nyní můžete vytvořit úlohu Stream Analytics, která přečte vzorový tweety ze souboru CSV v úložišti objektů BLOB. 

### <a name="create-the-job"></a>Vytvoření úlohy

1. Přejít na [Azure Portal](https://portal.azure.com).  

2. Klikněte na **vytvořit prostředek**  >  **Internet věcí**  >  **Stream Analytics úlohy**. 

3. Pojmenujte úlohu `azure-sa-ml-demo` , zadejte předplatné, zadejte existující skupinu prostředků nebo vytvořte novou a vyberte umístění úlohy.

   ![Zadejte nastavení pro novou úlohu Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurovat vstup úlohy
Úloha získá svůj vstup ze souboru CSV, který jste nahráli do úložiště objektů BLOB.

1. Po vytvoření úlohy klikněte v části **topologie úlohy** v okně úloha na možnost **vstupy** .    

2. V okně **vstupy** klikněte na **Přidat streamovat vstupní**  > **úložiště objektů BLOB** .

3. Vyplňte okno **BLOB Storage** s těmito hodnotami:

   
   |Pole  |Hodnota  |
   |---------|---------|
   |**Alias vstupu** | Použijte název `datainput` a vyberte **v předplatném možnost vybrat úložiště objektů BLOB** .       |
   |**Účet úložiště**  |  Vyberte dříve vytvořený účet úložiště.  |
   |**Kontejner**  | Vyberte kontejner, který jste vytvořili dříve ( `azuresamldemoblob` )        |
   |**Formát serializace události**  |  Vybrat **sdílený svazek clusteru**       |

   ![Nastavení pro nový vstup úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klikněte na **Uložit**.

### <a name="configure-the-job-output"></a>Konfigurace výstupu úlohy
Úloha odešle výsledky do stejného úložiště objektů blob, kde získá vstup. 

1. V části **topologie úlohy** v okně úloha klikněte na možnost **výstupy** .  

2. V okně **výstupy** klikněte na **Přidat**  > **úložiště objektů BLOB**a pak přidejte výstup s aliasem `datamloutput` . 

3. Vyplňte okno **BLOB Storage** s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   |**Alias pro výstup** | Použijte název `datamloutput` a vyberte **v předplatném možnost vybrat úložiště objektů BLOB** .       |
   |**Účet úložiště**  |  Vyberte dříve vytvořený účet úložiště.  |
   |**Kontejner**  | Vyberte kontejner, který jste vytvořili dříve ( `azuresamldemoblob` )        |
   |**Formát serializace události**  |  Vybrat **sdílený svazek clusteru**       |

   ![Nastavení pro nový výstup úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klikněte na **Uložit**.   


### <a name="add-the-machine-learning-function"></a>Přidání funkce Machine Learning 
Dříve jste publikovali Machine Learning model pro webovou službu. Pokud se v tomto scénáři spustí úloha analýzy streamu, pošle Každá ukázka z vstupu do webové služby pro analýzu mínění. Webová služba Machine Learning vrací mínění ( `positive` , `neutral` nebo `negative` ) a pravděpodobnost nesprávného prokladu. 

V této části kurzu definujete funkci v úloze Stream Analysis. Funkci lze vyvolat pro odeslání přípravku do webové služby a získání odpovědi zpět. 

1. Ujistěte se, že máte adresu URL webové služby a klíč rozhraní API, které jste si stáhli dříve v excelovém sešitu.

2. Přejděte do okna úlohy > **funkce**  >  **+ Přidat**  >  **AzureML** .

3. Vyplňte okno **Azure Machine Learning funkce** s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   | **Alias funkce** | Použijte název `sentiment` a vyberte možnost **zadat Azure Machine Learning nastavení funkce ručně** , což vám umožní zadat adresu URL a klíč.      |
   | **URL**| Vložte adresu URL webové služby.|
   |**Zkrat** | Vložte klíč rozhraní API. |
  
   ![Nastavení pro přidání funkce Machine Learning pro Stream Analytics úlohy](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klikněte na **Uložit**.

### <a name="create-a-query-to-transform-the-data"></a>Vytvoření dotazu pro transformaci dat

Stream Analytics používá deklarativní dotaz založený na jazyce SQL k prohlédnutí vstupu a jeho zpracování. V této části vytvoříte dotaz, který načte jednotlivé operace ze vstupu a potom vyvolá funkci Machine Learning pro provedení analýzy mínění. Dotaz pak odešle výsledek do výstupu, který jste definovali (BLOB Storage).

1. Vraťte se do okna Přehled úlohy.

2.  V části **topologie úlohy**klikněte na pole **dotazu** .

3. Zadejte následující dotaz:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Dotaz vyvolá funkci, kterou jste vytvořili dříve ( `sentiment` ), aby provedla analýzu mínění na všech prodaných prodaných ve vstupu. 

4. Kliknutím na **Uložit** dotaz uložte.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Nyní můžete spustit úlohu Stream Analytics.

### <a name="start-the-job"></a>Spuštění úlohy
1. Vraťte se do okna Přehled úlohy.

2. V horní části okna klikněte na **Start** .

3. V **nabídce Start**vyberte možnost **vlastní**a potom vyberte jeden den před odesláním souboru CSV do úložiště objektů BLOB. Až skončíte, klikněte na **Spustit**.  


### <a name="check-the-output"></a>Kontrolovat výstup
1. Nechejte úlohu běžet po dobu několika minut, dokud se nezobrazí aktivita v poli **sledování** . 

2. Pokud máte nástroj, který běžně používáte k prohlédnutí obsahu úložiště objektů blob, použijte tento nástroj k prohlédnutí `azuresamldemoblob` kontejneru. Případně proveďte následující kroky v Azure Portal:

    1. Na portálu Najděte `samldemo` účet úložiště a v rámci účtu Najděte `azuresamldemoblob` kontejner. V kontejneru vidíte dva soubory: soubor obsahující ukázkovou tweety a soubor CSV vygenerovaný úlohou Stream Analytics.
    2. Klikněte pravým tlačítkem na vygenerovaný soubor a pak vyberte **Stáhnout**. 

   ![Stažení výstupu úlohy CSV z úložiště objektů BLOB](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otevřete generovaný soubor CSV. Uvidíte něco jako v následujícím příkladu:  
   
   ![Stream Analytics Machine Learning, zobrazení CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Zobrazit metriky
Můžete také zobrazit Azure Machine Learning metriky související s funkcí. V okně **sledování** v okně úlohy se zobrazí následující metriky související s funkcí:

* **Požadavek funkce** indikuje počet požadavků odeslaných do webové služby Machine Learning.  
* **Události funkcí** označují počet událostí v žádosti. Ve výchozím nastavení každý požadavek na Machine Learning webové služby obsahuje až 1 000 událostí.  


## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrace REST API a Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



