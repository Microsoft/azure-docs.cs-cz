---
title: Integrace Azure Stream Analytics a Machine Learning | Microsoft Docs
description: Jak používat uživatelsky definované funkce a Machine Learning v úloze Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/01/2018
ms.author: sngun
ms.openlocfilehash: e5661f9f3871619201ff9106e87785de802e8933
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Provedení analýzy postojích pomocí Azure Stream Analytics nebo Azure Machine Learning
Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning. Používáte model Machine Learning postojích analytics z webu Cortana Intelligence Gallery a analyzovat data streamovaná text určení skóre postojích v reálném čase. Pomocí Cortana Intelligence Suite umožňuje provedení této úlohy bez obav o rozbor všech vytváření model postojích analytics.

Můžete použít co dozvíte z tohoto článku pro scénáře, jako je například tyto:

* Analýza v reálném čase postojích na streamování dat Twitteru.
* Analýza záznamy zákazníka konverzace s pracovníky technické podpory.
* Vyhodnocení komentáře na fóra, blogy a videa. 
* Mnoho dalších v reálném čase, prediktivní vyhodnocování scénářů.

Ve scénáři reálného by získat data přímo z datového proudu Twitter. Pro zjednodušení tento kurz, jsme jste jej tak, aby úlohy streamování Analytics získá tweetů ze souboru CSV v úložišti objektů Blob Azure. Můžete vytvořit svůj vlastní soubor CSV, nebo můžete použít ukázkový soubor CSV, jak je znázorněno na následujícím obrázku:

