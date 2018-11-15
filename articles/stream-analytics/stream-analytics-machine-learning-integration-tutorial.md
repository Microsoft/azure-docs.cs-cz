---
title: Integrace Azure Stream Analytics pomocí Azure Machine Learning
description: Tento článek popisuje, jak rychle vytvořit jednoduchou úlohu Azure Stream Analytics, která se integruje s Azure Machine Learning, jak pomocí uživatelsky definovaná funkce.
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 2169c3a41991b0b49a4324c16ea079f5943fad0b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685748"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Provedení analýzy subjektivního hodnocení s využitím Azure Stream Analytics a Azure Machine Learning
Tento článek popisuje, jak rychle vytvořit jednoduchou úlohu Azure Stream Analytics, která se integruje s Azure Machine Learning. Použijete modelem Machine Learning pro analýzu mínění v galerii Cortana Intelligence pro analýzu streamování textová data a určit skóre mínění v reálném čase. Pomocí Cortana Intelligence Suite vám umožňuje provést bez starostí o složitými rozhraními vytváření modelu analýzy mínění.

Můžete použít, co jste se naučili v tomto článku pro podobné scénáře je:

* Analýza v reálném čase mínění na streamovaných datech Twitter.
* Analýza záznamy zákazníků konverzace s pracovníky podpory.
* Vyhodnocení komentáře na fóra, blogy a videa. 
* Mnoho dalších v reálném čase, prediktivní hodnocení scénáře.

Ve skutečném scénáři by získat data přímo z datového proudu Twitter. Pro zjednodušení tento kurz je napsán tak, aby úlohy Stream Analytics získá tweetů ze souboru CSV v úložišti objektů Blob v Azure. Můžete vytvořit svůj vlastní soubor CSV, nebo můžete použít ukázkový soubor CSV, jak je znázorněno na následujícím obrázku:

