---
title: 'Kurz: prognózování poptávky & AutoML'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí automatizovaného strojového učení v Azure Machine Learning Studiu naučit a nasazovat model prognózy poptávky.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 07/10/2020
ms.custom: automl
ms.openlocfilehash: 8b354abb98c56a572badf2421b0d7dbbd25f7a63
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921863"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Kurz: Předpověď poptávky pomocí automatizovaného strojového učení


V tomto kurzu pomocí automatizovaného strojového učení nebo automatizovaného ML v Azure Machine Learning Studiu vytvoříte model prognózy časových řad, který předpovídá poptávku pro službu pro sdílení kol.

Příklad klasifikačního modelu najdete v tématu [kurz: vytvoření klasifikačního modelu pomocí automatizovaného ml v Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte a načtěte datovou sadu.
> * Proveďte konfiguraci a spuštění automatizovaného experimentu ML.
> * Zadejte nastavení prognózy.
> * Prozkoumejte výsledky experimentů.
> * Nasaďte nejlepší model.

## <a name="prerequisites"></a>Předpoklady

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md). 

* Stažení [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) datového souboru

## <a name="get-started-in-azure-machine-learning-studio"></a>Začínáme v Azure Machine Learning Studiu

Pro účely tohoto kurzu vytvoříte v aplikaci Azure Machine Learning Studio konsolidované webové rozhraní, které zahrnuje nástroje machine learningu k provádění scénářů pro datové vědy u všech úrovní dovedností. Aplikace Studio není podporována v prohlížečích aplikace Internet Explorer.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **automatizovaná ml** .

1. Vyberte **+ Nový Automated ml Run**. 

## <a name="create-and-load-dataset"></a>Vytvořit a načíst datovou sadu

Před konfigurací experimentu nahrajte datový soubor do svého pracovního prostoru ve formě Azure Machine Learning datové sady. V takovém případě vám umožní zajistit, aby vaše data byla pro váš experiment vhodně formátována.

1. Ve formuláři **Vybrat datovou sadu** vyberte z rozevíracího seznamu **+ vytvořit sadu** možnost **z místních souborů** . 

    1. Ve formuláři **základní informace** zadejte název datové sady a zadejte volitelný popis. Typ datové sady by měl být výchozí pro **tabelární**, protože automatizované ML v Azure Machine Learning Studiu aktuálně podporuje jenom tabelární datové sady.
    
    1. V levém dolním rohu vyberte **Další** .

    1. Na formuláři **úložiště dat a výběr souboru** vyberte výchozí úložiště dat, které se automaticky nastavilo během vytváření pracovního prostoru, **workspaceblobstore (Azure Blob Storage)**. Toto je umístění úložiště, kam nahrajete datový soubor. 

    1. Vyberte **Procházet**. 
    
    1. Vyberte soubor **bike-no.csv** na místním počítači. Jedná se o soubor, který jste stáhli jako [požadavek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Vyberte **Další**.

       Po dokončení nahrávání se formulář nastavení a náhled předem vyplní podle typu souboru. 
       
    1. Ověřte, zda je formulář **nastavení a náhled** vyplněný následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro kurz
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělených
        Oddělovač|Jeden nebo více znaků pro určení hranice mezi &nbsp; oddělenými a nezávislými oblastmi v prostém textu nebo v jiných datových proudech. |Čárka
        Kódování|Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).| Použít hlavičky z prvního souboru
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.| Žádné

    1. Formulář **schématu** umožňuje další konfiguraci dat pro tento experiment. 
    
        1. V tomto příkladu můžete zvolit ignorování **neformálních** a **zaregistrovaných** sloupců. Tyto sloupce jsou rozčleněné do sloupce  **CNT** , takže je proto nezahrneme.

        1. Také v tomto příkladu ponechte výchozí hodnoty **vlastností** a **typu**. 
        
        1. Vyberte **Další**.

    1. Na formuláři **potvrdit podrobnosti** ověřte, že se informace shodují s dříve vyplněnými **základními informacemi** o formulářích pro informace a **nastavení a ve verzi Preview** .

    1. Vyberte **vytvořit** a dokončete vytvoření datové sady.

    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.

    1. Vyberte  **Další**.

## <a name="configure-experiment-run"></a>Konfigurace experimentového běhu

Po načtení a konfiguraci dat nastavte vzdálený cíl výpočtů a vyberte, který sloupec ve vašich datech chcete předpovědět.

