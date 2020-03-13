---
title: 'Kurz 3: nasazení modelu úvěrového rizika'
titleSuffix: Azure Machine Learning Studio (classic)
description: Podrobný kurz, který ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning Studio (Classic). Tento kurz je třetí částí série kurzů s třemi částmi. Ukazuje, jak nasadit model jako webovou službu.
keywords: úvěrové riziko, řešení prediktivní analýzy, posouzení rizik, nasazení, Webová služba
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204149"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Kurz 3: nasazení modelu úvěrového rizika – Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

V tomto kurzu se podíváme na proces vývoje řešení prediktivní analýzy. V Machine Learning Studio (Classic) vyvíjíte jednoduchý model.  Model pak nasadíte jako Azure Machine Learning webovou službu.  Tento nasazený model může vytvářet předpovědi s využitím nových dat. Tento kurz je **třetí částí série kurzů s třemi částmi**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je složitý problém, ale v tomto kurzu se tento kurz zjednodušuje. Použijete ho jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí Microsoft Azure Machine Learning Studio (Classic). Pro toto řešení použijete Azure Machine Learning Studio (Classic) a Machine Learning webovou službu. 

V tomto kurzu se třemi částmi začínáte veřejně dostupnými daty o úvěrovém riziku.  Pak vyvíjíte a naučíte prediktivní model.  Nakonec model nasadíte jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md)jste vytvořili pracovní prostor Machine Learning Studio (klasický), nahráli jste data a vytvořili experiment.

Ve [druhé části kurzu](tutorial-part2-credit-risk-train.md)jste prohodnotili a vyhodnotili modely.

V této části kurzu:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Test webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

## <a name="prerequisites"></a>Požadavky

