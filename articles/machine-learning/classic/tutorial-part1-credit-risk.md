---
title: 'Kurz pro ML Studio (Classic): předpověď úvěrového rizika – Azure'
description: Podrobný kurz, který ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning Studio (Classic). Tento kurz je první částí série kurzů se třemi částmi.  Ukazuje, jak vytvořit pracovní prostor, nahrát data a vytvořit experiment.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 946435175ea5cd366103bc1254bae0d9afe0926e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325809"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Kurz 1: předpověď úvěrového rizika – Azure Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

V tomto kurzu se podíváme na proces vývoje řešení prediktivní analýzy. V Machine Learning Studio (Classic) vyvíjíte jednoduchý model.  Model pak nasadíte jako Azure Machine Learning webovou službu.  Tento nasazený model může vytvářet předpovědi s využitím nových dat. Tento kurz je **první částí série kurzů se třemi částmi**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je složitý problém, ale v tomto kurzu se tento kurz zjednodušuje. Použijete ho jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí Microsoft Azure Machine Learning Studio (Classic). Pro toto řešení použijete Azure Machine Learning Studio (Classic) a Machine Learning webovou službu.  

V tomto kurzu se třemi částmi začínáte veřejně dostupnými daty o úvěrovém riziku.  Pak vyvíjíte a naučíte prediktivní model.  Nakonec model nasadíte jako webovou službu.

V této části kurzu: 
 
> [!div class="checklist"]
> * Vytvořit pracovní prostor Machine Learning Studio (Classic)
> * Nahrání existujících dat
> * Vytvoření experimentu

