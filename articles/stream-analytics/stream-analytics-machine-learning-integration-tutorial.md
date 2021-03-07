---
title: Azure Stream Analytics integrace s Azure Machine Learning Studio (Classic)
description: Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning Studio (Classic) pomocí uživatelsky definované funkce.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1ebe62c1b90e09b36dd75b5bda4054cca08d5759
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441206"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Analýza mínění s využitím Azure Stream Analytics a Azure Machine Learning Studio (Classic)

V tomto článku se dozvíte, jak nastavit jednoduchou úlohu Azure Stream Analytics, která pro analýzu mínění používá Azure Machine Learning Studio (Classic). Pomocí analytického modelu mínění (Classic) z Cortana Intelligence Gallery Analyzujte streamovaná textová data a určíte skóre mínění.

> [!TIP]
> Pro zlepšení výkonu a spolehlivosti doporučujeme použít [Azure Machine Learning UDF](machine-learning-udf.md) místo Azure Machine Learning Studio (Classic) UDF.

To, co se naučíte v tomto článku, můžete použít ve scénářích, jako jsou tyto:

* Analýza mínění v reálném čase na streamování dat Twitteru.
* Analýza záznamů zákaznických chatů s pracovníky podpory
* Vyhodnocení komentářů na fórech, blogůch a videích
* Mnoho dalších scénářů prediktivního bodování v reálném čase.

Úloha Stream Analytics, kterou vytvoříte, používá model mínění Analytics jako uživatelsky definovanou funkci (UDF) pro ukázková textová data z úložiště objektů BLOB. Výstup (výsledek analýzy mínění) se zapíše do stejného úložiště objektů BLOB v jiném souboru CSV. 

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující akce:

* Musíte mít aktivní předplatné Azure.

* Soubor CSV s některými daty z Twitteru. Ukázkový soubor si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), případně můžete vytvořit vlastní soubor. Ve scénáři reálného světa byste data získali přímo z datového proudu Twitteru.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Vytvoření kontejneru úložiště a nahrání vstupního souboru CSV

V tomto kroku nahrajete soubor CSV do kontejneru úložiště.

1. V Azure Portal vyberte **vytvořit prostředek** úložiště  >    >  **účet** úložiště.

