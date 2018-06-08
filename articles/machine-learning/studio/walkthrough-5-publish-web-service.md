---
title: 'Krok 5: Nasazení webové služby Machine Learning | Microsoft Docs'
description: 'Krok 5 vývoj prediktivního řešení návod: nasazení prediktivní experiment v nástroji Machine Learning Studio jako webovou službu.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: 7a0dc7e92df342789d2c498479aa54b94070c9f4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835939"
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Krok 5 průvodce: Nasazení webové služby Azure Machine Learning
Toto je pátý krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. **Nasazení webové služby**
6. [Nastavení přístupu k webové službě](walkthrough-6-access-web-service.md)

- - -
Ostatním uživatelům možnost použít prediktivní model, který jsme jste vytvořili v tomto návodu, můžeme ho nasadit jako webovou službu v Azure.

V tomto okamžiku jste byla jsme experimentování se cvičení naše modelu. Ale nasazená služba je již má proveďte školení – má vygenerujte nový předpovědi vyhodnocování vstupu uživatele na základě naše modelu. Takže vytvoříme provést určitou přípravu pro převod tohoto experimentu z ***školení*** experimentovat k ***prediktivní*** experimentovat. 

Jedná se o proces třech krocích:  

1. Odeberte jeden z modelů
2. Převést *výukový experiment* vytvořili jsme do *prediktivní experiment*
3. Nasadit prediktivní experiment jako webovou službu

## <a name="remove-one-of-the-models"></a>Odeberte jeden z modelů

Nejprve musíme trochu trim tohoto experimentu dolů. V současné době máme dva různé modely v experimentu, ale chceme používat jeden model, když jsme nasadit jako webovou službu.  