Pak můžete tento experiment použít k [proškolování modelů v části 2](tutorial-part2-credit-risk-train.md) a pak [je nasadit v části 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá, že jste předtím používali Machine Learning Studio (Classic) alespoň jednou a že máte zkušenosti s koncepty machine learningu. Bere ale v úvahu, že nejste odborníkem ani na jedno.

Pokud jste ještě nikdy nepoužívali **Azure Machine Learning Studio (Classic)** , můžete začít s rychlým startem, [vytvořit první experiment pro datové vědy v Azure Machine Learning Studio (Classic)](create-experiment.md). Rychlý Start vás provede procesem Machine Learning Studio (klasický) poprvé. Ukáže vám základy toho, jak pomocí myši přetáhnout moduly do experimentu, vzájemně je propojit, spustit experiment a prohlédnout si výsledky.


> [!TIP] 
> Pracovní kopii experimentu, který vyvíjíte v tomto kurzu, můžete najít v [Azure AI Gallery](https://gallery.azure.ai). Přejděte na **[kurz – předpověď úvěrového rizika](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** a kliknutím na **otevřít v studiu** Stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio (Classic).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Vytvořit pracovní prostor Machine Learning Studio (Classic)

Pokud chcete použít Machine Learning Studio (Classic), musíte mít pracovní prostor Microsoft Azure Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.  

Pokud chcete vytvořit pracovní prostor, přečtěte si téma [Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (Classic)](create-workspace.md).

Po vytvoření pracovního prostoru otevřete Machine Learning Studio (Classic) ( [https://studio.azureml.net/Home](https://studio.azureml.net/Home) ). Pokud máte více než jeden pracovní prostor, můžete pracovní prostor vybrat na panelu nástrojů v pravém horním rohu okna.

![Výběr pracovního prostoru v nástroji Studio (klasický)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Pokud jste vlastníkem pracovního prostoru, můžete nasdílet experimenty, na kterých právě pracujete, a to tak, že v pracovním prostoru vyzvete ostatní. To můžete provést v Machine Learning Studio (Classic) na stránce **Nastavení** . Pro každého uživatele potřebujete pouze účet účet Microsoft nebo organizace.
> 
> Na stránce **Nastavení** klikněte na **Uživatelé** a potom v dolní části okna klikněte na **pozvat další uživatele** .
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Nahrání existujících dat

Pro vývoj prediktivního modelu pro úvěrové riziko budete potřebovat data, která můžete použít ke školení a testování modelu. V tomto kurzu použijete datovou sadu "UCI Statlog (německá úvěrová data)" z úložiště UC Irvine Machine Learning. Můžete ji najít tady:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Použijete soubor s názvem **němčina. data**. Stáhněte si tento soubor na svůj místní pevný disk.  

**Němčina. data** DataSet obsahuje řádky 20 proměnných pro 1000 minulých žadatelů pro kredit. Tyto 20 proměnných představují sadu funkcí datové sady ( *vektor funkce* ), která poskytuje identifikovatelné charakteristiky pro jednotlivé kandidáty na kredit. Další sloupec v každém řádku představuje vypočtené úvěrové riziko žadatele s 700 žadateli, kteří se identifikovali jako nízké úvěrové riziko a 300 jako vysoké riziko.

Web UCI poskytuje popis atributů vektoru funkce pro tato data. Tato data zahrnují finanční informace, historii kreditů, stav zaměstnanosti a osobní údaje. Pro každého žadatele bylo uvedeno binární hodnocení, které označuje, zda se jedná o nízké nebo vysoké úvěrové riziko. 

Tato data použijete k vytvoření výukového modelu prediktivní analýzy. Až budete hotovi, váš model by měl být schopný přijmout vektor funkce pro novou osobu a odhadnout, jestli se jedná o nízké nebo vysoké úvěrové riziko.  

Tady je zajímavou značku.

Popis datové sady na webu UCI uvádí, k čemu v případě netřídění úvěrového rizika u osoby nebudete zařadit.
Pokud model předpovídá vysoké úvěrové riziko pro někoho, kdo je ve skutečnosti nízké úvěrové riziko, model provedl chybnou klasifikaci.

Ale reverzní nevratná klasifikace je pro finanční instituce pětkrát levnější: Pokud model předpovídá nízké úvěrové riziko pro někoho, kdo je skutečně vysokým úvěrovým rizikem.

Proto chcete svůj model naučit, aby náklady na tento druhý typ chybných klasifikací byly pětkrát vyšší než nezpůsobující netřídění jiným způsobem.

Jedním z jednoduchých způsobů, jak to provést při výuce modelu v experimentu, je duplikování (pětkrát) záznamů, které reprezentují někoho s vysokým úvěrovým rizikem. 

V případě, že model chybně klasifikuje osobu jako nízké úvěrové riziko, když jsou ve skutečnosti vysoké riziko, model to znamená, že stejná nechybná klasifikace je pětkrát, jednou pro každou duplicitu. Tím se zvýší náklady na tuto chybu ve výsledcích školení.


### <a name="convert-the-dataset-format"></a>Převod formátu datové sady

Původní datová sada používá formát oddělený prázdným znakem. Machine Learning Studio (Classic) funguje lépe pomocí souboru s hodnotami oddělenými čárkami (CSV), takže tuto datovou sadu převedete tak, že nahradíte mezery čárkami.  

Existuje mnoho způsobů, jak tato data převést. Jedním ze způsobů je použít následující příkaz Windows PowerShellu:   

```powershell
cat german.data | %{$_ -replace " ",","} | sc german.csv  
```

Dalším způsobem je použití příkazu UNIX sed:  

```console
sed 's/ /,/g' german.data > german.csv
```

V obou případech jste vytvořili data oddělená čárkami v souboru s názvem **german.csv** , který můžete použít v experimentu.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Nahrajte datovou sadu do Machine Learning Studio (Classic).

Jakmile budou data převedena do formátu CSV, je nutné ji odeslat do Machine Learning Studio (Classic). 

1. Otevřete domovskou stránku Machine Learning Studio (Classic) ( [https://studio.azureml.net](https://studio.azureml.net) ). 

2. ![ ](./media/tutorial-part1-credit-risk/menu.png) V levém horním rohu okna klikněte na nabídku nabídky, klikněte na **Azure Machine Learning** , vyberte **Studio** a přihlaste se.

3. V dolní části okna klikněte na **+ Nový** .

4. Vyberte **datovou sadu**.

5. Vyberte **z místního souboru**.

    ![Přidat datovou sadu z místního souboru](./media/tutorial-part1-credit-risk/add-dataset.png)

6. V dialogovém okně **nahrát novou datovou sadu** klikněte na tlačítko Procházet a vyhledejte soubor **german.csv** , který jste vytvořili.

7. Zadejte název datové sady. Pro tento kurz zavolejte IT data o kreditních kartách "UCI němčina".

8. Jako typ dat vyberte **obecný soubor CSV bez záhlaví (.nh.csv)**.

9. Pokud chcete, přidejte popis.

10. Klikněte na značku zaškrtnutí **OK** .  

    ![Nahrát datovou sadu](./media/tutorial-part1-credit-risk/upload-dataset.png)

Tím se data nahrávají do modulu DataSet, který můžete použít v experimentu.

Datové sady, které jste nahráli do studia (Classic), můžete spravovat kliknutím na kartu **datové sady** vlevo v okně Studio (Classic).

![Správa datových sad](./media/tutorial-part1-credit-risk/dataset-list.png)

Další informace o importování dalších typů dat do experimentu najdete v tématu [Import školicích dat do Azure Machine Learning Studio (Classic)](import-data.md).

## <a name="create-an-experiment"></a>Vytvoření experimentu

Dalším krokem v tomto kurzu je vytvořit experiment v Machine Learning Studio (Classic), který používá datovou sadu, kterou jste nahráli.  

1. V nástroji Studio (Classic) klikněte v dolní části okna na **+ Nový** .
1. Vyberte **experiment** a potom vyberte "prázdný experiment". 

    ![Vytvoření nového experimentu](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. V horní části plátna vyberte výchozí název experimentu a přejmenujte ho na něco smysluplného.

    ![Přejmenovat experiment](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Je dobrým zvykem vyplnit **Souhrn** a **Popis** experimentu v podokně **vlastnosti** . Tyto vlastnosti poskytují možnost zdokumentovat experiment, aby každý, kdo ho uvidí později, porozuměl vašim cílům a metodologii.
   > 
   > ![Vlastnosti experimentu](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. V paletě modulu nalevo od plátna experimentu rozbalte **uložené datové sady**.
1. Najděte datovou sadu, kterou jste vytvořili v části **Moje datové sady** , a přetáhněte ji na plátno. Datovou sadu můžete také vyhledat zadáním názvu do **vyhledávacího** pole nad paletou.  

    ![Přidat datovou sadu do experimentu](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Příprava dat

Můžete zobrazit prvních 100 řádků dat a některé statistické informace pro celou datovou sadu: klikněte na výstupní port datové sady (malý kroužek v dolní části) a vyberte **vizualizovat**.  

Vzhledem k tomu, že datový soubor nepřišel se záhlavími sloupců, Studio (Classic) poskytuje obecné nadpisy (Sloupec1, col2 *atd.* ). Dobré nadpisy nejsou nezbytné pro vytváření modelu, ale usnadňují práci s daty v experimentu. I když nakonec tento model publikujete ve webové službě, budou hlavičky identifikovat sloupce pro uživatele služby.  

Záhlaví sloupců můžete přidat pomocí modulu [Upravit metadata][edit-metadata] .

Pomocí modulu [Upravit metadata][edit-metadata] můžete změnit metadata přidružená k datové sadě. V takovém případě ji použijete k poskytnutí výstižnější názvy pro záhlaví sloupců. 

Chcete-li použít [úpravu metadat][edit-metadata], určete nejprve, které sloupce chcete upravit (v tomto případě všechny). Dále zadáte akci, která má být provedena na těchto sloupcích (v tomto případě Změna záhlaví sloupců).

1. V paletě modulu zadejte do **vyhledávacího** pole "metadata". V seznamu modulů se zobrazí [metadata pro úpravy][edit-metadata] .

1. Klikněte na plátno a přetáhněte na něj modul [Upravit metadata][edit-metadata] a přetáhněte ho pod datovou sadu, kterou jste přidali dříve.

1. Připojte datovou sadu k [metadatům pro úpravy][edit-metadata]: klikněte na výstupní port datové sady (malý kroužek v dolní části datové sady), přetáhněte na vstupní port [Upravit metadata][edit-metadata] (malý kroužek v horní části modulu) a pak uvolněte tlačítko myši. Datová sada a modul zůstávají propojené i v případě, že se přesunete podél plátna.
 
    Experiment by teď měl vypadat nějak takto:  

    ![Přidávání metadat pro úpravy](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Červený vykřičník označuje, že ještě nemáte nastavené vlastnosti pro tento modul. Uděláte to za chvíli.

    > [!TIP]
    > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V takovém případě poklikejte na modul [Upravit metadata][edit-metadata] a zadejte komentář "Přidání záhlaví sloupců". Textové pole zavřete kliknutím kamkoli jinde na plátně. Chcete-li zobrazit komentář, klikněte na šipku dolů v modulu.
    > 
    > ![Upravit modul metadat s přidaným komentářem](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Vyberte [Upravit metadata][edit-metadata]a v podokně **vlastnosti** napravo od plátna klikněte na **Spustit selektor sloupců**.

1. V dialogovém okně **Vybrat sloupce** vyberte všechny řádky v **dostupných sloupcích** a kliknutím na > je přesuňte do **vybraných sloupců**.
   Dialogové okno by mělo vypadat takto:

   ![Selektor sloupců se zvolenými vybranými sloupci](./media/tutorial-part1-credit-risk/select-columns.png)


1. Klikněte na značku zaškrtnutí **OK** .

1. Zpátky v podokně **vlastnosti** vyhledejte **Nový parametr názvů sloupců** . Do tohoto pole zadejte seznam názvů pro 21 sloupců v datové sadě oddělené čárkami a v pořadí sloupců. Názvy sloupců můžete získat z dokumentace datové sady na webu UCI nebo pro usnadnění můžete zkopírovat a vložit následující seznam:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Podokno Vlastnosti vypadá takto:

   ![Vlastnosti pro úpravu metadat](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Pokud chcete ověřit záhlaví sloupců, spusťte experiment (klikněte na tlačítko **Spustit** pod plátnem experimentu). Po dokončení běhu (pro [úpravy metadat][edit-metadata]se zobrazí zelený znak zaškrtnutí), klikněte na výstupní port modulu [úprav metadat][edit-metadata] a vyberte **vizualizovat**. Výstup libovolného modulu můžete zobrazit stejným způsobem, jak zobrazit průběh dat v experimentu.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Vytváření školicích a testovacích datových sad

K tomu, abyste mohli model vyškolit, budete potřebovat nějaká data a otestujete ho.
V dalším kroku experimentu budete mít datovou sadu rozdělenou do dvou samostatných datových sad: jednu pro školení našeho modelu a jednu pro testování.

K tomu použijte modul [rozdělit data][split] .  

1. Najděte modul [rozdělit data][split] , přetáhněte ho na plátno a připojte ho k modulu [Upravit metadata][edit-metadata] .

1. Ve výchozím nastavení je poměr rozdělení 0,5 a je nastaven parametr **náhodného rozdělení** . To znamená, že náhodná polovina dat je výstupem přes jeden port modulu [rozdělených dat][split] a polovina až po druhé. Tyto parametry můžete upravit, stejně jako parametr **náhodného osazení** , chcete-li změnit rozdělení mezi školicími a testovacími daty. V tomto příkladu je ponecháte tak, jak je.
   
   > [!TIP]
   > **Zlomky vlastností řádků v první výstupní datové sadě** určují, kolik dat je výstupem na *levém* výstupním portu. Pokud například nastavíte poměr na 0,7, pak 70% dat je výstupem přes levý port a 30% přes správný port.  
   > 
   > 

1. Dvakrát klikněte na modul [rozdělení dat][split] a zadejte komentář "data o školeních a testováních 50%". 

Můžete použít výstupy modulu [rozdělených dat][split] , ale můžete se rozhodnout použít levý výstup jako školicí data a správný výstup jako testovací data.  

Jak je uvedeno v [předchozím kroku](tutorial-part1-credit-risk.md#upload), náklady na chybnou klasifikaci vysokého úvěrového rizika v nízkém rozsahu jsou pětkrát vyšší než náklady na neklasifikaci nízkého úvěrového rizika jako vysoké. K tomuto účtu vygenerujete novou datovou sadu, která odráží tuto nákladovou funkci. V nové datové sadě se každý příklad s vysokým rizikem replikuje pětkrát, zatímco každý příklad s nízkým rizikem není replikovaný.   

Tuto replikaci můžete provést pomocí kódu R:  

1. Najděte a přetáhněte modul [spuštění skriptu jazyka R][execute-r-script] na plátno experimentu. 

1. Připojte levý výstupní port modulu [rozdělit data][split] k prvnímu vstupnímu portu ("DataSet1.") modulu [spuštění skriptu jazyka R][execute-r-script] .

1. Dvakrát klikněte na modul [spuštění skriptu jazyka R][execute-r-script] a zadejte komentář. "nastavit adjustaci nákladů".

1. V podokně **vlastnosti** odstraňte výchozí text v parametru **skriptu jazyka R** a zadejte tento skript:
   
    ```r
    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")
    ```

    ![Skript r v modulu spouštění skriptu jazyka R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Tato operace replikace je potřeba provést pro každý výstup modulu [rozdělení dat][split] , aby data školení a testování měla stejnou adjustaci nákladů. Nejsnadnější způsob, jak to provést, je duplikace modulu [skriptu Execute-R][execute-r-script] , který jste právě provedli, a jeho připojením k druhému výstupnímu portu modulu [Split data][split] .

1. Klikněte pravým tlačítkem na modul [spuštění skriptu jazyka R][execute-r-script] a vyberte možnost **Kopírovat**.

1. Klikněte pravým tlačítkem na plátno experimentu a vyberte **Vložit**.

1. Přetáhněte nový modul na pozici a pak připojte pravý výstupní port modulu [Split data][split] k prvnímu vstupnímu portu tohoto nového modulu [skriptu pro spuštění R][execute-r-script] . 

1. V dolní části plátna klikněte na **Spustit**. 

> [!TIP]
> Kopie modulu spuštění skriptu jazyka R obsahuje stejný skript jako původní modul. Když zkopírujete a vložíte modul na plátno, kopie zachová všechny vlastnosti originálu.  
> 
>

Náš experiment teď vypadá nějak takto:

![Přidání rozděleného modulu a skriptů R](./media/tutorial-part1-credit-risk/experiment.png)

Další informace o použití skriptů R v experimentech najdete v tématu o [prodloužení experimentu pomocí jazyka r](index.yml).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvořit pracovní prostor Machine Learning Studio (Classic)
> * Nahrání stávajících dat do pracovního prostoru
> * Vytvoření experimentu

Nyní jste připraveni vyškolit a vyhodnocovat modely pro tato data.

> [!div class="nextstepaction"]
> [Kurz 2 – výuka modelů a jejich vyhodnocování](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data