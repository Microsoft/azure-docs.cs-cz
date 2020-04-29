---
title: Předpověď požadavků na sdílení kol pomocí automatizovaného experimentu ML
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí automatizovaného strojového učení v Azure Machine Learning Studiu naučit a nasazovat model prognózy poptávky.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77088239"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Kurz: plánování požadavků na sdílení kol pomocí automatizovaného strojového učení
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu pomocí automatizovaného strojového učení nebo automatizovaného ML v Azure Machine Learning Studiu vytvoříte model prognózy časových řad, který předpovídá požadavky na pronájem pro službu sdílení kol.

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte a načtěte datovou sadu.
> * Proveďte konfiguraci a spuštění automatizovaného experimentu ML.
> * Prozkoumejte výsledky experimentů.
> * Nasaďte nejlepší model.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Machine Learning edice Enterprise. Pokud nemáte pracovní prostor, [Vytvořte pracovní prostor Enterprise Edition](how-to-manage-workspace.md). 
    * Automatizované strojové učení v Azure Machine Learning Studiu je dostupné jenom pro pracovní prostory Enterprise Edition. 
* Stažení datového souboru [Bike-No. csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Začínáme v Azure Machine Learning Studiu

Pro účely tohoto kurzu vytvoříte v aplikaci Azure Machine Learning Studio konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy u všech úrovní dovedností. Aplikace Studio není podporována v prohlížečích aplikace Internet Explorer.

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
    
    1. V místním počítači vyberte soubor **Bike-No. csv** . Jedná se o soubor, který jste stáhli jako [požadavek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Vybrat **Další**

       Po dokončení nahrávání se formulář nastavení a náhled předem vyplní podle typu souboru. 
       
    1. Ověřte, zda je formulář **nastavení a náhled** vyplněný následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro kurz
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělených
        Oddělovač|Jeden nebo více znaků pro určení hranice mezi&nbsp; oddělenými a nezávislými oblastmi v prostém textu nebo v jiných datových proudech. |Čárka
        Kódování|Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).| Použít hlavičky z prvního souboru
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.| Žádná

    1. Formulář **schématu** umožňuje další konfiguraci dat pro tento experiment. 
    
        1. V tomto příkladu můžete zvolit ignorování **neformálních** a **zaregistrovaných** sloupců. Tyto sloupce jsou rozčleněné do sloupce **CNT** , takže je proto nezahrneme.

        1. Také v tomto příkladu ponechte výchozí hodnoty **vlastností** a **typu**. 
        
        1. Vyberte **Další**.

    1. Na formuláři **potvrdit podrobnosti** ověřte, že se informace shodují s dříve vyplněnými **základními informacemi** o formulářích pro informace a **nastavení a ve verzi Preview** .

    1. Vyberte **vytvořit** a dokončete vytvoření datové sady.

    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.

    1. Vyberte **Další**.

## <a name="configure-experiment-run"></a>Konfigurace experimentového běhu

Po načtení a konfiguraci dat nastavte vzdálený cíl výpočtů a vyberte, který sloupec ve vašich datech chcete předpovědět.

1. Naplňte formulář pro **spuštění konfigurace** následujícím způsobem:
    1. Zadejte název experimentu:`automl-bikeshare`

    1. Jako cílový sloupec vyberte **CNT** , co chcete předpovědět. V tomto sloupci se zobrazuje celkový počet nájemné za podíl na kolo.

    1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte svůj cíl služby Compute. Automatizovaná ML podporuje jenom Azure Machine Learning výpočetní výkon. 

        Pole | Popis | Hodnota pro kurz
        ----|---|---
        Název výpočtu |Jedinečný název, který identifikuje váš výpočetní kontext.|kolo – COMPUTE
        Velikost&nbsp;virtuálního&nbsp;počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.|Standard_DS12_V2
        Minimální/maximální počet uzlů (v rozšířených nastaveních)| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů.|Minimální počet uzlů: 1<br>Maximální počet uzlů: 6
  
        1. Pokud chcete získat cíl výpočtů, vyberte **vytvořit** . 

            **Dokončení této akce trvá několik minut.** 

        1. Po vytvoření vyberte nový cíl služby COMPUTE z rozevíracího seznamu.

    1. Vyberte **Další**.

## <a name="select-task-type-and-settings"></a>Vybrat typ a nastavení úlohy

Dokončete instalaci pro automatický experiment ML zadáním typu úlohy Machine Learning a nastavení konfigurace.

1. Ve formuláři **typ úlohy a nastavení** vyberte jako typ úlohy Machine Learning **prognózu časových řad** .

1. Vyberte **Datum** ve **sloupci čas** a pole **Seskupit podle sloupce** ponechte prázdné. 

    1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lepší kontrolu nad úlohou školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat.

  
        Další&nbsp;konfigurace|Popis|Hodnota&nbsp;pro&nbsp;kurz
        ------|---------|---
        Primární metrika| Metrika vyhodnocení, podle které se algoritmus strojového učení měří.|Normalizovaný průměrný střední znak – chyba
        Automaticky featurization| Umožňuje předzpracování. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.| Povolení
        Vysvětlete nejlepší model (Preview)| Automaticky zobrazuje vysvětlení nejlepšího modelu vytvořeného pomocí automatizovaného ML.| Povolení
        Blokované algoritmy | Algoritmy, které chcete vyloučit z úlohy školení| Extrémní náhodné stromy
        Další nastavení prognózování| Tato nastavení vám pomůžou zlepšit přesnost modelu. <br><br> _**Horizont předpovědi**_: délka času do budoucna, kterou chcete předpovědět <br> _**Předpověď Target prodlevy:**_ jak daleko dozadu chcete vytvořit prodlevy cílové proměnné <br> _**Cílové posuvné okno**_: Určuje velikost posuvných oken, na které se budou generovat funkce, například *Max, min* a *Sum*. |Horizont předpovědi: 14 <br> Prodlevy&nbsp;cíle&nbsp;prognózy: žádné <br> Cílová&nbsp;velikost&nbsp;návratového okna&nbsp;: žádné
        Výstupní kritérium| Pokud je splněno kritérium, úloha školení se zastaví. |Čas&nbsp;úlohy&nbsp;školení (hodiny): 3 <br> Prahová&nbsp;hodnota&nbsp;skóre metriky: žádné
        Ověřování | Vyberte typ křížového ověření a počet testů.|Typ ověřování:<br>&nbsp;k skládání&nbsp;křížového ověřování <br> <br> Počet ověření: 5
        Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální&nbsp;počet&nbsp;souběžných iterací: 6
        
        Vyberte **Uložit**.

## <a name="run-experiment"></a>Spustit experiment

Pokud chcete svůj experiment spustit, vyberte **Dokončit**. Otevře se obrazovka s **podrobnostmi o spuštění** se **stavem spuštění** v horní části vedle čísla běhu. Tento stav se aktualizuje v průběhu experimentu.

>[!IMPORTANT]
> Příprava na Příprava spuštění experimentu trvá **10-15 minut** .
> Po spuštění bude **pro každou iteraci trvat více než 2-3 minut**.  <br> <br>
> V produkčním prostředí pravděpodobně nebudete chtít trochu, protože tento proces trvá čas. Během čekání doporučujeme začít zkoumat testované algoritmy na kartě **modely** , jak jsou dokončeny. 

##  <a name="explore-models"></a>Prozkoumat modely

Přejděte na kartu **modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle skóre metriky, jak jsou dokončeny. Pro účely tohoto kurzu se v horní části seznamu zobrazuje model, který vychází z nejvyšší hodnoty na základě zvolené **normalizované "průměrné chybové** metriky".

Až budete čekat na dokončení všech modelů experimentů, vyberte **název algoritmu** dokončeného modelu a prozkoumejte jeho podrobnosti o výkonu. 

Následující příklad prochází pomocí karet **Podrobnosti modelu** a **vizualizace** a zobrazí vlastnosti vybraného modelu, metriky a grafy výkonu. 

![Podrobnosti o spuštění](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Nasazení modelu

Automatizované strojové učení v Azure Machine Learning Studiu vám umožní nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrací modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazování do webové služby znamená, že společnost Shared na kolo má teď iterativní a škálovatelné webové řešení pro prognózování nároků na pronájem sdílené kol. 

Po dokončení spuštění přejděte zpět na stránku **podrobností o spuštění** a vyberte kartu **modely** .

V tomto kontextu experimentu se **StackEnsemble** považuje za nejlepší model, a to na základě **normalizované střední chybové** metriky chyby.  Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. V levém dolním rohu vyberte tlačítko **nasadit nejlepší model** .

1. Nasaďte podokno **nasadit model** následujícím způsobem:

    Pole| Hodnota
    ----|----
    Název nasazení| Bikeshare – nasazení
    Popis nasazení| nasazení požadavku na sdílení kol
    Typ výpočtu | Výběr instance služby Azure COMPUTE (ACI)
    Povolit ověřování| Zakázat. 
    Použití vlastních prostředků nasazení| Zakázat. Když se zakáže, umožní se automaticky vygenerovat výchozí soubor ovladače a soubor prostředí. 
    
    V tomto příkladu používáme výchozí hodnoty uvedené v nabídce *Upřesnit* . 

1. Vyberte **Nasadit**.  

    V horní části obrazovky **spuštění** se zobrazí zelená zpráva o úspěchu, že nasazení bylo úspěšně spuštěno. Průběh nasazení najdete  
    v podokně **doporučený model** v části **stav nasazení**.
    
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


>[!NOTE]
> Tato datová sada pro sdílení kol se pro tento kurz změnila. Tato datová sada byla zpřístupněna jako součást [soutěže Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) a byla původně dostupná prostřednictvím [kapitálového Bikeshareu](https://www.capitalbikeshare.com/system-data). Můžete ji také najít v rámci [databáze UCI Machine Learning](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Zdroj: Fanaee-T, hadi a gama, Joao, popis události kombinující detektory kompletu a znalosti na pozadí, pokrok v umělých Intelligencech (2013): PP. 1-15, Springer Berlín Heidelberg.