Řekněme, že jsme jste se rozhodli lépe než SVM model provést boosted stromu modelu. Tak, aby první věc, kterou chcete odebrat [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu a moduly, které se používaly k cvičení ho. Můžete chtít vytvořit kopii experimentu nejprve kliknutím **uložit jako** v dolní části na plátno experimentu.

Je potřeba odstranit následující moduly:  

* [Two-Class Support Vector Machine][two-class-support-vector-machine]
* [Cvičení modelu] [ train-model] a [Score Model] [ score-model] moduly, které byly připojené k němu
* [Normalizuje Data][normalize-data] (obou z nich)
* [Vyhodnocení modelu][evaluate-model] (protože jsme hotovi, vyhodnocení modelů)

Vyberte každý modul a stiskněte klávesu Delete, nebo klikněte pravým tlačítkem na modul a vyberte **odstranit**. 

![Odebrat SVM modelu][3a]

Naše modelu by teď měl vypadat přibližně takto:

![Odebrat SVM modelu][3]

Nyní je vše připraveno k nasazení této konfigurace pomocí modelu [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převést výukový experiment prediktivní experiment

Tento model Příprava pro nasazení, je potřeba převést tento výukový experiment prediktivní experiment. To zahrnuje tři kroky:

1. Uložit model jsme natrénovali a potom můžete nahradit naše školení moduly
2. Trim experimentu odebrat moduly, které byly potřeba jenom pro školení
3. Zadejte kde webová služba bude přijímat vstup a kde vygeneruje výstup

Jsme může udělat to ručně, ale naštěstí všechny tři kroky, můžete provést kliknutím na tlačítko **nastavit webové služby** v dolní části plátna experimentu (a výběrem **webové služby prediktivní** možnost).

> [!TIP]
> Pokud chcete podrobnosti na co se stane, když převedete výukový experiment prediktivní experiment najdete v tématu [postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Když kliknete na tlačítko **nastavit webové služby**, dojde k několika změnám:

* Pro cvičný model je převést na jednu **Trained Model** modulu a uložené paletě modulů nalevo od plátna experimentu (najdete ji v části **Trénované modely**)
* Moduly, které jste použili pro školení jsou odebrány; konkrétně:
  * [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  * [Train Model][train-model]
  * [Rozdělení dat][split]
  * druhý [spustit skript jazyka R] [ execute-r-script] modul, který byl použit pro testovací data
* Uložené trained model je přidat zpět do experimentu
* **Webové služby vstup** a **webové služby výstup** moduly jsou přidány (ty identifikují kde budou data uživatele zadejte modelu, a jaká data se vrátí, při přístupu k webové službě)

> [!NOTE]
> Uvidíte, že experiment uloží ve dvou částech na kartách, které byly přidány v horní části na plátno experimentu. Původní výukový experiment je na kartě **výukový experiment**, a nově vytvořený prediktivní experiment je pod **prediktivní experiment**. Prediktivní experiment je ten, který jsme vám nasadit jako webovou službu.

Je potřeba provést další krok se tento konkrétní experimentu.
Jsme přidali dvě [spustit skript jazyka R] [ execute-r-script] moduly, které poskytují funkce vyvážení k datům. Proto jsme vyjměte tyto moduly v posledním modelu, který byl právě efektu potřebnou pro trénování a testování.
Machine Learning Studio odebrat jeden [spustit skript jazyka R] [ execute-r-script] modulu odebrán [rozdělení] [ split] modulu. Můžete odebrat dalších a připojit teď [Metadata Editor] [ metadata-editor] přímo na [Score Model][score-model].    

Naše experiment by měl nyní vypadat takto:  

![Vyhodnocování trénovaného modelu][4]  

> [!NOTE]
> Asi vás zajímá, proč jsme left UCI němčina platební karty dat datové sady v prediktivní experiment. Služba se bude stanovení skóre data uživatele, není původní datové sady, takže proč nechte původní datové sady v modelu?
> 
> Je PRAVDA, že službu nemusí původní data platební karty. Ale potřebuje schéma pro tato data, což zahrnuje informace, jako je počet sloupců, jsou a sloupce, které jsou číselná. Tato informace o schématu je nutné interpretovat data uživatele. Jsme ponechat tyto součásti připojení tak, aby modul vyhodnocování nemá schéma datové sady, pokud je služba spuštěná. Data se nepoužívá, právě schématu.  
> 
> 

Spusťte experiment jednou (klikněte na tlačítko **spustit**.) Pokud chcete ověřit, zda model ještě pracuje, klikněte na výstup [Score Model] [ score-model] modul a vyberte **zobrazení výsledků**. Uvidíte, že se zobrazí původní data, společně s platební riziko hodnotu ("popisky vyhodnocení") a vyhodnocování hodnotu pravděpodobnosti ("skóre pro Magnitudu Pravděpodobnostech".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Experiment můžete nasadit jako buď Classic webovou službu nebo jako novou webovou službu, která je založená na Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Nasadit jako webovou službu Classic
Chcete-li nasadit odvozené z našich experimentu webové služby Classic, klikněte na tlačítko **nasazení webové služby** níže na plátno a vyberte **nasazení webové služby [Classic]**. Machine Learning Studio nasadí experimentu jako webovou službu a přejdete na řídicí panel pro tuto webovou službu. Z této stránky můžete vrátit do experimentu (**zobrazení snímku** nebo **zobrazit nejnovější**) a spusťte jednoduchý test webové služby (najdete v části **testování webovou službu** níže). Je zde také informace pro vytváření aplikací, které můžete přístup k webové službě (Další informace o, v dalším kroku tohoto názorného postupu).

![Řídicí panel webové služby][6]

Můžete nakonfigurovat službu kliknutím **konfigurace** kartě. Zde můžete upravit název služby (poskytla název experimentu ve výchozím nastavení) a zadejte jeho popis. Další popisný popisky můžete udělit taky pro vstupní a výstupní data.  

![Konfigurovat webovou službu][5]  

### <a name="deploy-as-a-new-web-service"></a>Nasadit jako novou webovou službu

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které nasazujete webovou službu. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Nasadit novou webovou službu získané z našich experimentu:

1. Klikněte na tlačítko **nasazení webové služby** níže na plátno a vyberte **nasazení [nové] webové služby**. Machine Learning Studio přenosy, můžete na webové služby Azure Machine Learning **nasazení experimentu** stránky.

2. Zadejte název pro webovou službu. 

3. Pro **cena plán**, můžete vybrat existující cenový plán, nebo vyberte "Vytvořit nový" a pojmenujte nový plán a vyberte možnost měsíčního plánu. Výchozí plán vrstvy do plánů pro vaši oblast výchozí a webové služby je nasazený na danou oblast.

4. Klikněte na tlačítko **nasazení**.

Po několika minutách **rychlý Start** otevře se stránka pro webovou službu.

Můžete nakonfigurovat službu kliknutím **konfigurace** kartě. Zde můžete upravit službu title a zadejte jeho popis. 

K otestování webové služby, klikněte na tlačítko **testování** karta (najdete v části **testování webovou službu** níže). Informace o vytváření aplikací, které můžete přístup k webové službě, klikněte **spotřebě** karta (na další krok v tomto návodu přejde do více podrobností).

> [!TIP]
> Poté, co nasadíte ji můžete aktualizovat webovou službu. Například pokud chcete změnit váš model pak výukový experiment můžete upravit, upravit parametry modelu a klikněte na **nasazení webové služby**, vyberete **nasazení webové služby [Classic]** nebo **Nasazení webové služby [nové]**. Při nasazení experiment, nahradí webové službě, teď pomocí aktualizovaném modelu.  
> 
> 

## <a name="test-the-web-service"></a>Test webové služby

Při přístupu k webové službě uživatele zadá prostřednictvím **webové služby vstup** modulu, kde je předána [Score Model] [ score-model] modulu a skóre. Způsob, jakým jsme zřídili prediktivní experiment, očekává modelu dat ve stejném formátu jako původní datové sady úvěrové riziko.
Jsou vráceny výsledky pro uživatele z webové služby pomocí **webové služby výstup** modulu.

> [!TIP]
> Způsob, jak máme prediktivní experiment nakonfigurované, výsledkem celý [Score Model] [ score-model] modulu jsou vráceny. To zahrnuje všechny vstupní data a hodnota riziko platební a vyhodnocování pravděpodobnosti. Ale něco jiného může vrátit, pokud chcete – například může vrátit pouze hodnota platební riziko. Chcete-li to provést, vložte [sloupce projektu] [ project-columns] modulu mezi [Score Model] [ score-model] a **webové služby výstup**k vyloučení sloupce nechcete webovou službu vrátit. 
> 
> 

Můžete otestovat Classic webové služby buď v **Machine Learning Studio** nebo **webové služby Azure Machine Learning** portálu.
Můžete otestovat nové webové služby pouze v **webové služby Machine Learning** portálu.

> [!TIP]
> Při testování na portálu Azure Machine Learning webové služby, můžete mít portálu vytvoření ukázkových dat, který můžete použít k testování služby požadavků a odpovědí. Na **konfigurace** vyberte "Ano" pro **ukázkových dat povolené?**. Když otevřete kartu požadavků a odpovědí na **Test** stránce portálu vyplní ukázková data přijatá z původní datové sady úvěrové riziko.

### <a name="test-a-classic-web-service"></a>Testování Classic webové služby

Webová služba Classic můžete otestovat v nástroji Machine Learning Studio nebo na portálu webové služby Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testování v nástroji Machine Learning Studio

1. Na **řídicí panel** stránky pro webovou službu, klikněte na tlačítko **Test** tlačítko pod **výchozí koncový bod**. Zobrazí se dialogové okno se zobrazí a se vás zeptá na vstupní data pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.  

2. Zadejte sadu dat a pak klikněte na tlačítko **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testování v portálu webové služby Machine Learning

1. Na **řídicí panel** stránky pro webovou službu, klikněte na tlačítko **testovací preview** v části **výchozí koncový bod**. Zkušební stránku na portálu Azure Machine Learning webové služby pro koncový bod webové služby se otevře a se vás zeptá na vstupní data pro službu. Jedná se o stejné sloupce, které se zobrazovaly v původní datové sady úvěrové riziko.

2. Klikněte na tlačítko **testování požadavků a odpovědí**. 

### <a name="test-a-new-web-service"></a>Otestovat novou webovou službu

Pouze na portálu webové služby Machine Learning můžete testovat novou webovou službu.

1. V [webové služby Azure Machine Learning](https://services.azureml.net/quickstart) portálu klikněte na **Test** v horní části stránky. **Test** otevře se stránka a můžete vstupní data pro službu. Vstupní pole zobrazí odpovídají na sloupce, které se zobrazovaly v původní datové sady úvěrové riziko. 

2. Zadejte sadu dat a pak klikněte na tlačítko **testu požadavků a odpovědí**.

Výsledky testu se zobrazí na pravé straně stránky v výstupního sloupce. 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby, zda Classic nebo nové, můžete spravovat z [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portálu.

Sledovat výkon webové služby:

1. Přihlaste se k [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portálu
2. Klikněte na tlačítko **webové služby**
3. Klikněte na webovou službu
4. Klikněte **řídicí panel**

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