1. Naplňte formulář pro **spuštění konfigurace** následujícím způsobem:
    1. Zadejte název experimentu: `automl-bikeshare`

    1. Jako cílový sloupec vyberte **CNT** , co chcete předpovědět. V tomto sloupci se zobrazuje celkový počet nájemné za podíl na kolo.

    1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte svůj cíl služby Compute. Automatizovaná ML podporuje jenom Azure Machine Learning výpočetní výkon. 

        Pole | Popis | Hodnota pro kurz
        ----|---|---
        Název výpočetních prostředků |Jedinečný název, který identifikuje váš výpočetní kontext.|kolo – COMPUTE
        &nbsp;Typ virtuálního počítače &nbsp;|Vyberte typ virtuálního počítače pro výpočetní výkon.|PROCESOR (jednotka ústředního zpracování)
        &nbsp;Velikost virtuálního počítače &nbsp;| Vyberte velikost virtuálního počítače pro výpočetní výkon.|Standard_DS12_V2
        Minimální/maximální počet uzlů| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů.|Minimální počet uzlů: 1<br>Maximální počet uzlů: 6
        Počet sekund nečinnosti před horizontálním navýšení kapacity | Doba nečinnosti před tím, než se cluster automaticky škáluje na minimální počet uzlů.|120 (výchozí)
        Pokročilá nastavení | Nastavení pro konfiguraci a autorizaci virtuální sítě pro svůj experiment.| Žádné
  
        1. Pokud chcete získat cíl výpočtů, vyberte **vytvořit** . 

            **Dokončení této akce trvá několik minut.** 

        1. Po vytvoření vyberte nový cíl služby COMPUTE z rozevíracího seznamu.

    1. Vyberte **Další**.

## <a name="select-forecast-settings"></a>Vybrat nastavení prognózy

Dokončete instalaci pro automatický experiment ML zadáním typu úlohy Machine Learning a nastavení konfigurace.

1. Ve formuláři **typ úlohy a nastavení** vyberte jako typ úlohy Machine Learning **prognózu časových řad** .

1. Vyberte **Datum** ve **sloupci čas** a nechte **identifikátory časových řad** prázdné. 

1. **Horizont předpovědi** je délka času do budoucna, kterou chcete předpovědět.  Zrušte zaškrtnutí políčka automaticky rozpoznat a v poli zadejte 14. 

1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lepší kontrolu nad úlohou školení a určují nastavení prognózy. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat.

    Další &nbsp; Konfigurace|Popis|Hodnota &nbsp; pro &nbsp; kurz
    ------|---------|---
    Primární metrika| Metrika vyhodnocení, podle které se algoritmus strojového učení měří.|Normalizovaný průměrný střední znak – chyba
    Vysvětlete nejlepší model| Automaticky zobrazuje vysvětlení nejlepšího modelu vytvořeného pomocí automatizovaného ML.| Povolit
    Blokované algoritmy | Algoritmy, které chcete vyloučit z úlohy školení| Extrémní náhodné stromy
    Další nastavení prognózování| Tato nastavení vám pomůžou zlepšit přesnost modelu. <br><br> _**Předpověď Target prodlevy:**_ jak daleko dozadu chcete vytvořit prodlevy cílové proměnné <br> _**Cílové posuvné okno**_: Určuje velikost posuvných oken, na které se budou generovat funkce, například *Max, min* a *Sum*. | <br><br>&nbsp;Prodlevy cíle prognózy &nbsp; : žádné <br> Cílová &nbsp; Velikost návratového &nbsp; okna &nbsp; : žádné
    Výstupní kritérium| Pokud je splněno kritérium, úloha školení se zastaví. |&nbsp;Čas úlohy školení &nbsp; (hodiny): 3 <br> &nbsp;Prahová hodnota skóre metriky &nbsp; : žádné
    Ověřování | Vyberte typ křížového ověření a počet testů.|Typ ověřování:<br>&nbsp;k skládání &nbsp; křížového ověřování <br> <br> Počet ověření: 5
    Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální počet &nbsp; souběžných &nbsp; iterací: 6
    
    Vyberte **Uložit**.

## <a name="run-experiment"></a>Spustit experiment

Pokud chcete svůj experiment spustit, vyberte **Dokončit**. Otevře se obrazovka s **podrobnostmi o spuštění**  se **stavem spuštění** v horní části vedle čísla běhu. Tento stav se aktualizuje v průběhu experimentu.

