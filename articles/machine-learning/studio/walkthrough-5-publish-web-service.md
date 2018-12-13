---
title: 'Krok 5: Nasazení webové služby – Azure Machine Learning Studio | Dokumentace Microsoftu'
description: 'Krok 5 vývoj prediktivního řešení návod: nasazení prediktivní experiment v Machine Learning studiu jako webovou službu.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: b0da85625ab938ea050ec6ae7bb1d66b095370b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077133"
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-studio-web-service"></a>Krok 5 průvodce: Nasazení webové služby Azure Machine Learning Studio
Toto je pátý krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. **Nasazení webové služby**
6. [Nastavení přístupu k webové službě](walkthrough-6-access-web-service.md)

- - -
Ostatním uživatelům možnost používat prediktivní model, který jsme vypracovali v tomto podrobném návodu, můžeme ji nasadit jako webovou službu v Azure.

Do této chvíle jsme experimentovali s školení náš model. Ale v nasazované službě se už to proveďte školení – chce generovat nových předpovědí podle hodnocení vstupu uživatele na základě našich modelu. Tedy budeme provést určitou přípravu pro převod z tohoto experimentu ***školení*** experimentovat ***prediktivní*** experimentovat. 

Jde o třech krocích:  

1. Odeberte jeden z modelů
2. Převést *výukového experimentu* jsme vytvořili do *prediktivní experiment*
3. Prediktivní experiment nasadit jako webovou službu

## <a name="remove-one-of-the-models"></a>Odeberte jeden z modelů

Nejdřív potřebujeme trochu trim tento experiment dolů. Aktuálně máme dva různé modely v experimentu, ale chceme použít jeden model, pokud jsme ji nasadit jako webovou službu.  

