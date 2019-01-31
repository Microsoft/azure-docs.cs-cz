---
Název: Příprava nasazení titleSuffix modelu: Azure Machine Learning Studio Popis: Jak připravit trénovaného modelu pro nasazení jako webové služby převedením vašeho výukového experimentu Machine Learning studia na prediktivní experiment.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.date: 03/28/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Příprava vašeho modelu pro nasazení v Azure Machine Learning Studio

Azure Machine Learning Studio poskytuje nástroje, které potřebujete k vývoji modelu prediktivní analýzy a zprovoznit ho a nasadit ho jako webová služba Azure.

K tomuto účelu použijete k vytvoření experimentu - volá Studio *výukového experimentu* – kde trénování, stanovení skóre a upravit model. Jakmile budete spokojeni, budete připravovat modelu pro nasazení na základě vašeho výukového experimentu k převedení *prediktivní experiment* , který je konfigurován ke skóre uživatelská data.

Uvedený příklad tohoto procesu v [názorný postup: Vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Tento článek používá podrobné informace o způsobu získá převést školicího experimentu na prediktivní experiment a jak se tento prediktivní experiment nasadit. Pochopením tyto podrobnosti se dozvíte, jak nakonfigurovat nasazeného modelu k němu efektivnější.



## <a name="overview"></a>Přehled 

Proces převedení školicího experimentu na prediktivní experiment zahrnuje tři kroky:

1. Nahraďte strojového učení algoritmu moduly trénovaného modelu.
2. Trim experiment pouze moduly, které jsou potřeba pro vyhodnocení. Výukový experiment obsahuje několik modulů, které jsou nezbytné pro školení, ale nejsou potřebné, jakmile model se trénuje.
3. Definujte, jak váš model přijímá data od uživatele webové služby a jaká data bude vrácen.

> [!TIP]
> Výukový experiment jste obeznámeni s trénování a vyhodnocování modelu pomocí svoje vlastní data. Ale po nasazení uživatele pošle nová data do modelu a vrátí výsledky předpovědí. Tak jak převést výukového experimentu na prediktivní experiment připravit na nasazení, mějte na paměti použití modelu jinými uživateli.
> 
> 

## <a name="set-up-web-service-button"></a>Tlačítko Nastavení webové služby
Po spuštění experimentu (klikněte na tlačítko **spustit** v dolní části na plátno experimentu), klikněte na tlačítko **nastavení webové služby** tlačítko (vyberte **prediktivní webová služba** možnost). **Nastavení webové služby** za vás provádí převod výukového experimentu na prediktivní experiment tři kroky:

1. Uloží trénovaného modelu ve službě **Trénované modely** části palety modulů (nalevo od plátna experimentu). Poté nahradí algoritmu strojového učení a [Train Model] [ train-model] moduly s uložené trénovaného modelu.
2. Analyzuje experimentu a odebere modulů, které byly použity jasně pouze pro školení a už nejsou potřeba.
3. Vloží _webové služby vstup_ a _výstup_ moduly do umístění výchozího v experimentu (tyto moduly přijímají a vrací data uživatele).

Například následující experiment trénovat dvěma třídami Posílený rozhodovací strom model s použitím ukázkových dat sčítání:

![Výukového experimentu][figure1]

Moduly v tento experiment provádět v podstatě čtyři různé funkce:

![Modul funkce][figure2]

Pokud převedete tento výukový experiment prediktivní experiment, některé z těchto modulů již nejsou potřebné nebo teď slouží k jinému účelu:

* **Data** – data v této ukázkové datové sadě se nepoužijí během vyhodnocování – zadání dat zohlednit uživatele webové službě. Metadata z této datové sady, jako jsou datové typy, ale používá trénovaného modelu. Proto je potřeba nechat datovou sadu v prediktivní experiment tak, aby mohl poskytnout tato metadata.

* **Příprava** – v závislosti na tom uživatelská data, která bude odeslána pro vyhodnocení, tyto moduly může nebo nemusí být potřeba zpracovávat příchozí data. **Nastavení webové služby** tlačítko nemá touch tyto – je potřeba rozhodnutí o způsobu jejich zpracování.
  
    Například v tomto příkladu ukázkovou datovou sadou může mít chybějící hodnoty, takže [vyčištění chybějících dat] [ clean-missing-data] modul byl zahrnut vypořádat s nimi. Ukázkovou datovou sadou navíc obsahuje sloupce, které nejsou potřebné pro trénování modelu. Proto [výběr sloupců v datové sadě] [ select-columns] modulu byla zahrnuta pro vyloučení tyto dodatečné sloupce z datového toku. Pokud víte, že data, která bude odeslána pro vyhodnocování prostřednictvím webové služby nebude obsahovat chybějící hodnoty a pak můžete odebrat [vyčištění chybějících dat] [ clean-missing-data] modulu. Nicméně od verze [výběr sloupců v datové sadě] [ select-columns] modulu pomáhá definovat sloupce dat, která očekává trénovaného modelu, tento modul musí zůstat.

* **Trénování** – tyto moduly se používají pro trénování modelu. Po kliknutí na **nastavení webové služby**, tyto moduly jsou nahrazeny jednoho modulu, který obsahuje model je školení. Tento nový modul se uloží do **Trénované modely** části palety modulů.

* **Skóre** – v tomto příkladu [rozdělení dat] [ split] modul se používá k rozdělení datový proud na testovací data a trénovací data. V prediktivní experiment jsme nejsou školení už nepotřebujeme, takže [rozdělení dat] [ split] je možné odebrat. Podobně, druhý [Score Model] [ score-model] modulu a [Evaluate Model] [ evaluate-model] modul se používá k porovnání výsledků z testovacích dat, takže Tyto moduly nejsou potřeba ve prediktivní experiment. Zbývající [Score Model] [ score-model] modulu, ale je potřeba provést vrácení výsledku skóre prostřednictvím webové služby.

Zde je, jak náš příklad vypadá po kliknutí na tlačítko **nastavení webové služby**:

![Převést prediktivní experiment][figure3]

Práci prováděnou **nastavení webové služby** může být dostatečné k přípravě experiment nasadit jako webovou službu. Však můžete chtít provést další úkony konkrétní do experimentu.

### <a name="adjust-input-and-output-modules"></a>Upravit vstupní a výstupní moduly
V výukového experimentu používá sadu trénovacích dat a pak jste nějaké zpracování pro získání dat ve formě potřeby algoritmu strojového učení. Když data, které by se měl zobrazit prostřednictvím webové služby, nebude nutné toto zpracování, můžete to obejít: propojte výstup modulu **vstupní modul webové služby** do jiného modulu v experimentu. Data uživatele se teď dorazí v modelu v tomto umístění.

Například ve výchozím nastavení **nastavení webové služby** umístí **webové služby vstup** modulu v horní části váš tok dat, jak je znázorněno na obrázku výše. Ale můžete ručně umístit **webové služby vstup** za zpracování dat modulů:

![Přesunutí vstup webové služby][figure4]

Vstupní data poskytovaná prostřednictvím webové služby předá nyní přímo do modulu určení skóre modelu bez jakékoli předběžného zpracování.

Podobně, ve výchozím nastavení **nastavení webové služby** umístí modul výstup webové služby v dolní části datový tok. V tomto příkladu vrátí webová služba uživatel výstup [Score Model] [ score-model] modul, který zahrnuje vektoru kompletní vstupní data a bodovací výsledky.
Ale pokud chcete vrátit něco jiné, pak můžete přidat další moduly před **webové služby výstup** modulu. 

Například vrátí pouze výsledky vyhodnocení a ne celý vektor vstupních dat, přidejte [výběr sloupců v datové sadě] [ select-columns] modulu, který chcete vyloučit všechny sloupce kromě bodování výsledky. Přesuňte **webové služby výstup** modulu k výstupu [výběr sloupců v datové sadě] [ select-columns] modulu. Experiment vypadá takto:

![Přesunutí výstup webové služby][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Přidat nebo odebrat moduly další zpracování dat
Pokud existují další moduly do experimentu, o kterém víte, že nebude potřeba během vyhodnocování, ty lze odebrat. Například protože jsme přešli **webové služby vstup** modulu do bodu po moduly zpracování dat, můžeme odebrat [vyčištění chybějících dat] [ clean-missing-data] modulu z Prediktivní experiment.

Naše prediktivní experiment teď vypadá takto:

![Odebrání dalších modulů][figure6]


### <a name="add-optional-web-service-parameters"></a>Přidání volitelných parametrů webové služby
V některých případech můžete chtít umožnit uživateli webovou službu můžete změnit chování modulů při přístupu k službě. *Webová služba parametry* umožňují udělat.

Běžným příkladem je nastavení [Import dat] [ import-data] modul proto uživatel nasazenou webovou službu můžete určit jiný zdroj dat při přístupu k webové službě. Nebo konfigurace [exportovat Data] [ export-data] modulu tak, aby se dá nastavit jiný cíl.

Můžete definovat parametry webové služby a přidružit jeden nebo více parametrů modulu, a můžete určit, jestli jsou povinné nebo volitelné. Když služba přistupuje, a modul akce, které jsou odpovídajícím způsobem změněny uživatelské web service poskytuje hodnoty pro tyto parametry.

Další informace o co jsou parametry webové služby a způsob jejich použití naleznete v tématu [použití parametrů webové služby Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Prediktivní experiment nasadit jako webovou službu
Teď, když prediktivní experiment dostatečně připravený, můžete ho nasadit jako webová služba Azure. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Další informace o procesu kompletní nasazení najdete v tématu [nasazení webové služby Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