2. Vyplňte kartu *základy* s následujícími podrobnostmi a ponechte výchozí hodnoty pro zbývající pole:

   |Pole  |Hodnota  |
   |---------|---------|
   |Předplatné|Zvolte vaše předplatné.|
   |Skupina prostředků|Vyberte skupinu prostředků.|
   |Název účtu úložiště|Zadejte název účtu úložiště. Název musí být v rámci Azure jedinečný.|
   |Umístění|Zvolte umístění. Všechny prostředky by měly používat stejné umístění.|
   |Druh účtu|Blob Storage|

   ![Zadejte podrobnosti účtu úložiště.](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Vyberte **Zkontrolovat a vytvořit**. Pak vyberte **vytvořit** a nasaďte účet úložiště.

4. Po dokončení nasazení přejděte k účtu úložiště. V části **Blob service** vyberte **Kontejnery**. Pak vyberte **+ kontejner** a vytvořte nový kontejner.

   ![Vytvořit kontejner úložiště objektů BLOB pro vstup](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Zadejte název kontejneru a ověřte, zda je **úroveň veřejného přístupu** nastavena na hodnotu **Private**. Po dokončení vyberte **Vytvořit**.

   ![zadat podrobnosti kontejneru objektů BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Přejděte k nově vytvořenému kontejneru a vyberte **nahrát**. Nahrajte soubor **sampleinput.csv** , který jste si stáhli dříve.

   ![Tlačítko nahrát pro kontejner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Přidání modelu mínění Analytics z Cortana Intelligence Gallery

Teď, když jsou ukázková data v objektu blob, můžete povolit model analýzy mínění v Cortana Intelligence Gallery.

1. Přejít na stránku [prediktivního modelu mínění Analytics](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) v Cortana Intelligence Gallery.  

2. **V nástroji Studio vyberte otevřít (Classic)**.  
   
   ![Stream Analytics Azure Machine Learning Studio (Classic), otevřete Studio (Classic).](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Přihlaste se, abyste mohli přejít k pracovnímu prostoru. Vyberte umístění.

4. V dolní části stránky vyberte **Run (spustit** ). Proces se spustí, což trvá přibližně minutu.

   ![Spustit experiment v studiu (Classic)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po úspěšném spuštění procesu vyberte v dolní části stránky **nasadit webovou službu** .

   ![Nasazení experimentu v nástroji Studio (Classic) jako webové služby](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Pokud chcete ověřit, jestli je model mínění Analytics připravený k použití, vyberte tlačítko **test** . Zadejte textové zadání, jako je například "Miluji Microsoft".

   ![Test experimentu v studiu (klasický)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Pokud test funguje, zobrazí se výsledek podobný následujícímu příkladu:

   ![Výsledky testů v studiu (klasické)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Ve sloupci **aplikace** vyberte odkaz **sešit aplikace Excel 2010 nebo starší** a stáhněte excelový sešit. Sešit obsahuje klíč rozhraní API a adresu URL, které budete později potřebovat k nastavení Stream Analytics úlohy.

    ![Stream Analytics Azure Machine Learning Studio (klasický), rychlý přehled](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Vytvoření úlohy Stream Analytics, která používá model studia (Classic)

Nyní můžete vytvořit úlohu Stream Analytics, která přečte vzorový tweety ze souboru CSV v úložišti objektů BLOB.

### <a name="create-the-job"></a>Vytvoření úlohy

Přejít na [Azure Portal](https://portal.azure.com) a vytvořit úlohu Stream Analytics. Pokud nejste obeznámeni s tímto procesem, přečtěte si téma [vytvoření Stream Analytics úlohy pomocí Azure Portal](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Konfigurovat vstup úlohy

Úloha získá svůj vstup ze souboru CSV, který jste nahráli do úložiště objektů BLOB.

1. Přejděte na úlohu Stream Analytics. V části **topologie úlohy** vyberte možnost **vstupy** . Vyberte **Přidat streamovat vstupní**  > **úložiště objektů BLOB**.

2. Vyplňte **BLOB Storage** podrobnosti s následujícími hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   |Alias vstupu|Zadejte název. Tento alias si zapamatujte při psaní dotazu.|
   |Předplatné|Vyberte své předplatné.|
   |Účet úložiště|Vyberte účet úložiště, který jste provedli v předchozím kroku.|
   |Kontejner|Vyberte kontejner, který jste vytvořili v předchozím kroku.|
   |Formát serializace události|CSV|

3. Vyberte **Uložit**.

### <a name="configure-the-job-output"></a>Konfigurace výstupu úlohy

Úloha odešle výsledky do stejného úložiště objektů blob, kde získá vstup.

1. Přejděte na úlohu Stream Analytics. V části **topologie úlohy** vyberte možnost **výstupy** . Vyberte **Přidat**  >  **úložiště objektů BLOB**.

2. Do formuláře **BLOB Storage** zadejte tyto hodnoty:

   |Pole  |Hodnota  |
   |---------|---------|
   |Alias vstupu|Zadejte název. Tento alias si zapamatujte při psaní dotazu.|
   |Předplatné|Vyberte své předplatné.|
   |Účet úložiště|Vyberte účet úložiště, který jste provedli v předchozím kroku.|
   |Kontejner|Vyberte kontejner, který jste vytvořili v předchozím kroku.|
   |Formát serializace události|CSV|

3. Vyberte **Uložit**.

### <a name="add-the-studio-classic-function"></a>Přidat funkci studia (Classic)

Dříve jste publikovali model studia (Classic) do webové služby. Pokud se v tomto scénáři spustí úloha analýzy streamu, pošle Každá ukázka z vstupu do webové služby pro analýzu mínění. Webová služba Studio (Classic) vrací mínění ( `positive` , `neutral` nebo `negative` ) a pravděpodobnost, že se na něj klade pozitivní.

V této části definujete funkci v úloze Stream Analysis. Funkci lze vyvolat pro odeslání přípravku do webové služby a získání odpovědi zpět.

1. Ujistěte se, že máte adresu URL webové služby a klíč rozhraní API, které jste si stáhli dříve v excelovém sešitu.

2. Přejděte na úlohu Stream Analytics. Pak vyberte **funkce**  >  **+ Přidat**  >  **Azure ml Studio**

3. Do formuláře **Azure Machine Learning funkce** zadejte následující hodnoty:

   |Pole  |Hodnota  |
   |---------|---------|
   | Alias funkce | Použijte název `sentiment` a vyberte možnost **zadat Azure Machine Learning nastavení funkce ručně**, což vám umožní zadat adresu URL a klíč.      |
   | URL| Vložte adresu URL webové služby.|
   |Klíč | Vložte klíč rozhraní API. |

4. Vyberte **Uložit**.

### <a name="create-a-query-to-transform-the-data"></a>Vytvoření dotazu pro transformaci dat

Stream Analytics používá deklarativní dotaz založený na jazyce SQL k prohlédnutí vstupu a jeho zpracování. V této části vytvoříte dotaz, který načte jednotlivé operace ze vstupu a potom vyvolá funkci studia (Classic), která provede analýzu mínění. Dotaz pak odešle výsledek do výstupu, který jste definovali (BLOB Storage).

1. Vraťte se do přehledu úlohy Stream Analytics.

2. V části **topologie úlohy** vyberte možnost **dotaz**.

3. Zadejte následující dotaz:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   Dotaz vyvolá funkci, kterou jste vytvořili dříve ( `sentiment` ) pro provedení analýzy mínění na všech prodaných prodaných ve vstupu.

4. Vyberte **Uložit** a dotaz uložte.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Nyní můžete spustit úlohu Stream Analytics.

### <a name="start-the-job"></a>Spuštění úlohy

1. Vraťte se do přehledu úlohy Stream Analytics.

2. v horní části stránky vyberte **Start** .

3. V **nabídce spustit úlohu** vyberte možnost **vlastní** a potom vyberte jeden den před odesláním souboru CSV do úložiště objektů BLOB. Jakmile budete mít hotovo, vyberte **Spustit**.  

### <a name="check-the-output"></a>Kontrolovat výstup

1. Nechejte úlohu běžet po dobu několika minut, dokud se nezobrazí aktivita v poli **sledování** .

2. Pokud máte nástroj, který běžně používáte k prohlédnutí obsahu úložiště objektů blob, použijte tento nástroj k prohlédnutí kontejneru. Případně proveďte následující kroky v Azure Portal:

      1. V Azure Portal Najděte svůj účet úložiště a v rámci účtu Najděte kontejner. V kontejneru vidíte dva soubory: soubor obsahující ukázkovou tweety a soubor CSV vygenerovaný úlohou Stream Analytics.
      2. Klikněte pravým tlačítkem na vygenerovaný soubor a pak vyberte **Stáhnout**.

3. Otevřete generovaný soubor CSV. Uvidíte něco jako v následujícím příkladu:  

   ![Stream Analytics Azure Machine Learning Studio (klasický), zobrazení CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Zobrazit metriky

Můžete také zobrazit metriky související s funkcí studia (Classic). Následující metriky související s funkcí se zobrazí v okně **monitorování** úlohy přehled:

* **Žádosti o funkce** označují počet požadavků odeslaných do webové služby studia (Classic).  
* **Události funkcí** označují počet událostí v žádosti. Ve výchozím nastavení každý požadavek na webovou službu studia (Classic) obsahuje až 1 000 událostí.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrace REST API a Machine Learning Studio (Classic)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)