>[!IMPORTANT]
> Příprava na Příprava spuštění experimentu trvá **10-15 minut** .
> Po spuštění bude **pro každou iteraci trvat více než 2-3 minut**.  <br> <br>
> V produkčním prostředí pravděpodobně nebudete chtít trochu, protože tento proces trvá čas. Během čekání doporučujeme začít zkoumat testované algoritmy na kartě **modely** , jak jsou dokončeny. 

##  <a name="explore-models"></a>Prozkoumat modely

Přejděte na kartu **modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle skóre metriky, jak jsou dokončeny. Pro účely tohoto kurzu se v horní části seznamu zobrazuje model, který vychází z nejvyšší hodnoty na základě zvolené **normalizované "průměrné chybové** metriky".

Až budete čekat na dokončení všech modelů experimentů, vyberte **název algoritmu** dokončeného modelu a prozkoumejte jeho podrobnosti o výkonu. 

Následující příklad přechází na karty **Podrobnosti** a **metriky** pro zobrazení vlastností vybraného modelu, metrik a grafů výkonu. 

![Podrobnosti o spuštění](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Nasazení modelu

Automatizované strojové učení v Azure Machine Learning Studiu vám umožní nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrací modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazování do webové služby znamená, že společnost Shared na kolo má teď iterativní a škálovatelné webové řešení pro prognózování nároků na pronájem sdílené kol. 

Až se spuštění dokončí, přejděte zpátky na nadřazenou spuštěnou stránku výběrem možnosti **Spustit 1** v horní části obrazovky.

V části **nejlepší souhrn modelu** se **StackEnsemble** považuje za nejlepší model v souvislosti s tímto experimentem, a to na základě **normalizované chybové** metriky na střední úrovni.  

Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. Výběrem **StackEnsemble** otevřete stránku specifickou pro daný model.

1. Vyberte tlačítko **nasadit** , které se nachází v levé horní části obrazovky.

1. Nasaďte podokno **nasadit model** následujícím způsobem:

    Pole| Hodnota
    ----|----
    Název nasazení| Bikeshare – nasazení
    Popis nasazení| nasazení požadavku na sdílení kol
    Typ výpočetních prostředků | Výběr instance služby Azure COMPUTE (ACI)
    Povolit ověřování| Zakázat. 
    Použití vlastních prostředků nasazení| Zakázat. Když se zakáže, umožní se automaticky vygenerovat výchozí soubor ovladače a soubor prostředí. 
    
    V tomto příkladu používáme výchozí hodnoty uvedené v nabídce *Upřesnit* . 

1. Vyberte **Nasadit**.  

    V horní části obrazovky **spuštění** se zobrazí zelená zpráva o úspěchu s oznámením, že nasazení bylo úspěšně spuštěno. Průběh nasazení najdete v podokně **Souhrn modelu** v části **stav nasazení**.
    
Po úspěšném nasazení budete mít provozní webovou službu, která vygeneruje předpovědi. 

Pokud chcete získat další informace o tom, jak používat novou webovou službu, a testovat předpovědi pomocí integrované Azure Machine Learning podpory Power BI, přejděte k [**dalším krokům**](#next-steps) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se o jejich uložení budou mnohem lépe ukládat. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na váš účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstraní instanci nasazení.

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Machine Learning studia. 

1. Přejít na [Azure Machine Learning Studio](https://ml.azure.com/). Přejděte do pracovního prostoru a vlevo pod podoknem **assety** vyberte **koncové body**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí automatizovaného ML v sadě Azure Machine Learning Studio vytvořili a nasadili model prognózy časových řad, který předpovídá požadavky na pronájem na kolo pro sdílení kol. 

Postup vytvoření Power BI podporovaného schématu pro usnadnění spotřeby nově nasazené webové služby najdete v tomto článku:

> [!div class="nextstepaction"]
> [Využívání webové služby](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).
+ Další informace o metrikách klasifikace a grafech najdete v článku [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md) .
+ Přečtěte si další informace o [featurization](how-to-configure-auto-features.md#featurization).
+ Přečtěte si další informace o [profilování dat](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Tato datová sada pro sdílení kol se pro tento kurz změnila. Tato datová sada byla zpřístupněna jako součást [soutěže Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) a byla původně dostupná prostřednictvím [kapitálového Bikeshareu](https://www.capitalbikeshare.com/system-data). Můžete ji také najít v rámci [databáze UCI Machine Learning](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Zdroj: Fanaee-T, hadi a gama, Joao, popis události kombinující detektory kompletu a znalosti na pozadí, pokrok v umělých Intelligencech (2013): PP. 1-15, Springer Berlín Heidelberg.
