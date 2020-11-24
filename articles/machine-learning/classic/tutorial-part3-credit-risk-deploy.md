---
title: 'Kurz pro ML Studio (Classic) 3: nasazení modelů úvěrového rizika – Azure'
description: Podrobný kurz, který ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning Studio (Classic). Tento kurz je třetí částí série kurzů s třemi částmi. Ukazuje, jak nasadit model jako webovou službu.
keywords: úvěrové riziko, řešení prediktivní analýzy, posouzení rizik, nasazení, Webová služba
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 3afcf87c360651ac314450910fbf5ab72afd289a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503909"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Kurz 3: nasazení modelu úvěrového rizika – Azure Machine Learning Studio (Classic)

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

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
> * Otestování webové služby
> * Správa webové služby
> * Nastavení přístupu k webové službě

## <a name="prerequisites"></a>Předpoklady

Vyplňte [druhou část kurzu](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení
Pokud chcete ostatním umožnit používání prediktivního modelu, který jste vytvořili v tomto kurzu, můžete ho nasadit jako webovou službu v Azure.

Až do tohoto okamžiku jste se seznámili s školením našeho modelu. Nicméně nasazená služba již neprovádí školení – vygeneruje nové předpovědi vyhodnocením vstupu uživatele na základě našeho modelu. Provedeme si ale nějaké přípravy na převod tohoto experimentu z experimentu ***školení** _ na _*_prediktivní_*_ experiment. 

Příprava na nasazení je proces tří kroků:  

1. Odebrat jeden z modelů
1. Převedení _training experimentu *, který jste vytvořili, do *prediktivního experimentu*
1. Nasazení prediktivního experimentu jako webové služby

### <a name="remove-one-of-the-models"></a>Odebrat jeden z modelů

Nejdřív je potřeba zkrátit tento experiment trochu. v současné době máte dva různé modely v experimentu, ale chcete použít jenom jeden model, když ho nasadíte jako webovou službu.  

Řekněme, že jste se rozhodli, že se zesílený stromový model vypnul lépe než model SVM. První věc, kterou je potřeba udělat, je odebrat modul [vektorového počítače podpory dvou tříd][two-class-support-vector-machine] a moduly, které se použily k jeho školení. Kopii experimentu můžete vytvořit nejdřív tak, že kliknete na **Uložit jako** v dolní části plátna experimentu.

je nutné odstranit následující moduly:  

* [Support Vector Machine (SVM) se dvěma třídami][two-class-support-vector-machine]
* [Model výuky][train-model] a modely [skóre][score-model] , které se k němu připojily
* [Normalizovat data][normalize-data] (obě z nich)
* [Vyhodnotit model][evaluate-model] (protože dokončili jste vyhodnocování modelů)

Vyberte jednotlivé moduly a stiskněte klávesu DELETE nebo klikněte pravým tlačítkem na modul a vyberte **Odstranit**. 

![Zvýrazní moduly, které se mají odstranit, aby se odstranil model podpory Vector.](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Náš model by teď měl vypadat nějak takto:

![Výsledný experiment při odstranění modelu podpory vektorového počítače](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nyní jsme připraveni nasadit tento model pomocí [rozřízeného rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod školicího experimentu na prediktivní experiment

Chcete-li získat tento model připravený pro nasazení, je třeba převést tento experiment školení na prediktivní experiment. Zahrnuje tři kroky:

1. Uložte si model, který jste pronaučili, a potom nahraďte naše školicí moduly.
1. Oříznout experiment a odebrat moduly, které byly pro školení nutné.
1. Definujte, kde bude webová služba přijímat vstup a kde vygeneruje výstup.

Můžete to provést ručně, ale naštěstí všechny tři kroky můžete provést kliknutím na **nastavit webovou službu** ve spodní části plátna experimentu (a výběrem možnosti **prediktivní webová služba** ).

> [!TIP]
> Pokud potřebujete další podrobnosti o tom, co se stane, když převedete školicí experiment na prediktivní experiment, přečtěte si téma [Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](deploy-a-machine-learning-web-service.md).

Když kliknete na **nastavit webovou službu**, stane se několik věcí:

* Vycvičený model se převede na jeden modul **trained model** a uloží se do palety modulu nalevo od plátna experimentu (můžete ho najít v části **trained Models**).
* Moduly, které se použily pro školení, se odeberou. určen
  * [Posílený rozhodovací strom se dvěma třídami][two-class-boosted-decision-tree]
  * [Trénování modelu][train-model]
  * [Rozdělení dat][split]
  * druhý modul [spuštění skriptu jazyka R][execute-r-script] , který se použil pro testovací data
* Uložený vycvičený model se přidá zpátky do experimentu.
* Jsou přidány výstupní moduly **webové služby** a **výstupní webové služby** (které určují, kde budou data uživatele zadávat model a jaká data se vrátí, když je webová služba dostupná).

> [!NOTE]
> Na kartách, které byly přidány v horní části plátna experimentu, vidíte, že je experiment uložený ve dvou částech. Původní školicí experiment je pod **experimentem školení** na kartě a nově vytvořený prediktivní experiment je v rámci **prediktivního experimentu**. Prediktivní experiment je ten, který nasadíte jako webovou službu.

v rámci tohoto konkrétního experimentu je třeba provést jeden další krok.
Přidali jste dva moduly [spuštění skriptu R][execute-r-script] , které poskytují funkci váhy dat. To bylo pouze štych, které jste potřebovali pro školení a testování, takže tyto moduly můžete vzít v konečném modelu.
Machine Learning Studio (Classic) odebraly při odebrání [rozděleného][split] modulu jeden modul pro [spuštění skriptu R][execute-r-script] . Nyní můžete odebrat [Editor metadat][metadata-editor] druhý a připojit přímo ke [bodovému modelu][score-model].    

Náš experiment by teď měl vypadat takto:  

![Bodování výukového modelu](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Možná vás zajímá, proč jste v prediktivním experimentu opustili datovou sadu dat platební karty v sadě UCI němčina. Služba bude pohodnocovat data uživatelů, ne původní datovou sadu, takže proč ponechat původní datovou sadu v modelu?
> 
> Je true, že služba nepotřebuje původní data kreditní karty. Ale potřebuje schéma pro tato data, včetně informací o tom, kolik sloupců existují a které sloupce jsou číselné. Tyto informace o schématu jsou nezbytné k interpretaci dat uživatele. Tyto součásti se nechají připojené, takže modul bodování má při spuštění služby schéma datové sady. Data se nepoužívají, stačí pouze ze schématu.  
> 
>Je důležité si uvědomit, že pokud vaše původní datová sada obsahovala popisek, očekává se také očekávané schéma z webového vstupu, které bude mít sloupec s popiskem. Takovým způsobem je odebrání popisku a všech dalších dat, která byla v datové sadě pro školení, ale nebudou ve webových vstupech před připojením datové sady webových vstupů a školení ke společnému modulu. 
> 

Spusťte experiment jednou, a to tak, že kliknete na **Spustit**. Pokud chcete ověřit, že model stále pracuje, klikněte na výstup modulu určení [skóre modelu][score-model] a vyberte **Zobrazit výsledky**. Vidíte, že se zobrazují původní data, spolu s hodnotou úvěrového rizika ("popisky s skóre") a hodnotou pravděpodobnosti bodování ("pravděpodobnosti skóre".) 

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Experiment můžete nasadit jako klasickou webovou službu nebo jako novou webovou službu založenou na Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Nasazení jako klasické webové služby
Pokud chcete nasadit klasickou webovou službu odvozenou z našeho experimentu, klikněte na **nasadit webovou službu** pod plátno a vyberte **nasadit webovou službu [Classic]**. Machine Learning Studio (Classic) nasadí experiment jako webovou službu a přejde na řídicí panel pro tuto webovou službu. Na této stránce se můžete vrátit do experimentu (**Zobrazit snímek** nebo **Zobrazit nejnovější**) a spustit jednoduchý test webové služby (viz **testování webové služby** níže). Zde najdete také informace pro vytváření aplikací, které mají přístup k webové službě (Další informace najdete v dalším kroku tohoto kurzu).

![Řídicí panel webové služby](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Službu můžete nakonfigurovat kliknutím na kartu **Konfigurace** . Tady můžete upravit název služby (ve výchozím nastavení se mu přidělí název experimentu) a pojmenujte ho. Pro vstupní a výstupní data můžete také zadat výstižnější popisky.  

![Konfigurace webové služby](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Nasadit jako novou webovou službu

> [!NOTE] 
> Nasazení nové webové služby vyžaduje dostatečná oprávnění v předplatném, do kterého nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Nasazení nové webové služby odvozené z našeho experimentu:

1. Klikněte na **nasadit webovou službu** pod plátno a vyberte **nasadit webovou službu [New]**. Machine Learning Studio (Classic) vás přenáší na stránku **experimentu nasazení** webové služby Azure Machine Learning Web Services.

1. Zadejte název webové služby. 

1. V případě **cenového plánu** můžete vybrat existující Cenový tarif nebo vybrat vytvořit nové a zadat název nového plánu a vybrat možnost měsíčního plánu. Plány plánu se ve výchozím nastavení naplánují pro výchozí oblast a vaše webová služba se v této oblasti nasadí.

1. Klikněte na **Deploy** (Nasadit).

Po několika minutách se otevře stránka **rychlý Start** pro vaši webovou službu.

Službu můžete nakonfigurovat kliknutím na kartu **Konfigurovat** . Tady můžete upravit název služby a zadat pro něj popis. 

Chcete-li otestovat webovou službu, klikněte na kartu **test** (viz **Test webové služby** níže). Informace o vytváření aplikací, které mají přístup k webové službě, získáte tak, že kliknete na kartu **spotřebování** (další krok v tomto kurzu se zobrazí podrobněji).

> [!TIP]
> Webovou službu můžete po nasazení aktualizovat. Například pokud chcete změnit svůj model, můžete upravit experiment pro školení, upravit parametry modelu a kliknout na **nasadit webovou službu**, vybrat **nasadit webovou službu [Classic]** nebo **nasadit webovou službu [New]**. Když znovu nasadíte experiment, nahradí se tím webová služba, která teď používá aktualizovaný model.  
> 
> 

## <a name="test-the-web-service"></a>Otestování webové služby

Když je k webové službě přistupovaná, data uživatele se doplní přes **Vstupní modul webové služby** , ve kterém se předají do modulu [skóre modelu][score-model] a skóre. Způsob, jakým jste nastavili prediktivní experiment, model očekává data ve stejném formátu jako v původní datové sadě úvěrového rizika.
Výsledky se vrátí uživateli z webové služby přes **výstupní modul webové služby** .

> [!TIP]
> Jak máte nakonfigurované prediktivní experiment, vrátí se celý výsledek z modulu [skóre modelu][score-model] . To zahrnuje všechna vstupní data plus hodnotu úvěrového rizika a pravděpodobnost bodování. Můžete ale vracet něco jiného, pokud chcete – například můžete vracet pouze hodnotu úvěrového rizika. Provedete to tak, že vložíte modul [Vybrat sloupce][select-columns] mezi [modelem skóre][score-model] a **výstupem webové služby** , aby se vyloučily sloupce, které nechcete, aby webová služba vracela. 
> 
> 

Klasickou webovou službu můžete testovat buď v **Machine Learning Studio (Classic)** , nebo na portálu **Azure Machine Learning Web Services** .
Novou webovou službu můžete testovat jenom na portálu **Machine Learning Web Services** .

> [!TIP]
> Při testování na portálu Azure Machine Learning Web Services můžete mít portál vytvořit ukázková data, která můžete použít k otestování služby Request-Response. Na stránce **Konfigurace** vyberte možnost Ano, pokud chcete **ukázková data povolit?**. Když na stránce **test** otevřete kartu Request-Response, na portálu se vyplní ukázková data z původní datové sady úvěrového rizika.

### <a name="test-a-classic-web-service"></a>Testování klasické webové služby

Klasickou webovou službu můžete testovat v Machine Learning Studio (Classic) nebo na portálu Machine Learning Web Services. 

#### <a name="test-in-machine-learning-studio-classic"></a>Test v Machine Learning Studio (klasický)

1. Na stránce **řídicího panelu** webové služby klikněte na tlačítko **test** pod **výchozím koncovým bodem**. Zobrazí se dialogové okno se žádostí o zadání vstupních dat pro službu. Jedná se o stejné sloupce, které se objevily v původní datové sadě úvěrového rizika.  

1. Zadejte sadu dat a pak klikněte na **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testování na portálu Machine Learning Web Services

1. Na stránce **řídicího panelu** webové služby klikněte na odkaz **test Preview** pod **výchozím koncovým bodem**. Stránka test na portálu Azure Machine Learning Web Services pro koncový bod webové služby se otevře a zobrazí dotaz na vstupní data pro danou službu. Jedná se o stejné sloupce, které se objevily v původní datové sadě úvěrového rizika.

2. Klikněte na **testovat požadavek-odpověď**. 

### <a name="test-a-new-web-service"></a>Testování nové webové služby

Novou webovou službu můžete testovat jenom na portálu Machine Learning Web Services.

1. Na portálu [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) klikněte v horní části stránky na **test** . Otevře se stránka **test** a můžete zadat data pro službu. Zobrazená vstupní pole odpovídají sloupcům, které se objevily v původní datové sadě úvěrového rizika. 

1. Zadejte sadu dat a pak klikněte na **testovat požadavek-odpověď**.

Výsledky testu se zobrazí na pravé straně stránky ve sloupci Output (výstup). 


## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby, ať už jde o klasickou nebo novou, ji můžete spravovat z portálu [Microsoft Azure Machine Learning webové služby](https://services.azureml.net/quickstart) .

Chcete-li monitorovat výkon webové služby:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)
1. Klikněte na **webové služby** .
1. Klikněte na svou webovou službu.
1. Klikněte na **řídicí panel** .

## <a name="access-the-web-service"></a>Nastavení přístupu k webové službě

V předchozím kroku tohoto kurzu jste nasadili webovou službu, která využívá model předpovědi úvěrového rizika. Nyní se uživatelé můžou do IT posílat data a získat výsledky. 

Webová služba je webová služba Azure, která může přijímat a vracet data pomocí rozhraní REST API jedním ze dvou způsobů:  

* **Požadavek nebo odpověď** – uživatel odešle do služby jeden nebo více řádků kreditních dat pomocí protokolu HTTP a služba odpoví jednou nebo více sadami výsledků.
* **Dávkové spuštění** – uživatel ukládá jeden nebo více řádků kreditních dat do objektu blob Azure a pak odešle umístění objektu blob do služby. Služba porovnává všechny řádky dat ve vstupním objektu blob, uloží výsledky do jiného objektu BLOB a vrátí adresu URL tohoto kontejneru. 

> [!NOTE]
> Názvy sloupců funkcí v studiu (Classic) rozlišují **velká a malá písmena**. Ujistěte se, že vstupní data pro vyvolání webové služby mají stejné názvy sloupců jako v datové sadě školení.

Další informace o přístupu a používání webové služby najdete v tématu věnovaném [využívání Azure Machine Learning webové služby pomocí šablony webové aplikace](./consume-web-services.md).



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

Můžete také vyvinout vlastní aplikaci pro přístup k webové službě pomocí počátečního kódu, který je k dispozici v programovacích jazycích R, C# a Python.

> [!div class="nextstepaction"]
> [Využívání webové služby Azure Machine Learning](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset