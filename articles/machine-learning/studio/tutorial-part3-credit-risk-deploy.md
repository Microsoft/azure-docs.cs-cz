---
title: 'Kurz 3: Úvěrové riziko model nasazení'
titleSuffix: Azure Machine Learning Studio
description: Podrobný kurz ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v nástroji Azure Machine Learning Studio. Tento kurz je třetí částí z třídílné série. Ukazuje, jak nasazení modelu jako webové služby.
keywords: úvěrové riziko, řešení prediktivní analýzy, posouzení rizik, nasazení, webová služba
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6e3f0613e89ef7d6e9b44e519226a793940d41f6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007870"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>Kurz 3: Nasadit model rizika kredit – Azure Machine Learning Studio

V tomto kurzu provedete rozšířené pohled na proces vývoje řešení prediktivní analýzy. Při vývoji jednoduchý model v nástroji Machine Learning Studio.  Potom tento model nasadíme jako webové služby Azure Machine Learning.  Tento model nasazený můžete vytvářet predikce na nová data. Tento kurz je **třetí částí z třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je komplexní problém, ale v tomto kurzu se tak si ho Zjednodušte trochu. Budete ho používat jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí služby Microsoft Azure Machine Learning. Budete používat Azure Machine Learning Studio a webovou službou Machine Learning pro toto řešení. 

V tomto kurzu třemi částmi začnete s veřejně dostupnými daty úvěrového rizika.  Vyvíjejte a natrénujeme prediktivní model.  Nakonec tento model nasadíme jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md), vytvořili pracovní prostor Machine Learning Studio, nahrát data a vytvoření experimentu.

V [druhou částí z kurzu](tutorial-part2-credit-risk-train.md), trénují a vyhodnocují modely.

V této části kurzu jste:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Test webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Požadavky