Řekněme, že můžeme říct, že model posíleného stromu lepších výsledků než SVM modelu. Proto první věc, kterou chcete odebrat [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu a moduly, které byly použity pro trénování ho. Možná budete chtít vytvořit kopii experimentu nejprve kliknutím **uložit jako** v dolní části na plátno experimentu.

Musíme odstranit následující moduly:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* [Trénování modelu] [ train-model] a [Score Model] [ score-model] moduly, které byly k ní připojená.
* [Normalizuje Data][normalize-data] (obou z nich)
* [Vyhodnocení modelu][evaluate-model] (protože jsme hotovi, vyhodnocení modelů)

Vyberte každého modulu a stiskněte klávesu Delete nebo klikněte pravým tlačítkem na modul a vyberte **odstranit**. 

![Odebrat SVM modelu][3a]

Náš model by teď měl vypadat přibližně takto:

![Odebrat SVM modelu][3]

Nyní jsme připraveni k nasazení pomocí modelu [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment

Chcete-li získat tento model připravené na nasazení, potřebujeme převést tento výukový experiment prediktivní experiment. To zahrnuje tři kroky:

1. Uložit model jsme natrénovali a potom nahraďte naše školicí moduly
2. Trim experiment odebrat modulů, které byly potřeba jenom pro školení
3. Definujte, ve kterém webová služba bude přijímat vstup a kde se vygeneruje výstup

Můžeme to udělat ručně, ale naštěstí všechny tři kroky můžete provést kliknutím **nastavení webové služby** v dolní části na plátno experimentu (a vyberete **prediktivní webová služba** možnost).

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
> Uvidíte, že experiment se uloží do dvou částí v rámci karty, které byly přidány v horní části na plátno experimentu. Původní výukového experimentu je na kartě **výukového experimentu**, a nově vytvořený prediktivní experiment probíhá **prediktivní experiment**. Prediktivní experiment je ten, který nasadíme jako webovou službu.

Je potřeba provést další krok se tento konkrétní experiment.
Přidali jsme dvě [spustit skript jazyka R] [ execute-r-script] moduly, které poskytují funkce váhu k datům. Který byl právě zdvih, kterou jsme potřebovali pro trénování a testování, tak můžeme vzít si tyto moduly v finálního modelu.
Machine Learning Studio odebrat jeden [spustit skript jazyka R] [ execute-r-script] modulu odebrán [rozdělení] [ split] modulu. Teď můžeme jinou odebrat a připojit [Editor metadat] [ metadata-editor] přímo na [Score Model][score-model].    

Naše experiment měl vypadat takto:  

![Vyhodnocování trénovaného modelu][4]  

> [!NOTE]
> Asi vás zajímá proč jsme left údajů o kreditních kartách němčina UCI datovou sadu v prediktivní experiment. Služba se to skóre data uživatele, nikoli původní datové sady, tak proč nechte původní datové sady v modelu?
> 
> To platí, že službu nemusí původní údajů o kreditních kartách. Ale ji potřebuje pro tato data, která obsahuje informace, jako je počet sloupců nejsou a sloupce, které jsou číselné schéma. Tyto informace schématu je nutné interpretovat data uživatele. Nezpracováváme a necháváme tyto součásti připojení tak, aby modul pro stanovení skóre měla schéma datové sady, když je služba spuštěná. Data se nepoužívá, pouze schéma.  
> 
>Jeden důležité si uvědomit je, že pokud původní datové sady obsahuje popisek, pak očekávané schéma ze vstupu web bude také předpokládají, že sloupec s popiskem! Odebrat popisek a jiná data, která byla v trénovací datové sady, ale nebude ve vstupech web před připojením vstup webové a trénovací datové sady do modulu common je možné vyřešit. 
> 

Spusťte experiment jednou (klikněte na tlačítko **spustit**.) Pokud chcete ověřit, že je model stále funguje, kliknutím na výstup [Score Model] [ score-model] modul a vyberte **zobrazení výsledků**. Uvidíte, že se zobrazí původní data, společně s riziko hodnota kreditů ("popisky vyhodnocení") a bodovací hodnota pravděpodobnosti ("skóre pravděpodobnosti".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit experiment jako buď webové služby Classic, nebo jako novou webovou službu, která je založená na Azure Resource Manageru.

### <a name="deploy-as-a-classic-web-service"></a>Nasadit jako webovou službu Classic
Nasazení webové služby Classic odvozený z našich experiment, klikněte na tlačítko **nasadit webovou službu** dole na plátně a vyberte **nasazení webové služby [Classic]**. Machine Learning Studio nasadí experiment jako webové služby a přejdete na řídicí panel pro tuto webovou službu. Na této stránce můžete vrátit do experimentu (**zobrazení snímku** nebo **zobrazit nejnovější**) a spouštět jednoduchý test webové služby (naleznete v tématu **testovat webovou službu** níže). Je zde také informace pro vytváření aplikací, které můžete přístup k webové službě (více zabývat v dalším kroku tohoto návodu).

![Řídicí panel webové služby][6]

Můžete nakonfigurovat službu kliknutím **konfigurace** kartu. Tady můžete upravit název služby (to má zadaný název experimentu ve výchozím nastavení) a zadejte pro něj popis. Můžete také udělit další popisný popisky pro vstupní a výstupní data.  

![Konfigurovat webovou službu][5]  

### <a name="deploy-as-a-new-web-service"></a>Nasadit jako novou webovou službu

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do kterého nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

K nasazení nové webové služby získané z našich experiment:

1. Klikněte na tlačítko **nasadit webovou službu** dole na plátně a vyberte **nasazení [nové] webová služba**. Machine Learning Studio přenese je na webové služby Azure Machine Learning **nasadit Experiment** stránky.

2. Zadejte název pro webovou službu. 

3. Pro **cenový plán**, můžete vybrat stávajícím cenovým tarifem, nebo vyberte "Vytvořit novou" a pojmenujte nový plán a možnost měsíční plán. Výchozí plán úrovně plány vaší výchozí oblasti a webová služba je nasazený na danou oblast.

4. Klikněte na tlačítko **nasazení**.

Po několika minutách **rychlý Start** se otevře stránka pro webovou službu.

Můžete nakonfigurovat službu kliknutím **konfigurovat** kartu. Tady můžete upravit službu nadpisu a zadejte pro něj popis. 

K otestování webové služby, klikněte na tlačítko **testování** kartu (naleznete v tématu **testovat webovou službu** níže). Informace o vytváření aplikací, které můžou přistupovat k webové službě, kliknutím na odkaz **využívání** kartu (dál v tomto názorném postupu přejde do další podrobnosti).

> [!TIP]
> Po jeho nasazení, můžete aktualizovat webovou službu. Například pokud chcete změnit váš model, pak výukového experimentu můžete upravit, upravit parametry modelu a klikněte na **nasadit webovou službu**, kde vyberou **nasazení webové služby [Classic]** nebo **Nasazení webové služby [nové]**. Při nasazení experimentu se nahradí webovou službu, teď pomocí aktualizovaného modelu.  
> 
> 

## <a name="test-the-web-service"></a>Test webové služby

Při přístupu k webové službě, data uživatele zadá prostřednictvím **webové služby vstup** modul, kde je předán [určení skóre modelu] [ score-model] modulu a skóre. Způsob, jakým jsme vytvořili prediktivní experiment, modelu očekává, že data ve stejném formátu jako původní datové sady úvěrové riziko.
Výsledky se vrátí uživateli z webové služby prostřednictvím **webové služby výstup** modulu.

> [!TIP]
> Způsob, jakým jsme prediktivní experiment nakonfigurovaná, výsledkem celé [Score Model] [ score-model] modulu jsou vráceny. To zahrnuje veškerá vstupní data a rizika hodnota kreditů a bodovací pravděpodobnosti. Ale něco jiného může vrátit, pokud chcete, aby – například může vrátit pouze hodnota kreditů rizika. Chcete-li to provést, vložte [sloupce projektu] [ project-columns] modulu mezi [Score Model] [ score-model] a **webové služby výstup**Chcete-li odstranit sloupce nechcete, aby webové služby se vraťte. 
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

2. Zadejte sadu dat a pak klikněte na tlačítko **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Test na portálu webových služeb Machine Learning

1. Na **řídicí panel** stránky pro webovou službu, klikněte na tlačítko **Test preview** odkaz pod **výchozí koncový bod**. Zkušební stránku na portálu Azure Machine Learning Web Services pro koncový bod webové služby se otevře a zobrazí výzvu k vstupní data pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.

2. Klikněte na tlačítko **testování Request-Response**. 

### <a name="test-a-new-web-service"></a>Otestovat novou webovou službu

Nové webové služby můžete otestovat pouze na portálu webových služeb Machine Learning.

1. V [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu klikněte na **Test** v horní části stránky. **Test** stránka se otevře a můžete zadat data pro službu. Vstupní pole zobrazí odpovídají sloupcům, které se zobrazovaly v původní datové sady úvěrové riziko. 

2. Zadejte sadu dat a pak klikněte na tlačítko **odpověď na požadavek testu**.

Výsledky testu se zobrazí na pravé straně stránky ve výstupním sloupci. 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby Classic nebo nové, ji můžete spravovat z [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu.

Ke sledování výkonu webové služby:

1. Přihlaste se k [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu
2. Klikněte na tlačítko **webové služby**
3. Klikněte na webovou službu
4. Klikněte na tlačítko **řídicího panelu**

- - -
**Další krok: [přístup k webové službě](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


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
