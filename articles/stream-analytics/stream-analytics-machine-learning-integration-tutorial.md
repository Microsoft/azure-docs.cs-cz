---
title: Integrace Azure Stream Analytics s Azure Machine Learning
description: Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning pomocí uživatelem definované funkce.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067095"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Provádění analýzy mínění pomocí Azure Stream Analytics a Azure Machine Learning Studio (klasické)

Tento článek popisuje, jak rychle nastavit jednoduchou úlohu Azure Stream Analytics, která integruje Azure Machine Learning Studio (klasické). Model analýzy mínění strojového učení z Galerie Cortana Intelligence používáte k analýze streamování textových dat a určení skóre mínění v reálném čase. Pomocí cortany intelligence suite můžete tento úkol provést bez obav o složitosti vytváření modelu analýzy mínění.

> [!TIP]
> Důrazně doporučujeme používat [UDF Azure Machine Learning](machine-learning-udf.md) místo Azure Machine Learning Studio (klasické) UDF pro lepší výkon a spolehlivost.

Co se dozvíte z tohoto článku, můžete použít na scénáře, jako jsou tyto:

* Analýza mínění v reálném čase na streamování dat Twitteru.
* Analýza záznamů o chatech se zákazníky s pracovníky podpory.
* Hodnocení komentářů na fórech, blozích a videích. 
* Mnoho dalších scénářů prediktivního hodnocení v reálném čase.

V reálném scénáři byste získali data přímo z datového proudu Twitteru. Pro zjednodušení kurzu je napsántak, aby úloha Streaming Analytics získá tweety ze souboru CSV v úložišti objektů Blob Azure. Můžete vytvořit vlastní soubor CSV nebo můžete použít ukázkový soubor CSV, jak je znázorněno na následujícím obrázku:

