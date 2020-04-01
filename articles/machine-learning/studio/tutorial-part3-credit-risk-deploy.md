---
title: 'Kurz 3: Nasazení modelu úvěrového rizika'
titleSuffix: Azure Machine Learning Studio (classic)
description: Podrobný kurz, který ukazuje, jak vytvořit prediktivní analytické řešení pro hodnocení úvěrového rizika v Azure Machine Learning Studio (klasické). Tento kurz je třetí částí třídílné série kurzů. Ukazuje, jak nasadit model jako webovou službu.
keywords: úvěrové riziko, prediktivní analytické řešení, hodnocení rizik, nasazení, webové služby
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204149"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Kurz 3: Nasazení modelu úvěrového rizika – Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

V tomto kurzu se podrobněpodíváte na proces vývoje řešení prediktivní analýzy. Jednoduchý model vyvíjíte ve Strojovém učení (klasické).  Potom nasadit model jako webovou službu Azure Machine Learning.  Tento nasazený model můžete provést předpovědi pomocí nových dat. Tento výukový program je **třetí částí třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Hodnocení úvěrového rizika je složitý problém, ale tento výukový program to trochu zjednoduší. Použijete ji jako příklad toho, jak můžete vytvořit prediktivní analytické řešení pomocí Microsoft Azure Machine Learning Studio (klasické). Pro toto řešení budete používat Azure Machine Learning Studio (klasické) a webovou službu Machine Learning. 

V tomto třídílném kurzu začnete s veřejně dostupnými údaji o úvěrovém riziku.  Potom vyvinout a trénovat prediktivní model.  Nakonec nasadit model jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md)jste vytvořili pracovní prostor Machine Learning Studio (klasické), nahráli data a vytvořili experiment.

Ve druhé části kurzu jste [vycvičili](tutorial-part2-credit-risk-train.md)a vyhodnotili modely.

V této části tutoriálu si:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Otestování webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

## <a name="prerequisites"></a>Požadavky

