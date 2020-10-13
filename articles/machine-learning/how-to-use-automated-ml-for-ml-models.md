---
title: Použití AutoML k vytvoření modelů & nasazení
titleSuffix: Azure Machine Learning
description: Vytvářejte, kontrolujte a nasaďte automatizované modely strojového učení pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 47df04a0195c4cfcc4e40db5bf21387a284f682c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362244"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Vytvářejte, kontrolujte a nasaďte automatizované modely strojového učení pomocí Azure Machine Learning


V tomto článku se naučíte, jak vytvářet, zkoumat a nasazovat automatizované modely strojového učení bez jediného řádku kódu v Azure Machine Learning Studiu.

Automatizované Machine Learning je proces, ve kterém se pro vás vybere nejlepší algoritmus strojového učení, který se má použít pro vaše konkrétní data. Tento proces vám umožní rychle vygenerovat modely strojového učení. [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md).
 
Příklad koncového na konci najdete v [kurzu Vytvoření modelu klasifikace pomocí automatizovaného rozhraní ML Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

V případě prostředí Pythonu založeného na kódu můžete pomocí sady Azure Machine Learning SDK [nakonfigurovat experimenty automatizovaného strojového učení](how-to-configure-auto-train.md) .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md). 

## <a name="get-started"></a>Začínáme

1. Přihlaste se k Azure Machine Learning na https://ml.azure.com . 

1. Vyberte své předplatné a pracovní prostor. 

1. Přejděte do levého podokna. V části **Autor** vyberte **automatizovaná ml** .

[![Navigační podokno Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Pokud se jedná o vaše první experimenty, zobrazí se prázdný seznam a odkazy na dokumentaci. 

V opačném případě se zobrazí seznam nedávných automatizovaných experimentů strojového učení, včetně těch, které jsou vytvořené pomocí sady SDK. 

## <a name="create-and-run-experiment"></a>Vytvoření a spuštění experimentu

1. Vyberte **+ Nový Automated ml Run** a naplňte formulář.

1. Vyberte datovou sadu z kontejneru úložiště nebo vytvořte novou datovou sadu. Datové sady je možné vytvořit z místních souborů, adres URL webu, úložiště dat nebo otevřených datových sad Azure. Přečtěte si další informace o [vytváření datových sad](how-to-create-register-datasets.md).  

    >[!Important]
    > Požadavky na školicí data:
    >* Data musí být v tabulkovém formátu.
    >* Hodnota, kterou chcete odhadnout (cílový sloupec), musí být k dispozici v datech.

    1. Pokud chcete vytvořit novou datovou sadu ze souboru v místním počítači, vyberte **+ vytvořit datovou sadu** a pak vyberte **z místního souboru**. 

    1. Ve formuláři **základní informace** zadejte jedinečný název datové sady a zadejte volitelný popis. 

    1. Výběrem **Další** otevřete **formulář úložiště dat a výběr souboru**. V tomto formuláři vyberte, kam chcete datovou sadu nahrát; výchozí kontejner úložiště, který se automaticky vytvoří s vaším pracovním prostorem, nebo vyberte kontejner úložiště, který chcete pro experiment použít. 
    
        1. Pokud jsou vaše data za virtuální sítí, musíte povolit funkci **přeskočení ověřování** , aby se zajistilo, že pracovní prostor bude mít přístup k vašim datům. Další informace najdete v tématu [použití Azure Machine Learning studia ve službě Azure Virtual Network](how-to-enable-studio-virtual-network.md). 
    
    1. Vyberte **Procházet** a nahrajte datový soubor pro datovou sadu. 

    1. Zkontrolujte správnost formuláře **nastavení a náhled** . Formulář se inteligentně vyplní podle typu souboru. 

        Pole| Description
        ----|----
        Formát souboru| Definuje rozložení a typ dat uložených v souboru.
        Oddělovač| Jeden nebo více znaků pro určení hranice mezi oddělenými a nezávislými oblastmi v prostém textu nebo v jiných datových proudech.
        Encoding| Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.
    
        Vyberte **Další**.

    1. Formulář **schématu** se inteligentně vyplní na základě výběrů ve formuláři **nastavení a náhled** . Tady můžete nakonfigurovat datový typ pro každý sloupec, zkontrolovat názvy sloupců a vybrat, které sloupce nechcete pro svůj experiment **Zahrnout** . 
            
        Vyberte **Další.**

    1. Formulář **potvrdit podrobnosti** je souhrn informací, které byly dříve vyplněny ve formulářích **základní informace** a **nastavení a náhled** . Máte také možnost vytvořit datový profil pro datovou sadu pomocí výpočetní služby s povoleným profilací. Přečtěte si další informace o [profilování dat](how-to-connect-data-ui.md#profile).

        Vyberte **Další**.
1. Jakmile se zobrazí, vyberte nově vytvořenou datovou sadu. Můžete si také zobrazit náhled datové sady a vzorových statistik. 

1. Ve formuláři **Konfigurace spuštění** zadejte jedinečný název experimentu.

1. Vyberte cílový sloupec; Toto je sloupec, na který byste chtěli předpovědi.

1. Pro úlohu profilace a školení pro data vyberte výpočetní prostředky. V rozevíracím seznamu jsou k dispozici seznam vašich stávajících výpočtů. Pokud chcete vytvořit nový výpočetní výkon, postupujte podle pokynů v kroku 7.

1. Vyberte **vytvořit novou výpočetní** prostředky a nakonfigurujte výpočetní kontext pro tento experiment.

    Pole|Description
    ---|---
    Název výpočetních prostředků| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Priorita virtuálního počítače| Virtuální počítače s nízkou prioritou jsou levnější, ale nezaručují výpočetní uzly. 
    Typ virtuálního počítače| Vyberte procesor nebo GPU pro typ virtuálního počítače.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.
    Minimální/maximální počet uzlů| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů. Zadejte maximální počet uzlů pro výpočetní výkon. Výchozí hodnota je 6 uzlů pro AML Compute.
    Rozšířená nastavení | Tato nastavení umožňují nakonfigurovat uživatelský účet a stávající virtuální síť pro svůj experiment. 
    
    Vyberte **Vytvořit**. Vytváření nových výpočetních prostředků může trvat několik minut.

    >[!NOTE]
    > Váš název COMPUTE určí, jestli je *povolená možnost profilace*, kterou vybíráte nebo vytváříte. (Další podrobnosti najdete v části [profilace dat](how-to-connect-data-ui.md#profile) .)

    Vyberte **Další**.

1. Ve formuláři **typ úlohy a nastavení** vyberte typ úkolu: klasifikace, regrese nebo prognózování. Další informace najdete v tématu [podporované typy úloh](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) .

    1. V případě **klasifikace**můžete také povolit hloubkové učení.
    
        Pokud je možnost hloubkového učení povolená, je ověřování omezené na _train_validation rozdělení_. [Přečtěte si další informace o možnostech ověřování](how-to-configure-cross-validation-data-splits.md).


    1. Pro **Prognózování** můžete, 
    
        1. Povolte hloubkové učení.
    
        1. Výběr *sloupce pro čas*: Tento sloupec obsahuje časová data, která se mají použít.

        1. Vybrat *horizont předpovědi*: Určete, kolik časových jednotek (minuty/hodiny/dny/týdny/měsíce/roky) bude model moci předpovědět do budoucna. Dalším modelem se vyžaduje předpověď do budoucna, tím méně přesné bude. [Přečtěte si další informace o prognózování a horizontu předpovědi](how-to-auto-train-forecast.md).

1. Volitelné Zobrazit nastavení konfigurace Přidání: Další nastavení, která můžete použít k lepšímu řízení úlohy školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat. 

    Další konfigurace|Description
    ------|------
    Primární metrika| Hlavní metrika použitá pro vyhodnocování modelu. [Přečtěte si další informace o metrikách modelů](how-to-configure-auto-train.md#primary-metric).
    Vysvětlete nejlepší model | Tuto možnost vyberte, pokud chcete povolit nebo zakázat, aby se zobrazila vysvětlení doporučeného nejlepšího modelu. <br> Tato funkce není aktuálně k dispozici pro [určité algoritmy prognózy](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Blokovaný algoritmus| Vyberte algoritmy, které chcete vyloučit z úlohy školení. <br><br> Povolení algoritmů je dostupné jenom pro [experimenty sady SDK](how-to-configure-auto-train.md#supported-models). <br> Podívejte se na [podporované modely pro každý typ úkolu](https://docs.microsoft.com/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels?view=azure-ml-py&preserve-view=true).
    Výstupní kritérium| Při splnění kteréhokoli z těchto kritérií se školicí úloha zastaví. <br> *Čas úlohy školení (hodiny)*: dobu, po kterou je možné spustit úlohu školení. <br> *Prahová hodnota skóre metriky*: minimální skóre metriky pro všechny kanály. Tím zajistíte, že pokud máte definovanou cílovou metriku, která má být dostupná, nebudete věnovat více času školicím úlohám, než je potřeba.
    Ověřování| Vyberte jednu z možností vzájemného ověření, kterou chcete použít v úloze školení. <br> [Další informace o vzájemném ověřování](how-to-configure-cross-validation-data-splits.md#prerequisites).<br> <br>Prognózování podporuje pouze k přeložení pro křížové ověření.
    Souběžnost| *Maximální počet souběžných iterací*: maximální počet kanálů (iterací), které se mají testovat v úloze školení. Úloha nebude spouštět více než zadaný počet iterací.

1. Volitelné Zobrazit nastavení featurization: Pokud se rozhodnete povolit **Automatické featurization** ve formuláři **Další nastavení konfigurace** , uplatní se výchozí techniky featurization. V **Nastavení zobrazení featurization** můžete změnit tyto výchozí hodnoty a odpovídajícím způsobem je přizpůsobit. Přečtěte si, jak [přizpůsobit featurizations](#customize-featurization). 

    ![Snímek obrazovky se zobrazí v dialogovém okně Vybrat typ úlohy s nastavením zobrazit featurization s názvem.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Přizpůsobení featurization

Ve formuláři **Featurization** můžete povolit nebo zakázat automatické Featurization a přizpůsobit automatické nastavení Featurization pro svůj experiment. Chcete-li otevřít tento formulář, přečtěte si krok 10 v oddílu [Vytvoření a spuštění experimentu](#create-and-run-experiment) . 

Následující tabulka shrnuje vlastní nastavení, která jsou aktuálně k dispozici prostřednictvím studia. 

Sloupec| Přizpůsobení
---|---
Zahrnuje | Určuje, které sloupce se mají zahrnout do školení.
Typ funkce| Změní typ hodnoty pro vybraný sloupec.
Imputace s| Vyberte, která hodnota má ve vašich datech imputace chybějící hodnoty.

![Azure Machine Learning studia Custom featurization](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Spuštění experimentu a zobrazení výsledků

Vyberte **Dokončit** pro spuštění experimentu. Proces přípravy experimentu může trvat až 10 minut. U každého kanálu může další 2 až 3 minuty trvat, než se dokončí úlohy trénování.

### <a name="view-experiment-details"></a>Zobrazení podrobností o experimentu

Otevře se obrazovka s **podrobnostmi o spuštění** na kartě **Podrobnosti** . Na této obrazovce se zobrazí souhrn experimentu, včetně stavového řádku v horní části vedle čísla běhu. 

Na kartě **Modely** je seznam vytvořených modelů seřazený podle skóre metriky. Ve výchozím nastavení se na prvním místě seznamu zobrazí model, který na základě zvolené metriky získá nejvyšší skóre. Když trénovací úloha vyzkouší další modely, přidají se do seznamu. Tady můžete rychle porovnat metriky pro zatím vytvořené modely.

[![Spustit řídicí panel podrobností](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Zobrazit podrobnosti o školicím běhu

Podrobnější informace o všech dokončených modelech získáte v podrobnostech o školicích běhůch, jako je například souhrn modelu na kartě **model** nebo grafy metriky výkonu na kartě **metriky** . další [informace o grafech](how-to-understand-automated-ml.md).

[![Podrobnosti iterace](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Nasazení modelu

Jakmile budete mít nejlepší model, je čas ho nasadit jako webovou službu, která bude vytvářet předpovědi na základě nových dat.

Automatizované strojové učení pomáhá s nasazením modelu bez psaní kódu:

1. Máte několik možností nasazení. 

    + Možnost 1: nasaďte nejlepší model podle definovaných kritérií metriky. 
        1. Po dokončení experimentu přejděte na nadřazenou stránku spuštění výběrem možnosti **Spustit 1** v horní části obrazovky. 
        1.  Vyberte model uvedený v části **nejlepší model souhrnu** . 
        1. V levém horním rohu okna vyberte **nasadit** . 

    + Možnost 2: Pokud chcete z tohoto experimentu nasadit konkrétní iteraci modelu.
        1. Na kartě **Modely** vyberte požadovaný model.
        1. V levém horním rohu okna vyberte **nasadit** .

1. Naplňte podokno **nasazení modelu** .

    Pole| Hodnota
    ----|----
    Název| Zadejte jedinečný název pro vaše nasazení.
    Description| Zadejte popis, který bude lépe identifikovat, pro které nasazení probíhá.
    Typ výpočetních prostředků| Vyberte typ koncového bodu, který chcete nasadit: *Azure Kubernetes Service (AKS)* nebo *Azure Container instance (ACI)*.
    Název výpočetních prostředků| *Platí jenom pro AKS:* Vyberte název clusteru AKS, do kterého chcete nasadit.
    Povolit ověřování | Tuto možnost vyberte, pokud chcete povolení ověřování na základě tokenu nebo klíče.
    Použití vlastních prostředků nasazení| Tuto funkci povolte, pokud chcete nahrát vlastní skript bodování a soubor prostředí. [Další informace o hodnoticích skriptech](how-to-deploy-and-where.md)

    >[!Important]
    > Názvy souborů musí být pod 32 znaků a musí začínat a končit alfanumerickými znaky. Jinak můžou obsahovat pomlčky, podtržítka, tečky a alfanumerické znaky. Mezery nejsou povolené.

    Nabídka *Upřesnit* nabízí výchozí funkce nasazení, jako jsou nastavení [shromažďování dat](how-to-enable-app-insights.md) nebo využití prostředků. Pokud chcete tato výchozí nastavení přepsat, můžete to udělat v této nabídce.

1. Vyberte **Nasadit**. Dokončení nasazení může trvat přibližně 20 minut.
    Po zahájení nasazení se zobrazí karta **Shrnutí modelu**. Průběh nasazení můžete sledovat v části **Stav nasazení**. 

Teď máte funkční webovou službu pro generování předpovědí. Předpovědi můžete otestovat dotazováním služby s využitím [integrované podpory služby Azure Machine Learning v Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Další kroky

* [Naučte se využívat webovou službu](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Pochopte automatizované výsledky strojového učení](how-to-understand-automated-ml.md).
* [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md) a Azure Machine Learning.