![Ukázkové tweety zobrazené v souboru CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Úloha Analýzy streamování, kterou vytvoříte, použije model analýzy mínění jako uživatelem definovanou funkci (UDF) na ukázková textová data z úložiště objektů blob. Výstup (výsledek analýzy mínění) je zapsán do stejného úložiště objektů blob v jiném souboru CSV. 

Následující obrázek ukazuje tuto konfiguraci. Jak již bylo uvedeno, pro realističtější scénář můžete nahradit úložiště objektů blob streamováním dat Twitteru ze vstupu služby Azure Event Hubs. Kromě toho můžete vytvořit vizualizaci agregovaného mínění v reálném čase [v Microsoft Power BI.](https://powerbi.microsoft.com/)    

![Přehled integrace strojového učení Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste provedli následující akce:

* Aktivní předplatné Azure.
* Soubor CSV s některými daty v něm. Můžete si stáhnout soubor zobrazený dříve z [GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), nebo si můžete vytvořit vlastní soubor. Pro tento článek se předpokládá, že používáte soubor z GitHubu.

Na vysoké úrovni k dokončení úkolů uvedených v tomto článku postupujte takto:

1. Vytvořte účet úložiště Azure a kontejner úložiště objektů blob a nahrajte vstupní soubor ve formátu CSV do kontejneru.
3. Přidejte model analýzy mínění z Galerie Cortana Intelligence do pracovního prostoru Azure Machine Learning Studio (klasické) a nasaďte tento model jako webovou službu v pracovním prostoru Machine Learning.
5. Vytvořte úlohu Stream Analytics, která volá tuto webovou službu jako funkci, abyste určili mínění pro zadávání textu.
6. Spusťte úlohu Stream Analytics a zkontrolujte výstup.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Vytvoření kontejneru úložiště a nahrání vstupního souboru CSV
V tomto kroku můžete použít libovolný soubor CSV, například ten, který je k dispozici na GitHubu.

1. Na webu Azure Portal klikněte na Vytvořit**účet úložiště****úložiště** >  **prostředků** > .

2. Zadejte název`samldemo` (v příkladu). Název můžete použít pouze malá písmena a čísla a musí být jedinečný v rámci Azure. 

3. Zadejte existující skupinu prostředků a určete umístění. Pro umístění doporučujeme, aby všechny prostředky vytvořené v tomto kurzu použít stejné umístění.

    ![poskytnout podrobnosti o účtu úložiště](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Na webu Azure Portal vyberte účet úložiště. V okně účtu úložiště klikněte ** + &nbsp;** na **Kontejnery** a potom klikněte na Kontejner k vytvoření úložiště objektů blob.

    ![Vytvoření kontejneru úložiště objektů blob pro vstup](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Zadejte název kontejneru`azuresamldemoblob` (v příkladu) a ověřte, zda je **typ aplikace Access** nastaven na objekt **Blob**. Až to budete mít, klikněte na **OK**.

    ![určení podrobností kontejneru objektů blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. V okně **Kontejnery** vyberte nový kontejner, který otevře okno pro tento kontejner.

7. Klikněte na **Odeslat**.

    ![Tlačítko "Nahrát" pro kontejner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. V okně **Nahrát objekt blob** nahrajte soubor **sampleinput.csv,** který jste stáhli dříve. U **typu Objekt blob**vyberte **objekt blob bloku** a nastavte velikost bloku na 4 MB, což je pro tento kurz dostačující.

9. Klikněte na tlačítko **Nahrát** v dolní části okna.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Přidání modelu analýzy mínění z Galerie cortana intelligence

Teď, když jsou ukázková data v objektu blob, můžete povolit model analýzy mínění v Galerii Cortana Intelligence Gallery.

1. Přejděte na stránku [modelu analýzy prediktivních mínění](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) v Galerii cortana intelligence.  

2. Klepněte na tlačítko **Otevřít v aplikaci Studio**.  
   
   ![Stream Analytics Machine Learning, otevřené Studio strojového učení](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Chcete-li přejít do pracovního prostoru, přihlaste se. Vyberte umístění.

4. V dolní části stránky klikněte na **Spustit.** Proces běží, což trvá asi minutu.

   ![spustit experiment v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po úspěšném spuštění procesu vyberte v dolní části stránky možnost **Nasadit webovou službu.**

   ![nasazení experimentu v Machine Learning Studiu jako webové služby](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Chcete-li ověřit, zda je model analýzy mínění připraven k použití, klepněte na tlačítko **Testovat.** Zadejte zadávání textu, například "Miluji Microsoft". 

   ![testovací experiment ve studiu Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Pokud test funguje, zobrazí se výsledek podobný následujícímu příkladu:

   ![výsledky testů v Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Ve sloupci **Aplikace** klikněte na odkaz **Sešit v Excelu 2010 nebo starším** a stáhněte si excelový sešit. Sešit obsahuje klíč rozhraní API a adresu URL, kterou později potřebujete k nastavení úlohy Stream Analytics.

    ![Stream Analytics Machine Learning, rychlý pohled](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Vytvoření úlohy Stream Analytics, která používá model Machine Learning

Teď můžete vytvořit úlohu Stream Analytics, která čte ukázkové tweety ze souboru CSV v úložišti objektů blob. 

### <a name="create-the-job"></a>Vytvoření úlohy

1. Přejděte na [portál Azure](https://portal.azure.com).  

2. Klikněte **na Vytvořit zdroj** > **Internet věcí** > **Stream Analytics úlohy**. 

3. Pojmenujte `azure-sa-ml-demo`úlohu , zadejte odběr, zadejte existující skupinu prostředků nebo vytvořte novou a vyberte umístění úlohy.

   ![určení nastavení pro novou úlohu Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurace vstupu úlohy
Úloha získá svůj vstup ze souboru CSV, který jste nahráli dříve do úložiště objektů blob.

1. Po vytvoření úlohy klikněte v části **Topologie úlohy** v okně úlohy na možnost **Vstupy.**    

2. V okně **Vstupy** klikněte na Přidat úložiště objektů blob > **vstupu datového proudu.****Blob storage**

3. Vyplňte okno **úložiště objektů blob** s těmito hodnotami:

   
   |Pole  |Hodnota  |
   |---------|---------|
   |**Vstupní alias** | Použijte název `datainput` a z předplatného vyberte **Vybrat úložiště objektů blob.**       |
   |**Účet úložiště**  |  Vyberte dříve vytvořený účet úložiště.  |
   |**Kontejner**  | Vyberte kontejner, který`azuresamldemoblob`jste vytvořili dříve ( )        |
   |**Formát serializace události**  |  Vybrat **CSV**       |

   ![Nastavení nového vstupu úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klikněte na **Uložit**.

### <a name="configure-the-job-output"></a>Konfigurace výstupu úlohy
Úloha odesílá výsledky do stejného úložiště objektů blob, kde získá vstup. 

1. V části **Topologie úlohy** v okně úlohy klikněte na možnost **Výstupy.**  

2. V okně **Výstupy** klikněte na **Přidat** >**úložiště objektů blob**a pak přidejte výstup s aliasem `datamloutput`. 

3. Vyplňte okno **úložiště objektů blob** s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   |**Výstupní alias** | Použijte název `datamloutput` a z předplatného vyberte **Vybrat úložiště objektů blob.**       |
   |**Účet úložiště**  |  Vyberte dříve vytvořený účet úložiště.  |
   |**Kontejner**  | Vyberte kontejner, který`azuresamldemoblob`jste vytvořili dříve ( )        |
   |**Formát serializace události**  |  Vybrat **CSV**       |

   ![Nastavení pro nový výstup úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klikněte na **Uložit**.   


### <a name="add-the-machine-learning-function"></a>Přidání funkce Strojového učení 
Dříve jste publikovali model Machine Learning do webové služby. V tomto scénáři při spuštění úlohy analýzy datového proudu odešle každý ukázkový tweet ze vstupu do webové služby pro analýzu mínění. Webová služba Machine Learning`positive`vrátí `neutral`mínění `negative`( , , nebo ) a pravděpodobnost, že tweet bude kladný. 

V této části kurzu definujete funkci v úloze analýzy datového proudu. Funkce může být vyvolána k odeslání tweet u webové služby a získat odpověď zpět. 

1. Ujistěte se, že máte adresu URL webové služby a klíč rozhraní API, které jste stáhli dříve v sešitu aplikace Excel.

2. Přejděte do okna úlohy > **funkce** > **+ Přidání** > **AzureML**

3. Vyplňte okno **funkce Azure Machine Learning** s těmito hodnotami:

   |Pole  |Hodnota  |
   |---------|---------|
   | **Alias funkce** | Použijte název `sentiment` a ručně vyberte **Poskytněte nastavení funkce Azure Machine Learning,** což vám dává možnost zadat adresu URL a klíč.      |
   | **Adresa URL**| Vložte adresu URL webové služby.|
   |**Klíč** | Vložte klíč rozhraní API. |
  
   ![Nastavení pro přidání funkce Strojového učení do úlohy Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klikněte na **Uložit**.

### <a name="create-a-query-to-transform-the-data"></a>Vytvoření dotazu pro transformaci dat

Stream Analytics používá deklarativní dotaz založený na SQL ke kontrole vstupu a jeho zpracování. V této části vytvoříte dotaz, který přečte každý pípání ze vstupu a potom vyvolá funkci Machine Learning k provedení analýzy mínění. Dotaz pak odešle výsledek do výstupu, který jste definovali (úložiště objektů blob).

1. Vraťte se do okna přehledu úloh.

2.  V části **Topologie úlohy**klikněte na pole **Dotaz.**

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

    Dotaz vyvolá funkci, kterou jste`sentiment`vytvořili dříve ( ) za účelem provedení analýzy mínění u každého pípání ve vstupu. 

4. Kliknutím na **Uložit** dotaz uložte.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Nyní můžete spustit úlohu Stream Analytics.

### <a name="start-the-job"></a>Spuštění úlohy
1. Vraťte se do okna přehledu úloh.

2. V horní části okna klikněte na **Tlačítko Start.**

3. V **úloze Start**vyberte **Vlastní**a pak vyberte jeden den před odesláním souboru CSV do úložiště objektů blob. Až budete hotovi, klikněte na **Start**.  


### <a name="check-the-output"></a>Zkontrolujte výstup
1. Nechte úlohu běžet několik minut, dokud se nezobrazí aktivita v poli **Monitorování.** 

2. Pokud máte nástroj, který běžně používáte ke kontrole obsahu úložiště objektů `azuresamldemoblob` blob, použijte tento nástroj ke kontrole kontejneru. Případně proveďte na webu Azure Portal následující kroky:

    1. Na portálu najděte `samldemo` účet úložiště a v `azuresamldemoblob` rámci účtu najděte kontejner. V kontejneru se zobrazí dva soubory: soubor, který obsahuje ukázkové tweety a soubor CSV generovaný úlohou Stream Analytics.
    2. Klepněte pravým tlačítkem myši na vygenerovaný soubor a vyberte příkaz **Stáhnout**. 

   ![Stažení výstupu úlohy CSV z úložiště objektů Blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otevřete vygenerovaný soubor CSV. Zobrazí se něco jako následující příklad:  
   
   ![Stream Analytics Machine Learning, zobrazení CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Zobrazit metriky
Můžete také zobrazit metriky související s funkcemi Azure Machine Learning. Následující metriky související s funkcemi jsou zobrazeny v poli **Monitorování** v okně úlohy:

* **Požadavky na funkce** označují počet požadavků odeslaných webové službě Machine Learning.  
* **Události funkce** označuje počet událostí v požadavku. Ve výchozím nastavení obsahuje každý požadavek na webovou službu Machine Learning až 1 000 událostí.  


## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrace rozhraní REST API a strojového učení](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