Dokončení [druhé části výukového programu](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení
Chcete-li dát ostatním možnost používat prediktivní model, který jste vyvinuli v tomto kurzu, můžete jej nasadit jako webovou službu v Azure.

Až do této chvíle jste experimentovali s tréninkem našeho modelu. Ale nasazené služby již nebude dělat školení – bude generovat nové předpovědi tím, že vyhodnocuje vstup uživatele na základě našeho modelu. Takže uděláme nějakou přípravu, abychom přeměnili tento experiment z ***tréninkového*** experimentu na ***prediktivní*** experiment. 

Příprava na nasazení je třístupňový proces:  

1. Odebrání jednoho z modelů
1. Převedení *tréninkového experimentu,* který jste *vytvořili, na prediktivní experiment*
1. Nasazení prediktivního experimentu jako webové služby

### <a name="remove-one-of-the-models"></a>Odebrání jednoho z modelů

Nejprve musíte tento experiment trochu zkrátit. v současné době máte dva různé modely v experimentu, ale chcete použít pouze jeden model při nasazení tohoto jako webové služby.  

Řekněme, že jste se rozhodli, že model posíleného stromu fungoval lépe než model SVM. Takže první věc, kterou udělat, je odstranit [two-class support vector machine][two-class-support-vector-machine] modul a moduly, které byly použity pro školení. Kopii experimentu můžete chtít nejprve vytvořit kliknutím na **Uložit jako** v dolní části plátna experimentu.

musíte odstranit následující moduly:  

* [Support Vector Machine (SVM) se dvěma třídami][two-class-support-vector-machine]
* [Moduly modelu vlaku][train-model] a [modelu skóre,][score-model] které k němu byly připojeny
* [Normalizovat data][normalize-data] (oba)
* [Vyhodnoťte model][evaluate-model] (protože jsme dokončili vyhodnocení modelů)

Vyberte jednotlivé moduly a stiskněte klávesu Delete nebo klepněte pravým tlačítkem myši na modul a vyberte **příkaz Odstranit**. 

![Zvýrazní moduly, které chcete odstranit, aby se odstranil model Support Vector Machine](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Náš model by nyní měl vypadat nějak takto:

![Výsledný experiment při odstranění modelu podpůrného vektorového stroje](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nyní jsme připraveni nasadit tento model pomocí [dvoutřídní hodovaný rozhodovací strom][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převedení tréninkového experimentu na prediktivní experiment

Chcete-li tento model připravit k nasazení, je třeba převést tento trénovací experiment na prediktivní experiment. To zahrnuje tři kroky:

1. Uložte model, který jste vycvičili, a vyměňte naše školicí moduly
1. Ořízněte experiment, abyste odstranili moduly, které byly potřebné pouze pro školení
1. Definujte, kde bude webová služba přijímat vstup a kde generuje výstup

Můžete to provést ručně, ale naštěstí všechny tři kroky lze provést klepnutím na tlačítko **Nastavit webovou službu** v dolní části plátna experimentu (a výběrem **možnosti Prediktivní webová služba).**

> [!TIP]
> Pokud chcete další podrobnosti o tom, co se stane při převodu trénovacího experimentu na prediktivní experiment, přečtěte si informace o [tom, jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)](convert-training-experiment-to-scoring-experiment.md).

Po klepnutí na tlačítko **Nastavit webovou službu**se stane několik věcí:

* Trénovaný model je převeden na jeden **trénovaný model** modulu a uložen v paletě modulů nalevo od plátna experimentu (najdete jej pod **trénovanými modely)**
* Moduly, které byly použity pro školení jsou odstraněny; Konkrétně:
  * [Posílený rozhodovací strom se dvěma třídami][two-class-boosted-decision-tree]
  * [Model vlaku][train-model]
  * [Rozdělení dat][split]
  * druhý modul [Spustit skript R,][execute-r-script] který byl použit pro testovací data
* Uložený trénovaný model je přidán zpět do experimentu
* **Jsou** přidány vstupní a výstupní moduly webové **služby** (ty identifikují, kam data uživatele zadají model a jaká data jsou vrácena při přístupu k webové službě)

> [!NOTE]
> Můžete vidět, že experiment je uložen ve dvou částech pod kartami, které byly přidány v horní části plátna experimentu. Původní tréninkový experiment je pod záložkou **Tréninkový experiment**a nově vytvořený prediktivní experiment je pod **prediktivním experimentem**. Prediktivní experiment je ten, který budete nasadit jako webovou službu.

s tímto konkrétním experimentem je třeba udělat ještě jeden krok.
přidali jste dva moduly [Spustit skript R,][execute-r-script] abyste data poskytli váhovou funkci. To byl jen trik, který jste potřebovali pro školení a testování, takže můžete tyto moduly v konečném modelu vyřadit.
Machine Learning Studio (klasické) odebrané jeden [modul Spustit R Script][execute-r-script] při odebrání modulu [Split.][split] Nyní můžete odstranit ostatní a připojit [Editor metadat][metadata-editor] přímo k [score model][score-model].    

Náš experiment by nyní měl vypadat takto:  

![Bodování trénovaný model](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Možná se divíte, proč jste opustili datovou sadu německých údajů o kreditních kartě UCI v prediktivním experimentu. Služba bude skóre dat uživatele, nikoli původní datové sady, tak proč ponechat původní datovou sadu v modelu?
> 
> Je pravda, že služba nepotřebuje původní údaje o kreditní kartě. Ale potřebuje schéma pro tato data, která obsahuje informace, jako je například kolik sloupců jsou a sloupce jsou číselné. Tyto informace o schématu je nezbytné k interpretaci dat uživatele. ponecháte tyto součásti připojené tak, aby modul vyhodnocování má schéma datové sady, když je služba spuštěna. Data se nepoužívají, pouze schéma.  
> 
>Jedna důležitá věc, kterou je třeba poznamenat, je, že pokud původní datová sada obsahovala popisek, pak očekávané schéma z webového vstupu bude také očekávat sloupec s popiskem! Způsob, jak to obejít, je odebrat popisek a všechna další data, která byla v trénovací datové sadě, ale nebude ve webových vstupech, před připojením webového vstupu a trénovací datové sady do společného modulu. 
> 

Spusťte experiment naposledy (klepněte na tlačítko **Spustit**.) Pokud chcete ověřit, zda model stále pracuje, klikněte na výstup modulu [Model skóre][score-model] a vyberte **Zobrazit výsledky**. Můžete vidět, že jsou zobrazena původní data spolu s hodnotou úvěrového rizika ("Scoredlabely") a hodnotou pravděpodobnosti hodnocení ("Pravděpodobnost skóre".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Experiment můžete nasadit jako klasickou webovou službu nebo jako novou webovou službu, která je založená na Azure Resource Manageru.

### <a name="deploy-as-a-classic-web-service"></a>Nasazení jako klasické webové služby
Chcete-li nasadit klasickou webovou službu odvozenou z našeho experimentu, klepněte na **tlačítko Nasadit webovou službu** pod plátnem a vyberte **možnost Nasadit webovou službu [Classic]**. Machine Learning Studio (classic) nasazuje experiment jako webovou službu a přenese vás na řídicí panel pro tuto webovou službu. Na této stránce se můžete vrátit k experimentu **(Zobrazit snímek** nebo **Zobrazit nejnovější)** a spustit jednoduchý test webové služby (viz **Test webové služby** níže). K dispozici jsou také informace pro vytváření aplikací, které mají přístup k webové službě (více o tom v dalším kroku tohoto kurzu).

![Řídicí panel webové služby](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Službu můžete nakonfigurovat kliknutím na kartu **KONFIGURACE.** Zde můžete upravit název služby (ve výchozím nastavení je uveden název experimentu) a dát mu popis. Můžete také poskytnout popisky pro vstupní a výstupní data.  

![Konfigurace webové služby](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Nasazení jako nové webové služby

> [!NOTE] 
> Chcete-li nasadit novou webovou službu, musíte mít dostatečná oprávnění v předplatném, do kterého nasadíte webovou službu. Další informace najdete [v tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Nasazení nové webové služby odvozené z našeho experimentu:

1. Pod plátnem klepněte na **tlačítko Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [New]**. Machine Learning Studio (klasické) vás přenese na webovou stránku Deploy **Experiment** azure machine learningu.

1. Zadejte název webové služby. 

1. V **případě Cenový plán**můžete vybrat existující cenový plán nebo vybrat možnost Vytvořit nový, pojmenovat nový plán a vybrat možnost měsíčního plánu. Úrovně plánu výchozí plány pro výchozí oblast a vaše webová služba je nasazena do této oblasti.

1. Klepněte na tlačítko **Nasadit**.

Po několika minutách se otevře stránka **Rychlý start** webové služby.

Službu můžete nakonfigurovat kliknutím na kartu **Konfigurovat.** Zde můžete upravit název služby a dát mu popis. 

Chcete-li webovou službu otestovat, klepněte na kartu **Test** (viz **Test webové služby** níže). Informace o vytváření aplikací, které mají přístup k webové službě, klikněte na kartu **Spotřebovábka** (další krok v tomto kurzu půjde do podrobností).

> [!TIP]
> Webovou službu můžete aktualizovat po její nasazení. Chcete-li například změnit model, můžete upravit trénovací experiment, vyladit parametry modelu a klepnout na tlačítko **Nasadit webovou službu**, vybrat možnost **Nasadit webovou službu [Classic]** nebo **Nasadit webovou službu [New]**. Při nasazení experimentu znovu nahradí webovou službu, nyní pomocí aktualizovaného modelu.  
> 
> 

## <a name="test-the-web-service"></a>Otestování webové služby

Při přístupu k webové službě data uživatele zadá prostřednictvím **vstupního** modulu webové služby, kde je předána modulu [Model skóre][score-model] a scored. Způsob, jakým jste nastavili prediktivní experiment, model očekává data ve stejném formátu jako původní datová sada úvěrového rizika.
Výsledky jsou vráceny uživateli z webové služby prostřednictvím výstupního modulu **webové služby.**

> [!TIP]
> Způsob, jakým máte nakonfigurovaný prediktivní experiment, jsou vráceny všechny výsledky z modulu [Model skóre.][score-model] To zahrnuje všechny vstupní údaje plus hodnotu úvěrového rizika a pravděpodobnost hodnocení. Ale můžete vrátit něco jiného, pokud chcete - například můžete vrátit pouze hodnotu úvěrového rizika. Chcete-li to provést, vložte modul [Vybrat sloupce][select-columns] mezi [modelem skóre][score-model] a **výstupem webové služby,** abyste odstranili sloupce, které nechcete, aby se webová služba vrátila. 
> 
> 

Klasickou webovou službu můžete otestovat buď v **Machine Learning Studio (klasické)** nebo na portálu **Azure Machine Learning Web Services.**
Novou webovou službu můžete otestovat pouze na portálu **Machine Learning Web Services.**

> [!TIP]
> Při testování na portálu Azure Machine Learning Web Services můžete nechat portál vytvořit ukázková data, která můžete použít k testování služby Požadavek-odpověď. Na stránce **Konfigurovat** vyberte možnost Ano pro **povolenou ukázková data?**. Když otevřete kartu Požadavek odpověď na stránce **Test,** portál vyplní ukázková data převzatá z původní datové sady úvěrového rizika.

### <a name="test-a-classic-web-service"></a>Testování klasické webové služby

Klasickou webovou službu můžete otestovat v Machine Learning Studiu (klasické) nebo na portálu Machine Learning Web Services. 

#### <a name="test-in-machine-learning-studio-classic"></a>Test ve studiu strojového učení (klasika)

1. Na stránce **ŘÍDICÍPANEL** pro webovou službu klepněte na tlačítko **Testovat** v části **Výchozí koncový bod**. Zobrazí se dialogové okno s dotazem na zadání vstupních dat pro službu. Jedná se o stejné sloupce, které se objevily v původní datové sadě úvěrového rizika.  

1. Zadejte sadu dat a klepněte na tlačítko **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testování na portálu Machine Learning Web Services

1. Na stránce **ŘÍDICÍPANEL** pro webovou službu klikněte na odkaz **Testovat náhled** v části **Výchozí koncový bod**. Otevře se testovací stránka na portálu Azure Machine Learning Web Services pro koncový bod webové služby a zobrazí se dotaz na vstupní data pro službu. Jedná se o stejné sloupce, které se objevily v původní datové sadě úvěrového rizika.

2. Klepněte na tlačítko **Testovat odpověď na požadavek**. 

### <a name="test-a-new-web-service"></a>Testování nové webové služby

Novou webovou službu můžete otestovat pouze na portálu Machine Learning Web Services.

1. Na portálu [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) klikněte v horní části stránky na **Testovat.** Otevře se stránka **Test** a můžete zadat data pro službu. Zobrazená vstupní pole odpovídají sloupcům, které se objevily v původní datové sadě úvěrového rizika. 

1. Zadejte sadu dat a klepněte na tlačítko **Testovat odpověď na požadavek .**

Výsledky testu jsou zobrazeny na pravé straně stránky ve výstupním sloupci. 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby, ať už klasické nebo nové, můžete ji spravovat z portálu [Microsoft Azure Machine Learning Web Services.](https://services.azureml.net/quickstart)

Sledování výkonu webové služby:

1. Přihlášení k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)
1. Klikněte na **webové služby.**
1. Klikněte na webovou službu.
1. Klikněte na **řídicí panel**

## <a name="access-the-web-service"></a>Nastavení přístupu k webové službě

V předchozím kroku v tomto kurzu jste nasadili webovou službu, která používá váš model predikce úvěrového rizika. Nyní mohou uživatelé odesílat data a přijímat výsledky. 

Webová služba je webová služba Azure, která může přijímat a vracet data pomocí api REST jedním ze dvou způsobů:  

* **Požadavek/odpověď** – uživatel odešle jeden nebo více řádků dat kreditu službě pomocí protokolu HTTP a služba odpoví jednou nebo více sadami výsledků.
* **Dávkové spuštění** – uživatel uloží jeden nebo více řádků dat kreditu v objektu blob Azure a potom odešle umístění objektu blob do služby. Služba zatkne všechny řádky dat ve vstupním objektu blob, uloží výsledky do jiného objektu blob a vrátí adresu URL tohoto kontejneru.  

Další informace o přístupu k webové službě a její využívání najdete v [tématu Využití webové služby Azure Machine Learning pomocí šablony webové aplikace](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky:

> [!div class="checklist"]
> * Příprava nasazení
> * Nasazení webové služby
> * Otestování webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

Můžete také vytvořit vlastní aplikaci pro přístup k webové službě pomocí počátečního kódu, který je pro vás k dispozici v programovacích jazycích R, C# a Pythonu.

> [!div class="nextstepaction"]
> [Využití webové služby Azure Machine Learning](consume-web-services.md)

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