Kompletní [druhou částí z kurzu](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení
Ostatním uživatelům příležitost dobře se pomocí prediktivního modelu, který jste vytvořili v tomto kurzu, můžete ho nasadit jako webovou službu v Azure.

Do této chvíle jste experimentovali s trénujete model pro naše. Ale v nasazované službě se už to proveďte školení – chce generovat nových předpovědí podle hodnocení vstupu uživatele na základě našich modelu. Tedy budeme provést určitou přípravu pro převod z tohoto experimentu ***školení*** experimentovat ***prediktivní*** experimentovat. 

Příprava pro nasazení je třech krocích:  

1. Odeberte jeden z modelů
1. Převést *výukového experimentu* jste vytvořili do *prediktivní experiment*
1. Prediktivní experiment nasadit jako webovou službu

### <a name="remove-one-of-the-models"></a>Odeberte jeden z modelů

Nejprve budete muset oříznout tento experiment trochu dolů. momentálně máte dva různé modely v experimentu, ale chcete použít jeden model, pokud jste ji nasadit jako webovou službu.  

Řekněme, že jste se rozhodli, že model posíleného stromu lepších výsledků než SVM modelu. Proto první věc, kterou chcete odebrat [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu a moduly, které byly použity pro trénování ho. Možná budete chtít vytvořit kopii experimentu nejprve kliknutím **uložit jako** v dolní části na plátno experimentu.

je třeba odstranit následující moduly:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* [Trénování modelu] [ train-model] a [Score Model] [ score-model] moduly, které byly k ní připojená.
* [Normalizuje Data][normalize-data] (obou z nich)
* [Vyhodnocení modelu][evaluate-model] (protože jsme hotovi, vyhodnocení modelů)

Vyberte každého modulu a stiskněte klávesu Delete nebo klikněte pravým tlačítkem na modul a vyberte **odstranit**. 

![Odebrat SVM modelu](./media/tutorial-part3-credit-risk-deploy/publish3a.png)


Náš model by teď měl vypadat přibližně takto:

![Odebrat SVM modelu](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nyní jsme připraveni k nasazení pomocí modelu [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment

Chcete-li získat tento model připravené na nasazení, budete muset převést tento výukový experiment prediktivní experiment. To zahrnuje tři kroky:

1. Uložit model natrénovali a potom nahraďte naše školicí moduly
1. Trim experiment odebrat modulů, které byly potřeba jenom pro školení
1. Definujte, ve kterém webová služba bude přijímat vstup a kde se vygeneruje výstup

Můžete to udělat ručně, ale naštěstí všechny tři kroky můžete provést kliknutím **nastavení webové služby** v dolní části na plátno experimentu (a vyberete **prediktivní webová služba** možnost).

> [!TIP]
> Pokud chcete podrobnosti na co se stane po převedení školicího experimentu na prediktivní experiment, přečtěte si téma [přípravu modelu pro nasazení v Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Po kliknutí na **nastavení webové služby**, stane několik věcí:

* Trénovaného modelu je převést na jediné **Trénovaného modelu** modulu a uložené paletě modulů nalevo od plátna experimentu (najdete ho pod **Trénované modely**)
* Moduly, které byly použity pro vzdělávání se odeberou; konkrétně:
  * [Two-Class posíleného rozhodovacího stromu][two-class-boosted-decision-tree]
  * [Trénování modelu][train-model]
  * [Rozdělení dat.][split]
  * Druhá [spustit skript jazyka R] [ execute-r-script] modul, který byl použit pro testovací data
* Uložené trénovaného modelu je přidána zpět do experimentu
* **Webová služba vstup** a **webové služby výstup** moduly jsou přidány (tyto identifikátory identifikují ve kterém se data uživatele zadejte modelu a jaká data se vrátí, když přistupuje k webové službě)

> [!NOTE]
> Uvidíte, že experiment se uloží do dvou částí v rámci karty, které byly přidány v horní části na plátno experimentu. Původní výukového experimentu je na kartě **výukového experimentu**, a nově vytvořený prediktivní experiment probíhá **prediktivní experiment**. Prediktivní experiment je ta, kterou budete nasazovat jako webové služby.

je potřeba provést další krok se tento konkrétní experiment.
jste přidali dvě [spustit skript jazyka R] [ execute-r-script] moduly, které poskytují funkce váhu k datům. Který byl právě zdvih potřebné pro trénování a testování, díky kterému můžete na tyto moduly v finálního modelu.
Machine Learning Studio odebrat jeden [spustit skript jazyka R] [ execute-r-script] modulu odebrán [rozdělení] [ split] modulu. Teď můžete odebrat druhé a připojit [Editor metadat] [ metadata-editor] přímo na [Score Model][score-model].    

Naše experiment měl vypadat takto:  

![Vyhodnocování trénovaného modelu](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Asi vás zajímá proč left údajů o kreditních kartách němčina UCI datovou sadu v prediktivní experiment. Služba se to skóre data uživatele, nikoli původní datové sady, tak proč nechte původní datové sady v modelu?
> 
> To platí, že službu nemusí původní údajů o kreditních kartách. Ale ji potřebuje pro tato data, která obsahuje informace, jako je počet sloupců nejsou a sloupce, které jsou číselné schéma. Tyto informace schématu je nutné interpretovat data uživatele. můžete ponechat tyto součásti připojení tak, aby modul pro stanovení skóre měla schéma datové sady, když je služba spuštěná. Data se nepoužívá, pouze schéma.  
> 
>Jeden důležité si uvědomit je, že pokud původní datové sady obsahuje popisek, pak očekávané schéma ze vstupu web bude také předpokládají, že sloupec s popiskem! Odebrat popisek a jiná data, která byla v trénovací datové sady, ale nebude ve vstupech web před připojením vstup webové a trénovací datové sady do modulu common je možné vyřešit. 
> 

Spusťte experiment jednou (klikněte na tlačítko **spustit**.) Pokud chcete ověřit, že je model stále funguje, kliknutím na výstup [Score Model] [ score-model] modul a vyberte **zobrazení výsledků**. Uvidíte, že se zobrazí původní data, společně s riziko hodnota kreditů ("popisky vyhodnocení") a bodovací hodnota pravděpodobnosti ("skóre pravděpodobnosti".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit experiment jako buď webové služby Classic, nebo jako novou webovou službu, která je založená na Azure Resource Manageru.

### <a name="deploy-as-a-classic-web-service"></a>Nasadit jako webovou službu Classic
Nasazení webové služby Classic odvozený z našich experiment, klikněte na tlačítko **nasadit webovou službu** dole na plátně a vyberte **nasazení webové služby [Classic]**. Machine Learning Studio nasadí experiment jako webové služby a přejdete na řídicí panel pro tuto webovou službu. Na této stránce můžete vrátit do experimentu (**zobrazení snímku** nebo **zobrazit nejnovější**) a spouštět jednoduchý test webové služby (naleznete v tématu **testovat webovou službu** níže). Je zde také informace pro vytváření aplikací, které můžou přistupovat k webové službě (více zabývat v dalším kroku v tomto kurzu).

![Řídicí panel webové služby](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Můžete nakonfigurovat službu kliknutím **konfigurace** kartu. Tady můžete upravit název služby (to má zadaný název experimentu ve výchozím nastavení) a zadejte pro něj popis. Můžete také udělit další popisný popisky pro vstupní a výstupní data.  

![Konfigurovat webovou službu](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Nasadit jako novou webovou službu

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do kterého nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

K nasazení nové webové služby získané z našich experiment:

1. Klikněte na tlačítko **nasadit webovou službu** dole na plátně a vyberte **nasazení [nové] webová služba**. Machine Learning Studio přenese je na webové služby Azure Machine Learning **nasadit Experiment** stránky.

1. Zadejte název pro webovou službu. 

1. Pro **cenový plán**, můžete vybrat stávajícím cenovým tarifem, nebo vyberte "Vytvořit novou" a pojmenujte nový plán a možnost měsíční plán. Výchozí plán úrovně plány vaší výchozí oblasti a webová služba je nasazený na danou oblast.

1. Klikněte na tlačítko **nasazení**.

Po několika minutách **rychlý Start** se otevře stránka pro webovou službu.

Můžete nakonfigurovat službu kliknutím **konfigurovat** kartu. Tady můžete upravit službu nadpisu a zadejte pro něj popis. 

K otestování webové služby, klikněte na tlačítko **testování** kartu (naleznete v tématu **testovat webovou službu** níže). Informace o vytváření aplikací, které můžou přistupovat k webové službě, kliknutím na odkaz **využívání** kartu (dál v tomto kurzu přejde do další podrobnosti).

> [!TIP]
> Po jeho nasazení, můžete aktualizovat webovou službu. Například pokud chcete změnit váš model, pak výukového experimentu můžete upravit, upravit parametry modelu a klikněte na **nasadit webovou službu**, kde vyberou **nasazení webové služby [Classic]** nebo **Nasazení webové služby [nové]**. Při nasazení experimentu se nahradí webovou službu, teď pomocí aktualizovaného modelu.  
> 
> 

## <a name="test-the-web-service"></a>Test webové služby

Při přístupu k webové službě, data uživatele zadá prostřednictvím **webové služby vstup** modul, kde je předán [určení skóre modelu] [ score-model] modulu a skóre. Způsob, jakým jste nastavili prediktivní experiment, modelu očekává, že data ve stejném formátu jako původní datové sady úvěrové riziko.
Výsledky se vrátí uživateli z webové služby prostřednictvím **webové služby výstup** modulu.

> [!TIP]
> Způsob, jak máte prediktivní experiment nakonfigurovaná, výsledkem celé [Score Model] [ score-model] modulu jsou vráceny. To zahrnuje veškerá vstupní data a rizika hodnota kreditů a bodovací pravděpodobnosti. Ale něco jiného může vrátit, pokud chcete, aby – například může vrátit pouze hodnota kreditů rizika. Chcete-li to provést, vložte [sloupce projektu] [ project-columns] modulu mezi [Score Model] [ score-model] a **webové služby výstup**Chcete-li odstranit sloupce nechcete, aby webové služby se vraťte. 
> 
> 

Můžete otestovat klasické webové služby v **Machine Learning Studio** nebo **Azure Machine Learning Web Services** portálu.
Můžete testovat nové webové služby pouze v **Machine Learning Web Services** portálu.

> [!TIP]
> Při testování na portálu Azure Machine Learning Web Services, můžete mít na portálu vytvořit ukázková data, která slouží k otestování služby typu žádost-odpověď. Na **konfigurovat** stránky, vyberte Ano pro" **povolené rozhraní ukázková Data?**. Když otevřete kartu odpověď na požadavek na **Test** stránce portálu vyplní ukázková data z původní datové sady úvěrové riziko.

### <a name="test-a-classic-web-service"></a>Test webové služby Classic

Webové služby Classic můžete otestovat v nástroji Machine Learning Studio nebo na portálu webových služeb Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testování v nástroji Machine Learning Studio

1. Na **řídicí panel** stránky pro webovou službu, klikněte na tlačítko **testovací** tlačítko **výchozí koncový bod**. Dialogové okno otevře a vyzve vás vstupních dat pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.  

1. Zadejte sadu dat a pak klikněte na tlačítko **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Test na portálu webových služeb Machine Learning

1. Na **řídicí panel** stránky pro webovou službu, klikněte na tlačítko **Test preview** odkaz pod **výchozí koncový bod**. Zkušební stránku na portálu Azure Machine Learning Web Services pro koncový bod webové služby se otevře a zobrazí výzvu k vstupní data pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.

2. Klikněte na tlačítko **testování Request-Response**. 

### <a name="test-a-new-web-service"></a>Otestovat novou webovou službu

Nové webové služby můžete otestovat pouze na portálu webových služeb Machine Learning.

1. V [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu klikněte na **Test** v horní části stránky. **Test** stránka se otevře a můžete zadat data pro službu. Vstupní pole zobrazí odpovídají sloupcům, které se zobrazovaly v původní datové sady úvěrové riziko. 

1. Zadejte sadu dat a pak klikněte na tlačítko **odpověď na požadavek testu**.

Výsledky testu se zobrazí na pravé straně stránky ve výstupním sloupci. 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby Classic nebo nové, ji můžete spravovat z [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu.

Ke sledování výkonu webové služby:

1. Přihlaste se k [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu
1. Klikněte na tlačítko **webové služby**
1. Klikněte na webovou službu
1. Klikněte na tlačítko **řídicího panelu**

## <a name="access-the-web-service"></a>Nastavení přístupu k webové službě

V předchozím kroku v tomto kurzu jste nasadili webovou službu, která používá vaše úvěrové riziko prediktivního modelu. Uživatelé teď budou moct odesílat data a zobrazí výsledky. 

Webová služba je webová služba Azure, který dokáže přijmout a vrátit data pomocí rozhraní REST API v jednom ze dvou způsobů:  

* **Žádost/odpověď** – uživatel odesílá jeden nebo více řádků daty o úvěrovém ke službě s použitím protokolu HTTP a služba jako odpověď vrátí jednu nebo více sad výsledků.
* **Spuštění dávky** – uživatel uloží jednu nebo více řádků daty o úvěrovém v Azure blob a pak odešle umístění objektu blob do služby. Služba získává všechny řádky dat do vstupního objektu blob, jiný objekt blob uloží výsledky a vrátí adresu URL tohoto kontejneru.  

Nejrychlejší a nejjednodušší způsob pro přístup k webové služby Classic, je prostřednictvím [webová aplikace Azure ML Request Response Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) nebo [Azure ML Batch spuštění služby webové aplikace šablona](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Tyto šablony webové aplikace můžete vytvářet vlastní webové aplikace, který zná vstupních dat webové služby a budou vracet. Všechno, co je třeba provést je poskytnout přístup k vaší webové služby a data a udělá zbytek šablony.

Další informace o použití šablony webové aplikace najdete v tématu [využívání služby Azure Machine Learning Web pomocí šablony webové aplikace](consume-web-service-with-web-app-template.md).



## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste dokončili tyto kroky:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Test webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

Můžete také vyvíjet vlastní aplikace pro přístup k webové službě pomocí počáteční kód pro vás k dispozici v R, C#, a programovací jazyky Python.

> [!div class="nextstepaction"]
> [Využívání služby Azure Machine Learning Web](consume-web-services.md)

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
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/