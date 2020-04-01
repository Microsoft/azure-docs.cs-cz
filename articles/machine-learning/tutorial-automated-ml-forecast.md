---
title: Prognóza poptávky po sdílení jízdních kol pomocí automatizovaného experimentu ML
titleSuffix: Azure Machine Learning
description: Zjistěte, jak trénovat a nasazovat model prognózy poptávky pomocí automatizovaného strojového učení ve studiu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088239"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Kurz: Prognóza poptávky po sdílení jízdních kol pomocí automatizovaného strojového učení
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu použijete automatizované strojové učení nebo automatizované ML ve studiu Azure Machine Learning k vytvoření modelu prognózy časových řad k předvídání poptávky po pronájmu pro službu sdílení jízdních kol.

V tomto kurzu se dozvíte, jak provést následující úkoly:

> [!div class="checklist"]
> * Vytvořte a načtěte datovou sadu.
> * Nakonfigurujte a spusťte automatizovaný experiment ML.
> * Prozkoumejte výsledky experimentu.
> * Nasaďte nejlepší model.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Machine Learning pro enterprise edici. Pokud pracovní prostor nemáte, [vytvořte pracovní prostor enterprise edition](how-to-manage-workspace.md). 
    * Automatizované strojové učení ve studiu Azure Machine Learning je dostupné jenom pro pracovní prostory edice Enterprise. 