Vyplňte [druhou část kurzu](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení
Pokud chcete ostatním umožnit používání prediktivního modelu, který jste vytvořili v tomto kurzu, můžete ho nasadit jako webovou službu v Azure.

Až do tohoto okamžiku jste se seznámili s školením našeho modelu. Ale v nasazované službě se už to proveďte školení – chce generovat nových předpovědí podle hodnocení vstupu uživatele na základě našich modelu. Provedeme si ale nějaké přípravy na převod tohoto experimentu z ***školicího*** experimentu na ***prediktivní*** experiment. 

Příprava na nasazení je proces tří kroků:  

1. Odeberte jeden z modelů
1. Převod *experimentu školení* , který jste vytvořili, do *prediktivního experimentu*
1. Prediktivní experiment nasadit jako webovou službu

### <a name="remove-one-of-the-models"></a>Odeberte jeden z modelů

Nejdřív je potřeba zkrátit tento experiment trochu. v současné době máte dva různé modely v experimentu, ale chcete použít jenom jeden model, když ho nasadíte jako webovou službu.  

Řekněme, že jste se rozhodli, že se zesílený stromový model vypnul lépe než model SVM. První věc, kterou je potřeba udělat, je odebrat modul [vektorového počítače podpory dvou tříd][two-class-support-vector-machine] a moduly, které se použily k jeho školení. Kopii experimentu můžete vytvořit nejdřív tak, že kliknete na **Uložit jako** v dolní části plátna experimentu.

je nutné odstranit následující moduly:  

* [Vektorový počítač podpory dvou tříd][two-class-support-vector-machine]
* [Model výuky][train-model] a modely [skóre][score-model] , které se k němu připojily
* [Normalizovat data][normalize-data] (obě z nich)
* [Vyhodnotit model][evaluate-model] (protože dokončili jste vyhodnocování modelů)

Vyberte jednotlivé moduly a stiskněte klávesu DELETE nebo klikněte pravým tlačítkem na modul a vyberte **Odstranit**. 

![Zvýrazní moduly, které se mají odstranit, aby se odstranil model podpory Vector.](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Náš model by teď měl vypadat přibližně takto:

![Výsledný experiment při odstranění modelu podpory vektorového počítače](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nyní jsme připraveni nasadit tento model pomocí [rozřízeného rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment

Chcete-li získat tento model připravený pro nasazení, je třeba převést tento experiment školení na prediktivní experiment. To zahrnuje tři kroky:

1. Uložte si model, který jste pronaučili, a potom nahraďte naše školicí moduly.
1. Trim experiment odebrat modulů, které byly potřeba jenom pro školení
1. Definujte, ve kterém webová služba bude přijímat vstup a kde se vygeneruje výstup

Můžete to provést ručně, ale naštěstí všechny tři kroky můžete provést kliknutím na **nastavit webovou službu** ve spodní části plátna experimentu (a výběrem možnosti **prediktivní webová služba** ).

> [!TIP]
> Pokud potřebujete další podrobnosti o tom, co se stane, když převedete školicí experiment na prediktivní experiment, přečtěte si téma [Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](convert-training-experiment-to-scoring-experiment.md).

Když kliknete na **nastavit webovou službu**, stane se několik věcí:

* Vycvičený model se převede na jeden modul **trained model** a uloží se do palety modulu nalevo od plátna experimentu (můžete ho najít v části **trained Models**).
* Moduly, které byly použity pro vzdělávání se odeberou; konkrétně:
  * [Zvýšení rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree]
  * [Model výuky][train-model]
  * [Rozdělit data][split]
  * druhý modul [spuštění skriptu jazyka R][execute-r-script] , který se použil pro testovací data
* Uložené trénovaného modelu je přidána zpět do experimentu
* Jsou přidány výstupní moduly **webové služby** a **výstupní webové služby** (které určují, kde budou data uživatele zadávat model a jaká data se vrátí, když je webová služba dostupná).

> [!NOTE]
> Uvidíte, že experiment se uloží do dvou částí v rámci karty, které byly přidány v horní části na plátno experimentu. Původní školicí experiment je pod **experimentem školení**na kartě a nově vytvořený prediktivní experiment je v rámci **prediktivního experimentu**. Prediktivní experiment je ten, který nasadíte jako webovou službu.

v rámci tohoto konkrétního experimentu je třeba provést jeden další krok.
Přidali jste dva moduly [spuštění skriptu R][execute-r-script] , které poskytují funkci váhy dat. To bylo pouze štych, které jste potřebovali pro školení a testování, takže tyto moduly můžete vzít v konečném modelu.
Machine Learning Studio (Classic) odebraly při odebrání [rozděleného][split] modulu jeden modul pro [spuštění skriptu R][execute-r-script] . Nyní můžete odebrat [Editor metadat][metadata-editor] druhý a připojit přímo ke [bodovému modelu][score-model].    

Naše experiment měl vypadat takto:  

![Vyhodnocování trénovaného modelu](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Možná vás zajímá, proč jste v prediktivním experimentu opustili datovou sadu dat platební karty v sadě UCI němčina. Služba se to skóre data uživatele, nikoli původní datové sady, tak proč nechte původní datové sady v modelu?
> 
> To platí, že službu nemusí původní údajů o kreditních kartách. Ale ji potřebuje pro tato data, která obsahuje informace, jako je počet sloupců nejsou a sloupce, které jsou číselné schéma. Tyto informace schématu je nutné interpretovat data uživatele. Tyto součásti se nechají připojené, takže modul bodování má při spuštění služby schéma datové sady. Data se nepoužívá, pouze schéma.  
> 
>Jeden důležité si uvědomit je, že pokud původní datové sady obsahuje popisek, pak očekávané schéma ze vstupu web bude také předpokládají, že sloupec s popiskem! Odebrat popisek a jiná data, která byla v trénovací datové sady, ale nebude ve vstupech web před připojením vstup webové a trénovací datové sady do modulu common je možné vyřešit. 
> 

Spusťte experiment jednou, a to tak, že kliknete na **Spustit**. Pokud chcete ověřit, že model stále pracuje, klikněte na výstup modulu určení [skóre modelu][score-model] a vyberte **Zobrazit výsledky**. Uvidíte, že se zobrazí původní data, společně s riziko hodnota kreditů ("popisky vyhodnocení") a bodovací hodnota pravděpodobnosti ("skóre pravděpodobnosti".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit experiment jako buď webové služby Classic, nebo jako novou webovou službu, která je založená na Azure Resource Manageru.

### <a name="deploy-as-a-classic-web-service"></a>Nasadit jako webovou službu Classic
Pokud chcete nasadit klasickou webovou službu odvozenou z našeho experimentu, klikněte na **nasadit webovou službu** pod plátno a vyberte **nasadit webovou službu [Classic]** . Machine Learning Studio (Classic) nasadí experiment jako webovou službu a přejde na řídicí panel pro tuto webovou službu. Na této stránce se můžete vrátit do experimentu (**Zobrazit snímek** nebo **Zobrazit nejnovější**) a spustit jednoduchý test webové služby (viz **testování webové služby** níže). Zde najdete také informace pro vytváření aplikací, které mají přístup k webové službě (Další informace najdete v dalším kroku tohoto kurzu).

![Řídicí panel webové služby](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Službu můžete nakonfigurovat kliknutím na kartu **Konfigurace** . Tady můžete upravit název služby (ve výchozím nastavení se mu přidělí název experimentu) a pojmenujte ho. Můžete také udělit další popisný popisky pro vstupní a výstupní data.  

![Konfigurovat webovou službu](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Nasadit jako novou webovou službu

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do kterého nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

K nasazení nové webové služby získané z našich experiment:

1. Klikněte na **nasadit webovou službu** pod plátno a vyberte **nasadit webovou službu [New]** . Machine Learning Studio (Classic) vás přenáší na stránku **experimentu nasazení** webové služby Azure Machine Learning Web Services.

1. Zadejte název pro webovou službu. 

1. V případě **cenového plánu**můžete vybrat existující Cenový tarif nebo vybrat vytvořit nové a zadat název nového plánu a vybrat možnost měsíčního plánu. Výchozí plán úrovně plány vaší výchozí oblasti a webová služba je nasazený na danou oblast.

1. Klikněte na **nasadit**.

Po několika minutách se otevře stránka **rychlý Start** pro vaši webovou službu.

Službu můžete nakonfigurovat kliknutím na kartu **Konfigurovat** . Tady můžete upravit název služby a zadat pro něj popis. 

Chcete-li otestovat webovou službu, klikněte na kartu **test** (viz **Test webové služby** níže). Informace o vytváření aplikací, které mají přístup k webové službě, získáte tak, že kliknete na kartu **spotřebování** (další krok v tomto kurzu se zobrazí podrobněji).

> [!TIP]
> Po jeho nasazení, můžete aktualizovat webovou službu. Například pokud chcete změnit svůj model, můžete upravit experiment pro školení, upravit parametry modelu a kliknout na **nasadit webovou službu**, vybrat **nasadit webovou službu [Classic]** nebo **nasadit webovou službu [New]** . Při nasazení experimentu se nahradí webovou službu, teď pomocí aktualizovaného modelu.  
> 
> 

## <a name="test-the-web-service"></a>Test webové služby

Když je k webové službě přistupovaná, data uživatele se doplní přes **Vstupní modul webové služby** , ve kterém se předají do modulu [skóre modelu][score-model] a skóre. Způsob, jakým jste nastavili prediktivní experiment, model očekává data ve stejném formátu jako v původní datové sadě úvěrového rizika.
Výsledky se vrátí uživateli z webové služby přes **výstupní modul webové služby** .

> [!TIP]
> Jak máte nakonfigurované prediktivní experiment, vrátí se celý výsledek z modulu [skóre modelu][score-model] . To zahrnuje veškerá vstupní data a rizika hodnota kreditů a bodovací pravděpodobnosti. Ale něco jiného může vrátit, pokud chcete, aby – například může vrátit pouze hodnota kreditů rizika. Provedete to tak, že vložíte modul [Vybrat sloupce][select-columns] mezi [modelem skóre][score-model] a **výstupem webové služby** , aby se vyloučily sloupce, které nechcete, aby webová služba vracela. 
> 
> 

Klasickou webovou službu můžete testovat buď v **Machine Learning Studio (Classic)** , nebo na portálu **Azure Machine Learning Web Services** .
Novou webovou službu můžete testovat jenom na portálu **Machine Learning Web Services** .

> [!TIP]
> Při testování na portálu Azure Machine Learning Web Services, můžete mít na portálu vytvořit ukázková data, která slouží k otestování služby typu žádost-odpověď. Na stránce **Konfigurace** vyberte možnost Ano, pokud chcete **ukázková data povolit?** . Když na stránce **test** otevřete kartu požadavek-odpověď, vyplní portál ukázková data přijatá z původní datové sady kreditového rizika.

### <a name="test-a-classic-web-service"></a>Test webové služby Classic

Klasickou webovou službu můžete testovat v Machine Learning Studio (Classic) nebo na portálu Machine Learning Web Services. 

#### <a name="test-in-machine-learning-studio-classic"></a>Test v Machine Learning Studio (klasický)

1. Na stránce **řídicího panelu** webové služby klikněte na tlačítko **test** pod **výchozím koncovým bodem**. Dialogové okno otevře a vyzve vás vstupních dat pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.  

1. Zadejte sadu dat a pak klikněte na **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Test na portálu webových služeb Machine Learning

1. Na stránce **řídicího panelu** webové služby klikněte na odkaz **test Preview** pod **výchozím koncovým bodem**. Zkušební stránku na portálu Azure Machine Learning Web Services pro koncový bod webové služby se otevře a zobrazí výzvu k vstupní data pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.

2. Klikněte na **testovat požadavek-odpověď**. 

### <a name="test-a-new-web-service"></a>Otestovat novou webovou službu

Nové webové služby můžete otestovat pouze na portálu webových služeb Machine Learning.

1. Na portálu [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) klikněte v horní části stránky na **test** . Otevře se stránka **test** a můžete zadat data pro službu. Vstupní pole zobrazí odpovídají sloupcům, které se zobrazovaly v původní datové sady úvěrové riziko. 

1. Zadejte sadu dat a pak klikněte na **testovat požadavek-odpověď**.

Výsledky testu se zobrazí na pravé straně stránky ve výstupním sloupci. 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby, ať už jde o klasickou nebo novou, ji můžete spravovat z portálu [Microsoft Azure Machine Learning webové služby](https://services.azureml.net/quickstart) .

Ke sledování výkonu webové služby:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)
1. Klikněte na **webové služby** .
1. Klikněte na webovou službu
1. Klikněte na **řídicí panel** .

## <a name="access-the-web-service"></a>Nastavení přístupu k webové službě

V předchozím kroku tohoto kurzu jste nasadili webovou službu, která využívá model předpovědi úvěrového rizika. Uživatelé teď budou moct odesílat data a zobrazí výsledky. 

Webová služba je webová služba Azure, který dokáže přijmout a vrátit data pomocí rozhraní REST API v jednom ze dvou způsobů:  

* **Požadavek nebo odpověď** – uživatel odešle do služby jeden nebo více řádků kreditních dat pomocí protokolu HTTP a služba odpoví jednou nebo více sadami výsledků.
* **Dávkové spuštění** – uživatel ukládá jeden nebo více řádků kreditních dat do objektu blob Azure a pak odešle umístění objektu blob do služby. Služba získává všechny řádky dat do vstupního objektu blob, jiný objekt blob uloží výsledky a vrátí adresu URL tohoto kontejneru.  

Další informace o přístupu a používání webové služby najdete v tématu věnovaném [využívání Azure Machine Learning webové služby pomocí šablony webové aplikace](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Test webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

Můžete také vyvíjet vlastní aplikace pro přístup k webové službě pomocí počáteční kód pro vás k dispozici v R, C#, a programovací jazyky Python.

> [!div class="nextstepaction"]
> [Využívání webové služby Azure Machine Learning](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