![Ukázka tweety v souboru CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Úloha Stream Analytics, který vytvoříte platí modelu analýzy subjektivního hodnocení jako uživatelem definované funkce (UDF) na těchto ukázkových datech text z úložiště objektů blob. Výstup (výsledek analýzy mínění) je zapsán do stejného úložiště objektů blob v jiném souboru CSV. 

Následující obrázek znázorňuje tuto konfiguraci. Jak je uvedeno pro realističtější scénář, můžete nahradit úložiště objektů blob se streamovanými daty Twitteru z Azure Event Hubs vstup. Kromě toho může vytvářet [Microsoft Power BI](https://powerbi.microsoft.com/) vizualizace v reálném čase z agregační mínění.    

![Přehled integrace služby Stream Analytics, Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste provedli následující akce:

* Aktivní předplatné Azure.
* Soubor CSV s daty v ní. Můžete stáhnout soubor je uvedeno výše z [Githubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), nebo můžete vytvořit svůj vlastní soubor. Pro účely tohoto článku se předpokládá, že používáte soubor z Githubu.

Na vysoké úrovni k dokončení úloh jsme vám ukázali v tomto článku provedete následující:

1. Vytvoření účtu služby Azure storage a kontejner úložiště objektů blob a nahrajte vstupní soubor ve formátu CSV do kontejneru.
3. Přidat model analýzy mínění v galerii Cortana Intelligence do pracovního prostoru Azure Machine Learning a tento model nasadíme jako webovou službu v pracovního prostoru Machine Learning.
5. Vytvoření úlohy Stream Analytics, která volá tuto webovou službu jako funkce, aby bylo možné zjistit mínění pro textový vstup.
6. Spuštění úlohy Stream Analytics a kontrola výstupu.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Vytvoření kontejneru úložiště a nahrání vstupního souboru CSV
Pro tento krok můžete použít libovolný soubor CSV, jako je například je k dispozici na Githubu.

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** > **úložiště** > **účtu úložiště**.

2. Zadejte název (`samldemo` v příkladu). Název lze použít pouze malá písmena a číslice a musí být jedinečné v Azure. 

3. Zadejte existující skupinu prostředků a umístění. Pro umístění doporučujeme vám, že všechny prostředky vytvořené v tomto kurzu používat stejné umístění.

    ![Zadejte podrobnosti o účtu úložiště](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Na webu Azure Portal vyberte účet úložiště. V okně účtu úložiště, klikněte na tlačítko **kontejnery** a potom klikněte na tlačítko  **+ &nbsp;kontejneru** k vytvoření objektu blob úložiště.

    ![vytvořit kontejner objektů blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Zadejte název kontejneru (`azuresamldemoblob` v příkladu) a ověřte, že **získat přístup k typu** je nastavena na **Blob**. Až to budete mít, klikněte na **OK**.

    ![Zadejte podrobnosti o kontejneru objektů blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. V **kontejnery** okně Vybrat nový kontejner, který se otevře okno pro tento kontejner.

7. Klikněte na **Odeslat**.

    ![Nahrát tlačítko pro kontejner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. V **nahrát objekt blob** okno, odesílání **sampleinput.csv** soubor, který jste předtím stáhli. Pro **typ blobu**vyberte **objektů blob bloku** a velikost bloku nastavená na 4 MB, což stačí pro účely tohoto kurzu.

9. Klikněte na tlačítko **nahrát** tlačítko v dolní části okna.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Přidat model analýzy mínění v galerii Cortana Intelligence

Ukázková data jsou v objektu blob, můžete povolit model analýzy subjektivního hodnocení ve galerii Cortana Intelligence.

1. Přejděte [modelu analýzy mínění prediktivní](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) stránky v galerii Cortana Intelligence.  

2. Klikněte na tlačítko **Open in Studio**.  
   
   ![Stream Analytics, Machine Learning, otevřete Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Přihlaste se k přejděte do pracovního prostoru. Vyberte umístění.

4. Klikněte na tlačítko **spustit** v dolní části stránky. Proces je spuštěn, což trvá přibližně minutu.

   ![Spusťte experiment v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po dokončení procesu bylo úspěšně spuštěno, vyberte **nasadit webovou službu** v dolní části stránky.

   ![experiment v Machine Learning Studio nasadíte jako webovou službu](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Chcete-li ověřit, že je připravená k použití modelu analýzy subjektivního hodnocení, klikněte na tlačítko **Test** tlačítko. Zadejte textový vstup, jako je "Mám rád Microsoft". 

   ![Test experiment v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Pokud test funguje, zobrazí se výsledek podobný následujícím příkladu:

   ![výsledky testů v nástroji Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. V **aplikace** sloupce, klikněte na tlačítko **Excel 2010 nebo starší sešitu** odkaz ke stažení sešitu aplikace Excel. Sešit obsahuje klíč rozhraní API a adresu URL, kterou budete později potřebovat k nastavení úlohy Stream Analytics.

    ![Stream Analytics Machine Learning, rychlý přehled](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Vytvořit úlohu Stream Analytics, která používá model Machine Learning

Nyní můžete vytvořit úlohu Stream Analytics, která čte tweety, které ukázka ze souboru CSV v úložišti objektů blob. 

### <a name="create-the-job"></a>Vytvoření úlohy

1. Přejděte na [Azure Portal](https://portal.azure.com).  

2. Klikněte na tlačítko **vytvořit prostředek** > **Internet of Things** > **úlohy Stream Analytics**. 

3. Název úlohy `azure-sa-ml-demo`, zadejte předplatné, zadejte existující skupinu prostředků nebo vytvořte novou a vyberte umístění pro úlohu.

   ![Zadejte nastavení pro nové úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurace vstupu úlohy
Úloha získá vstup ze souboru CSV, který jste dříve nahráli do úložiště objektů blob.

1. Po úloze, v části **topologie úlohy** v okně úlohy, klikněte **vstupy** možnost.    

2. V **vstupy** okna, klikněte na tlačítko **přidat vstupní Stream** >**úložiště objektů Blob**

3. Vyplňte **úložiště objektů Blob** okno s těmito hodnotami:

   
   |Pole  |Hodnota  |
   |---------|---------|
   |**Vstupní alias** | Použijte název `datainput` a vyberte **vyberte služby blob storage z předplatného**       |
   |**Účet úložiště**  |  Vyberte účet úložiště, který jste vytvořili dříve.  |
   |**Kontejner**  | Vyberte kontejner, který jste vytvořili dříve (`azuresamldemoblob`)        |
   |**Formát serializace události**  |  Vyberte **sdíleného svazku clusteru**       |

   ![Nastavení pro nové vstupu úlohy](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Klikněte na **Uložit**.

### <a name="configure-the-job-output"></a>Konfigurace výstupu úlohy
Úloha odešle výsledky do stejného úložiště objektů blob ve kterém získá vstup. 

1. V části **topologie úlohy** v okně úlohy, klikněte **výstupy** možnost.  

2. V **výstupy** okna, klikněte na tlačítko **přidat** >**úložiště objektů Blob**a pak přidejte výstup s aliasem `datamloutput`. 

3. Vyplňte **úložiště objektů Blob** okno s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   |**Alias pro výstup** | Použijte název `datamloutput` a vyberte **vyberte služby blob storage z předplatného**       |
   |**Účet úložiště**  |  Vyberte účet úložiště, který jste vytvořili dříve.  |
   |**Kontejner**  | Vyberte kontejner, který jste vytvořili dříve (`azuresamldemoblob`)        |
   |**Formát serializace události**  |  Vyberte **sdíleného svazku clusteru**       |

   ![Nastavení pro nový výstup úlohy](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Klikněte na **Uložit**.   


### <a name="add-the-machine-learning-function"></a>Přidat funkci strojového učení 
Dříve jste publikovali modelu strojového učení k webové službě. V tomto scénáři při spuštění úlohy analýzy Stream, odešle jednotlivých tweetů ukázka ze vstupu webové služby pro analýzu mínění. Webové služby Machine Learning vrací mínění (`positive`, `neutral`, nebo `negative`) a pravděpodobnost tweet se kladná. 

V této části kurzu definujete funkci v úloze Stream analýzy. Funkci lze vyvolat a odeslání tweetu k webové službě a získejte odpověď. 

1. Ujistěte se, že máte na webové služby adresy URL a klíč rozhraní API, který jste stáhli dříve v Excelovém sešitu.

2. Přejděte do okna vaší úlohy > **funkce** > **+ přidat** > **Azure ml**

3. Vyplňte **funkce Azure Machine Learning** okno s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   | **Alias funkce** | Použijte název `sentiment` a vyberte **nastavení funkce poskytují Azure Machine Learning ručně** níž získáte možnost zadat adresu URL a klíč.      |
   | **Adresa URL**| Vložte adresu URL webové služby.|
   |**Klíč** | Vložte klíč rozhraní API. |
  
   ![Nastavení pro přidání funkce Machine Learning do úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Klikněte na **Uložit**.

### <a name="create-a-query-to-transform-the-data"></a>Vytvořit dotaz, který transformuje data

Stream Analytics využívá deklarativní, na základě SQL dotaz zkontrolujte vstup a zpracovat je. V této části vytvoříte dotaz, který čte jednotlivých tweetů ze vstupu a potom vyvolá funkce Machine Learning provádět analýzu subjektivního hodnocení. Výsledek dotazu poté odešle do výstupu, který jste definovali (úložiště objektů blob).

1. Vraťte se do okna přehledu úlohy.

2.  V části **topologie úlohy**, klikněte na tlačítko **dotazu** pole.

3. Zadejte následující příkaz:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Dotaz volá funkci, která jste vytvořili dříve (`sentiment`) aby bylo možné provést analýzu subjektivního hodnocení pro každý tweet ve vstupu. 

4. Klikněte na tlačítko **Uložit** Uložte dotaz.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Teď můžete začít úlohy Stream Analytics.

### <a name="start-the-job"></a>Spuštění úlohy
1. Vraťte se do okna přehledu úlohy.

2. Klikněte na tlačítko **Start** v horní části okna.

3. V **spuštění úlohy**vyberte **vlastní**a pak zvolte jeden den před datem nahrání souboru CSV do úložiště objektů blob. Jakmile budete hotovi, klikněte na tlačítko **Start**.  


### <a name="check-the-output"></a>Zkontrolujte výstup
1. Umožní se úloha spouštět na několik minut, dokud se nezobrazí aktivity v **monitorování** pole. 

2. Pokud máte nástroj, který obvykle můžete prozkoumat obsah úložiště objektů blob, pomocí tohoto nástroje `azuresamldemoblob` kontejneru. Případně proveďte následující kroky na webu Azure Portal:

    1. Na portálu vyhledejte `samldemo` úložiště účtu a v rámci účtu, najdete `azuresamldemoblob` kontejneru. Vidět dva soubory v kontejneru: soubor, který obsahuje ukázkové tweety a soubor CSV generovaný úlohy Stream Analytics.
    2. Vygenerovaný soubor pravým tlačítkem a pak vyberte **Stáhnout**. 

   ![Stáhněte si výstup úlohy sdíleného svazku clusteru z úložiště objektů Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otevřete vygenerovaný soubor CSV. Můžete zobrazit něco jako v následujícím příkladu:  
   
   ![Stream Analytics Machine Learning, zobrazení sdíleného svazku clusteru](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Zobrazit metriky
Můžete také zobrazit metriky související s funkcí Azure Machine Learning. Následující funkce související metriky jsou zobrazeny v **monitorování** pole v okně úlohy:

* **Funkce požadavků** označuje počet požadavky odeslané na webovou službu Machine Learning.  
* **Funkce události** označuje počet událostí v požadavku. Ve výchozím nastavení obsahuje každý požadavek na webovou službu Machine Learning až 1 000 událostí.  


## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrace rozhraní REST API a Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