![Ukázka tweetů v souboru CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Úlohy streamování Analytics, který vytvoříte platí model analýzy postojích jako uživatelem definované funkce (UDF) na ukázková data text z úložiště objektů blob. Výstup (výsledek analýzy postojích) je zapsán do stejné úložiště objektů blob v jiném souboru CSV. 

Následující obrázek ukazuje tuto konfiguraci. Jak jsme uvedli, realističtější scénáři můžete nahradit úložiště objektů blob streamování Twitter dat z Azure Event Hubs vstup. Kromě toho je sestavení [Microsoft Power BI](https://powerbi.microsoft.com/) v reálném čase vizualizace agregační postojích.    

![Přehled integrace Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte následující:

* Aktivní předplatné Azure.
* Soubor CSV s některá data v ní. Si můžete stáhnout soubor uvedena výše z [Githubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), nebo můžete vytvořit svůj vlastní soubor. V tomto článku předpokládáme, že používáte soubor z Githubu.

Na vysoké úrovni k dokončení úkolů ukázáno v tomto článku jste takto:

1. Vytvořit účet úložiště Azure a kontejner úložiště objektů blob a nahrajte vstupní soubor ve formátu CSV do kontejneru.
3. Přidání modelu analytics postojích z webu Cortana Intelligence Gallery do pracovního prostoru Azure Machine Learning a nasadit tento model jako webovou službu v pracovním prostoru Machine Learning.
5. Vytvořte úlohu služby Stream Analytics, který volání této webové služby jako funkci, aby bylo možné zjistit postojích pro zadávání textu.
6. Spustit úlohu služby Stream Analytics a zkontrolujte výstup.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Vytvoření kontejneru úložiště a načtěte vstupní soubor CSV
Pro tento krok můžete použít všechny souboru CSV, jako je třeba k dispozici z Githubu.

1. Na portálu Azure klikněte na tlačítko **vytvořit prostředek** > **úložiště** > **účet úložiště**.

2. Zadejte název (`samldemo` v příkladu). Název můžete použít jenom malá písmena a čísla a musí být jedinečný v Azure. 

3. Zadejte existující skupinu prostředků a zadejte umístění. Pro umístění doporučujeme všechny prostředky, které jsou vytvořené v tomto kurzu použít stejné umístění.

    ![Zadejte podrobnosti o účtu úložiště](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Na portálu Azure vyberte účet úložiště. V okně účtu úložiště, klikněte na tlačítko **kontejnery** a pak klikněte na  **+ &nbsp;kontejneru** k vytvoření úložiště objektů blob.

    ![Vytvoření kontejneru objektů blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Zadejte název kontejneru (`azuresamldemoblob` v příkladu) a ověřte, že **přistupovat typu** je nastaven na **Blob**. Až to budete mít, klikněte na **OK**.

    ![Zadejte podrobnosti kontejner objektů blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. V **kontejnery** okně vyberte nový kontejner, který se otevře v okně pro tento kontejner.

7. Klikněte na **Odeslat**.

    ![Nahrát tlačítko pro kontejner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. V **nahrávání blob** okno, odesílání **sampleinput.csv** soubor, který jste předtím stáhli. Pro **Blob typ**, vyberte **objekt blob bloku** a nastavit velikost bloku na 4 MB, který je dostatečný pro účely tohoto kurzu.

9. Klikněte **nahrát** tlačítko v dolní části okna.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Přidat model analýzy postojích z webu Cortana Intelligence Gallery

Teď, když je ukázková data do objektu BLOB, můžete povolit model postojích analýzy v Cortana Intelligence Gallery.

1. Přejděte na [model postojích prediktivní analýzy](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) stránky v Cortana Intelligence Gallery.  

2. Klikněte na tlačítko **Open in Studio**.  
   
   ![Stream Analytics Machine Learning, otevřete Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Přihlaste se k přejděte do pracovního prostoru. Vyberte umístění.

4. Klikněte na tlačítko **spustit** v dolní části stránky. Spustí se proces, který trvá několik minut.

   ![Spusťte experiment v nástroji Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Poté, co proces proběhla úspěšně, vyberte **nasazení webové služby** v dolní části stránky.

   ![experiment v nástroji Machine Learning Studio nasaďte jako webovou službu](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Chcete-li ověřit, zda model analýzy postojích připravené k použití, klikněte na tlačítko **Test** tlačítko. Zadejte textový vstup, jako je "I rádi Microsoft". 

   ![Test experimentu v nástroji Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Pokud test funguje, zobrazí výsledek podobně jako v následujícím příkladu:

   ![výsledky testu v nástroji Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. V **aplikace** sloupce, klikněte na tlačítko **Excel 2010 nebo starší sešitu** odkaz ke stažení sešitu aplikace Excel. Sešit obsahuje klíč rozhraní API a adresu URL, kterou budete potřebovat později nastavit úlohu služby Stream Analytics.

    ![Stream Analytics Machine Learning, rychlého přehledu.](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Vytvořit úlohu služby Stream Analytics, který používá model Machine Learning

Nyní můžete vytvořit úlohu služby Stream Analytics, která načte tweetů ukázka ze souboru CSV v úložišti objektů blob. 

### <a name="create-the-job"></a>Vytvoření úlohy

1. Přejděte na [portál Azure](https://portal.azure.com).  

2. Klikněte na tlačítko **vytvořit prostředek** > **Internet věcí** > **úlohy služby Stream Analytics**. 

3. Název úlohy `azure-sa-ml-demo`, zadejte předplatné, zadejte existující skupinu prostředků nebo vytvořte novou a vyberte umístění pro úlohu.

   ![Zadejte nastavení pro nové úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurace vstupu úlohy
Úloha získá vstupní ze souboru CSV, který jste dříve nahráli do úložiště objektů blob.

1. Po úloha byla vytvořena, v části **úlohy topologie** v okně úlohy klikněte na tlačítko **vstupy** možnost.    

2. V **vstupy** okně klikněte na tlačítko **přidat vstup datového proudu** >**úložiště objektů Blob**

3. Vyplňte **úložiště objektů Blob** okno s těmito hodnotami:

   
   |Pole  |Hodnota  |
   |---------|---------|
   |**Vstupní alias** | Použijte název `datainput` a vyberte **vyberte objekt blob úložiště ze svého předplatného**       |
   |**Účet úložiště**  |  Vyberte účet úložiště, které jste vytvořili dříve.  |
   |**kontejner**  | Vyberte kontejner, který jste vytvořili dříve (`azuresamldemoblob`)        |
   |**Formát serializace událostí**  |  Vyberte **sdíleného svazku clusteru**       |

   ![Nastavení pro nové úlohy vstup](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Klikněte na **Uložit**.

### <a name="configure-the-job-output"></a>Konfigurace výstup úlohy
Úloha odešle výsledky na stejné úložiště objektů blob kde získá vstup. 

1. V části **úlohy topologie** v okně úlohy klikněte na tlačítko **výstupy** možnost.  

2. V **výstupy** okně klikněte na tlačítko **přidat** >**úložiště objektů Blob**a poté přidejte výstup s aliasem `datamloutput`. 

3. Vyplňte **úložiště objektů Blob** okno s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   |**Alias pro výstup** | Použijte název `datainput` a vyberte **vyberte objekt blob úložiště ze svého předplatného**       |
   |**Účet úložiště**  |  Vyberte účet úložiště, které jste vytvořili dříve.  |
   |**kontejner**  | Vyberte kontejner, který jste vytvořili dříve (`azuresamldemoblob`)        |
   |**Formát serializace událostí**  |  Vyberte **sdíleného svazku clusteru**       |

   ![Nastavení pro nové výstup úlohy](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Klikněte na **Uložit**.   


### <a name="add-the-machine-learning-function"></a>Přidání funkce Machine Learning 
Dříve jste publikovali model Machine Learning k webové službě. V tomto scénáři při spuštění úlohy analýzy datového proudu odešle každý ukázka tweet ze vstupu k webové službě pro analýzu postojích. Vrátí webové službě Machine Learning postojích (`positive`, `neutral`, nebo `negative`) a pravděpodobnost vzniku tweet se kladné. 

V této části kurzu definujete funkce v Úloha analýzy datového proudu. Funkci nelze vyvolat odeslala tweet k webové službě a získat odpověď zpět. 

1. Ujistěte se, že máte webové adresy URL a rozhraní API klíč služby, který jste si stáhli dříve v sešitu aplikace Excel.

2. Přejděte do okna vaší úlohy > **funkce** > **+ přidat** > **AzureML**

3. Vyplňte **funkce Azure Machine Learning** okno s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   | **Alias – funkce** | Použijte název `sentiment` a vyberte **nastavení funkce poskytují Azure Machine Learning ručně** níž získáte možnost zadat adresu URL a klíč.      |
   | **Adresa URL**| Vložte adresu URL webové služby.|
   |**Klíč** | Vložte klíč rozhraní API. |
  
   ![Nastavení pro přidání funkce Machine Learning do úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Klikněte na **Uložit**.

### <a name="create-a-query-to-transform-the-data"></a>Vytvořit dotaz, který transformace dat

Stream Analytics používá k ověření vstupu a zpracovat dotaz deklarativní, na základě SQL. V této části vytvoříte dotaz, který čte každý tweet ze vstupu a pak zavolá funkci Machine Learning k analýze postojích. Výsledek dotazu pak odešle na výstupu, že jste definovali (úložiště objektů blob).

1. Vraťte se do okna Přehled úlohy.

2.  V části **úlohy topologie**, klikněte na tlačítko **dotazu** pole.

3. Zadejte následující dotaz:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    Dotaz, vyvolá funkce, které jste vytvořili dříve (`sentiment`) Chcete-li provést analýzu postojích na každý tweet ve vstupu. 

4. Klikněte na tlačítko **Uložit** Uložte dotaz.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spustit úlohu služby Stream Analytics a zkontrolujte výstup

Nyní můžete spustit úlohu služby Stream Analytics.

### <a name="start-the-job"></a>Spustit úlohu
1. Vraťte se do okna Přehled úlohy.

2. Klikněte na tlačítko **spustit** v horní části okna.

3. V **spuštění úlohy**, vyberte **vlastní**a pak vyberte jeden den před při nahrát soubor CSV do úložiště objektů blob. Když jste hotovi, klikněte na tlačítko **spustit**.  


### <a name="check-the-output"></a>Zkontrolujte výstup
1. Umožní má úloha spustit několik minut, dokud neuvidíte aktivity v **monitorování** pole. 

2. Pokud máte nástroj, který se standardně používáte k ověřit obsah úložiště objektů blob, že kontrola pomocí nástroje `azuresamldemoblob` kontejneru. Případně proveďte následující kroky na portálu Azure:

    1. V portálu, najdete `samldemo` úložiště účet a v rámci účtu, najděte `azuresamldemoblob` kontejneru. Zobrazí dva soubory v kontejneru: soubor, který obsahuje ukázkové tweetů a soubor CSV generované úlohu služby Stream Analytics.
    2. Klikněte pravým tlačítkem na vygenerovaný soubor a pak vyberte **Stáhnout**. 

   ![Stáhněte si výstup úlohy CSV z úložiště objektů Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otevřete generovaný soubor CSV. Vidět něco podobného jako v následujícím příkladu:  
   
   ![Stream Analytics Machine Learning, CSV zobrazení](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Zobrazit metriky
Také můžete zobrazit související funkce metrik Azure Machine Learning. Následující metriky funkce související se zobrazují v **monitorování** pole v okně úlohy:

* **Funkce požadavky** označuje počet požadavky odeslané na webové službě Machine Learning.  
* **Funkce události** označuje počet událostí v požadavku. Každý požadavek k webové službě Machine Learning obsahuje ve výchozím nastavení až 1 000 událostí.  


## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrace rozhraní REST API a strojového učení](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