* Stáhněte si datový soubor [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Začínáme ve studiu Azure Machine Learning

V tomto kurzu vytvoříte automatizovaný experiment ML spuštěný ve studiu Azure Machine Learning, což je konsolidované rozhraní, které obsahuje nástroje strojového učení k provádění scénářů datové vědy pro odborníky v oblasti datové vědy všech úrovní dovedností. Studio není v prohlížečích Internet Explorer podporováno.

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. Vyberte předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Možnost Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **Automatizovaná ml.**

1. Vyberte **možnost +Nový automatizovaný běh ML**. 

## <a name="create-and-load-dataset"></a>Vytvoření a načtení datové sady

Než experiment nakonfigurujete, nahrajte datový soubor do pracovního prostoru ve formě datové sady Azure Machine Learning. Díky tomu můžete zajistit, že vaše data jsou formátována odpovídajícím způsobem pro váš experiment.

1. Ve formuláři **Vybrat datovou sadu** vyberte z rozevíracího souboru **+Vytvořit datovou sadu** z **místních souborů.** 

    1. Ve formuláři **Základní informace** zadejte název datové sady a poskytněte volitelný popis. Typ datové sady by měl být ve výchozím nastavení **nastaven jako tabulkový**, protože automatizovaná ml ve studiu Azure Machine Learning studio aktuálně podporuje jenom tabulkové datové sady.
    
    1. V levém dolním rohu vyberete **další.**

    1. Ve formuláři **Datastore a výběru souborů** vyberte výchozí úložiště dat, které bylo automaticky nastaveno během vytváření pracovního prostoru, **workspaceblobstore (Azure Blob Storage).** Toto je umístění úložiště, kam nahrajete datový soubor. 

    1. Vyberte **Procházet**. 
    
    1. V místním počítači zvolte soubor **bike-no.csv.** Jedná se o soubor, který jste stáhli jako [předpoklad](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Vybrat **další**

       Po dokončení nahrávání je formulář Nastavení a náhled předem vyplněn na základě typu souboru. 
       
    1. Ověřte, zda je formulář **Nastavení a náhled** naplněn následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro výuku
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělený
        Oddělovač|Jeden nebo více znaků pro určení&nbsp; hranice mezi samostatnými nezávislými oblastmi ve formátu prostého textu nebo jiných datových proudech. |Čárka
        Kódování|Určuje, jaký bit do tabulky schématu znaků použít ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Označuje, jak bude zpracováno záhlaví datové sady, pokud existuje.| Použití záhlaví z prvního souboru
        Přeskočit řádky | Označuje, kolik řádků, pokud existuje, je v datové sadě přeskočeno.| Žádný

    1. Formulář **Schéma** umožňuje další konfiguraci dat pro tento experiment. 
    
        1. V tomto příkladu zvolte ignorovat **příležitostné** a **registrované** sloupce. Tyto sloupce jsou rozpis sloupec **CNT,** takže je nebudeme obsahovat.

        1. Také v tomto příkladu ponechte výchozí hodnoty **vlastnosti** a **typ**. 
        
        1. Vyberte **další**.

    1. Ve formuláři **Potvrdit podrobnosti** ověřte, zda se informace shodují s tím, co bylo dříve vyplněno ve formulářích **Základní informace** a Nastavení **a Náhled.**

    1. Vyberte **Vytvořit,** chcete-li dokončit vytvoření datové sady.

    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.

    1. Vyberte **další**.

## <a name="configure-experiment-run"></a>Konfigurovat spuštění experimentu

Po načtení a konfiguraci dat nastavte vzdálený výpočetní cíl a vyberte sloupec v datech, který chcete předpovědět.

1. Formulář **Konfigurovat spuštění** naplňte následujícím způsobem:
    1. Zadejte název experimentu:`automl-bikeshare`

    1. Vyberte **cnt** jako cílový sloupec, co chcete předpovědět. Tento sloupec udává počet celkových pronájmů podílu na kole.

    1. Vyberte **Vytvořit nový výpočetní výkon** a nakonfigurujte svůj výpočetní cíl. Automatizované ML podporuje jenom azure machine learning výpočetní prostředky. 

        Pole | Popis | Hodnota pro výuku
        ----|---|---
        Název výpočetního výkonu |Jedinečný název, který identifikuje váš výpočetní kontext.|bike-počítat
        Velikost&nbsp;&nbsp;virtuálního počítače| Vyberte velikost virtuálního počítače pro vaše výpočty.|Standard_DS12_V2
        Min / Max uzly (v pokročilých nastaveních)| Chcete-li daty profilu, musíte zadat 1 nebo více uzlů.|Min uzly: 1<br>Maximální počet uzlů: 6
  
        1. Vyberte **Vytvořit,** chcete-li získat výpočetní cíl. 

            **To trvá několik minut na dokončení.** 

        1. Po vytvoření vyberte nový výpočetní cíl z rozevíracího seznamu.

    1. Vyberte **další**.

## <a name="select-task-type-and-settings"></a>Výběr typu a nastavení úkolu

Dokončete nastavení automatizovaného experimentu ML zadáním typu úlohy strojového učení a nastavení konfigurace.

1. Ve formuláři **Typ úkolu a nastavení** vyberte jako typ úlohy strojového učení **prognózy časových řad.**

1. Jako sloupec **Čas** vyberte **datum** a ponechte **možnost Seskupit podle sloupců** prázdné. 

    1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lépe řídit úlohu školení. V opačném případě jsou výchozí hodnoty použity na základě výběru experimentu a dat.

  
        Další&nbsp;konfigurace|Popis|Hodnota&nbsp;&nbsp;pro výuku
        ------|---------|---
        Primární metrika| Metrika vyhodnocení, podle které bude algoritmus strojového učení měřen.|Normalizovaná střední kvadratická chyba kořenového adresáře
        Automatická featurizace| Povolí předběžné zpracování. To zahrnuje automatické čištění dat, přípravu a transformaci pro generování syntetických funkcí.| Povolení
        Vysvětlete nejlepší model (náhled)| Automaticky zobrazuje vysvětlitelnost nejlepšího modelu vytvořeného automatizovaným ml.| Povolení
        Blokované algoritmy | Algoritmy, které chcete vyloučit z tréninkové úlohy| Extrémní náhodné stromy
        Další nastavení prognózy| Tato nastavení pomáhají zlepšit přesnost modelu <br><br> _**Horizont prognózy:**_ doba do budoucna, kterou chcete předpovědět <br> _**Cíl prognózy zaostává:**_ jak daleko dozadu chcete vytvořit zpoždění cílové proměnné <br> _**Cílové rolovací okno**_: určuje velikost odvalovacího okna, nad kterými budou generovány prvky, jako je *max, min* a *sum*. |Horizont předpovědí: 14 <br> Prodlevy v cílové&nbsp;&nbsp;prognóze: Žádné <br> Velikost&nbsp;&nbsp;cílového klouzavého okna:&nbsp;Žádná
        Kritérium ukončení| Pokud je splněna kritéria, je úloha školení zastavena. |Doba&nbsp;&nbsp;tréninku (hodiny): 3 <br> Prahová&nbsp;hodnota metriky:&nbsp;Žádná
        Ověřování | Zvolte typ křížového ověření a počet testů.|Typ ověření:<br>&nbsp;k-fold&nbsp;křížové ověření <br> <br> Počet ověření: 5
        Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální&nbsp;počet&nbsp;souběžných iterací: 6
        
        Vyberte **Uložit**.

## <a name="run-experiment"></a>Spustit experiment

Chcete-li experiment spustit, vyberte **možnost Dokončit**. Otevře se obrazovka **Podrobnosti spuštění** se **stavem Spustit** v horní části vedle čísla spuštění. Tento stav se aktualizuje v průběhu experimentu.

>[!IMPORTANT]
> Příprava trvá **10-15 minut,** než se připraví experiment.
> Po spuštění trvá **2-3 minuty více pro každou iteraci**.  <br> <br>
> Ve výrobě byste pravděpodobně odejít na chvíli, protože tento proces vyžaduje čas. Zatímco budete čekat, doporučujeme začít zkoumat testované algoritmy na kartě **Modely,** jakmile budou dokončeny. 

##  <a name="explore-models"></a>Prozkoumejte modely

Přejděte na kartu **Modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle metriky skóre, jak je dokončí. Pro účely tohoto kurzu je model, který skóre nejvyšší na základě zvolené **normalizované kořenové střední kvadratální chyba metrika** je v horní části seznamu.

Zatímco čekáte na dokončení všech modelů experimentu, vyberte **název algoritmu** dokončeného modelu a prozkoumejte podrobnosti o jeho výkonu. 

Následující příklad prochází **podrobnosti modelu** a **vizualizace** karty zobrazit vlastnosti vybraného modelu, metriky a grafy výkonu. 

![Spustit podrobnosti](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Nasazení modelu

Automatizované strojové učení ve studiu Azure Machine Learning vám umožní nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrace modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazení do webové služby znamená, že společnost pro sdílení jízdních kol má nyní iterativní a škálovatelné webové řešení pro prognózování poptávky po pronájmu sdílení jízdních kol. 

Po dokončení běhu přejděte zpět na stránku **Podrobnosti spustit** a vyberte kartu **Modely.**

V tomto kontextu experimentu **StackEnsemble** je považován za nejlepší model, na základě **normalizované kořenové střední kvadratý chyba metriky.**  Tento model nasazujeme, ale upozorňujeme, že dokončení nasazení trvá přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. V levém dolním rohu vyberte tlačítko **Nasadit nejlepší model.**

1. Naplnit **podokno nasazení modelu** takto:

    Pole| Hodnota
    ----|----
    Název nasazení| bikeshare-nasadit
    Popis nasazení| nasazení poptávky po sdílení jízdních kol
    Typ výpočtu | Výběr výpočetní instance Azure (ACI)
    Povolení ověřování| Zakázat. 
    Použití vlastních prostředků nasazení| Zakázat. Zakázání umožňuje automatické generování výchozího souboru ovladače (bodovacího skriptu) a souboru prostředí. 
    
    V tomto příkladu použijeme výchozí hodnoty uvedené v nabídce *Upřesnit.* 

1. Vyberte **Nasadit**.  

    Zelená zpráva o úspěchu se zobrazí v horní části obrazovky **Spustit** uvedeno, že nasazení bylo úspěšně spuštěno. Pokrok v nasazení lze nalézt  
    v podokně **Doporučený model** v části **Nasazovat stav**.
    
Jakmile nasazení proběhne úspěšně, máte provozní webové služby pro generování předpovědi. 

Přejděte k [**dalším krokům,**](#next-steps) kde se dozvíte víc o tom, jak využít novou webovou službu, a otestujte předpovědi pomocí integrované podpory Azure Machine Learning u Power BI.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než soubory dat a experimentů, takže jejich uložení bude stát více. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na svůj účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. V opačném případě odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstranění instance nasazení

Odstraňte jenom instanci nasazení ze studia Azure Machine Learning, pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum. 

1. Přejděte do [studia Azure Machine Learning studio](https://ml.azure.com/). Přejděte do pracovního prostoru a vlevo v podokně **Datové zdroje** vyberte **Koncové body**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **Pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili automatizované ML ve studiu Azure Machine Learning k vytvoření a nasazení modelu prognózy časových řad, který předpovídá poptávku po pronájmu sdílení jízdních kol. 

V tomto článku najdete postup, jak vytvořit schéma podporované Power BI, které usnadní spotřebu nově nasazené webové služby:

> [!div class="nextstepaction"]
> [Využívání webové služby](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Tato datová sada sdílení jízdních kol byla upravena pro tento kurz. Tento datový soubor byl zpřístupněn v rámci [soutěže Vaggle](https://www.kaggle.com/c/bike-sharing-demand/data) a byl původně k dispozici prostřednictvím [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). Lze jej také nalézt v [databázi Strojového učení UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Zdroj: Fanaee-T, Hadi a Gama, Joao, Event labeling kombinující detektory souborů a znalosti pozadí, Pokrok v umělé inteligenci (2013): pp. 1-15, Springer Berlin Heidelberg.
