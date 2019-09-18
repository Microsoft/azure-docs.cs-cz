---
title: Použití automatizovaného rozhraní ML Azure ke školení & nasazení modelů
titleSuffix: Azure Machine Learning
description: Vytvářejte, spravujte a nasaďte automatizované experimenty strojového učení na úvodní stránce pracovního prostoru Azure Machine Learning (Preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 3a47977f2589227347582dc6fcaff25120e380d7
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034828"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Vytvářejte, Zkoumejte a nasaďte automatizované experimenty strojového učení s využitím úvodní stránky pracovního prostoru Azure Machine Learning (Preview).

 V tomto článku se dozvíte, jak vytvářet, zkoumat a nasazovat automatizované experimenty strojového učení na úvodní stránce pracovního prostoru Azure Machine Learning bez jediného řádku kódu. Automatizované Machine Learning automatizuje proces výběru nejlepšího algoritmu, který se má použít pro vaše konkrétní data, takže můžete model strojového učení rychle vygenerovat. [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md).

 Pokud dáváte přednost více prostředím založeným na kódu, můžete [v Pythonu nakonfigurovat i automatizované experimenty strojového učení](how-to-configure-auto-train.md) s [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="get-started"></a>Začínáme


1. Přihlaste se na [úvodní stránku pracovního prostoru](https://ml.azure.com/workspaceportal/). 

1. Vyberte své předplatné a pracovní prostor. 

1. Přejděte do levého podokna. V části **vytváření obsahu** vyberte možnost **automatizovaná ml** .

[![Azure Portal navigační podokno](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Pokud se jedná o vaše první experimenty, zobrazí se **obrazovka Začínáme.** 

V opačném případě uvidíte na **automatizovaném** řídicím panelu Machine Learning přehled všech automatických experimentů strojového učení, včetně těch, které se vytvořily pomocí sady SDK. Tady můžete filtrovat a zkoumat své běhy podle data, experimentu a stavu spuštění.

## <a name="create-and-run-experiment"></a>Vytvoření a spuštění experimentu

1. Vyberte **vytvořit experiment** a vyplňte formulář.

1. Zadejte jedinečný název experimentu.

1. Pro úlohu profilace a školení pro data vyberte výpočetní prostředky. V rozevíracím seznamu jsou k dispozici seznam vašich stávajících výpočtů. Pokud chcete vytvořit nový výpočetní výkon, postupujte podle pokynů v kroku 4.

1. Vyberte **vytvořit novou výpočetní** prostředky a nakonfigurujte výpočetní kontext pro tento experiment.

    Pole|Popis
    ---|---
    Název výpočetních prostředků služby Machine Learning| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.
    Další nastavení| *Minimální uzel*: Zadejte minimální počet uzlů pro výpočetní výkon. Minimální počet uzlů pro výpočetní výkon AML je 0. Chcete-li povolit profilaci dat, je nutné mít nejméně jeden uzel. <br> *Maximální počet uzlů*: Zadejte maximální počet uzlů pro výpočetní výkon. Výchozí hodnota je 6 uzlů pro AML Compute.
    
    Vyberte **Vytvořit**. Vytváření nových výpočetních prostředků může trvat několik minut.

    >[!NOTE]
    > Váš název COMPUTE určí, jestli je povolená možnost *profilace*, kterou vybíráte nebo vytváříte. (Další podrobnosti najdete v části [profilace dat](#profile) .)

1. Vyberte datovou sadu z kontejneru úložiště nebo ji vytvořte tak, že do kontejneru nahrajete soubor z místního počítače. Verze Public Preview podporuje pouze místní nahrávání souborů a účty úložiště Azure Blob.

    >[!Important]
    > Požadavky na školicí data:
    >* Data musí být v tabulkovém formátu.
    >* Hodnota, kterou chcete odhadnout (cílový sloupec), musí být k dispozici v datech.

    1. Pokud chcete vytvořit novou datovou sadu ze souboru na vašem místním výpočetním prostředí, vyberte **Procházet** a pak vyberte soubor. 

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. Vyberte možnost **Další** a nahrajte ji do výchozího kontejneru úložiště, který se automaticky vytvoří s vaším pracovním prostorem, nebo zvolte kontejner úložiště, který chcete pro experiment použít. 

    1. Zkontrolujte správnost formuláře **nastavení a náhled** . Formulář se inteligentně vyplní podle typu souboru. 

        Pole| Popis
        ----|----
        Formát souboru| Definuje rozložení a typ dat uložených v souboru.
        Oddělovač| Jeden nebo více znaků pro určení hranice mezi oddělenými a nezávislými oblastmi v prostém textu nebo v jiných datových proudech.
        Kódování| Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.
    
        Vyberte **Další**.

    1. Formulář **schématu** se inteligentně vyplní na základě výběrů ve formuláři **nastavení a náhled** . Tady můžete nakonfigurovat datový typ pro každý sloupec, zkontrolovat názvy sloupců a vybrat, které sloupce nechcete pro svůj experiment **Zahrnout** . 
            
        Vyberte **Další.**

1. Vyberte typ úlohy školení: klasifikace, regrese nebo prognózování.

1. Vybrat cílový sloupec; Toto je sloupec, na který byste chtěli předpovědi.

1. Pro prognózování:
    1. Vyberte sloupec čas: Tento sloupec obsahuje časová data, která se mají použít.

    1. Vybrat horizont předpovědi: Určete, kolik časových jednotek (minuty/hodiny/dny/týdny/měsíce/roky) bude model moci předpovědět budoucímu. Dalším modelem se vyžaduje předpověď do budoucna, tím méně přesné bude. [Přečtěte si další informace o prognózování a horizontu předpovědi](how-to-auto-train-forecast.md).

1. Volitelné Rozšířená nastavení: Další nastavení, která můžete použít k lepšímu řízení úlohy školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat. 

    Pokročilá nastavení|Popis
    ------|------
    Primární metrika| Hlavní metrika použitá pro vyhodnocování modelu. [Přečtěte si další informace o metrikách modelů](how-to-configure-auto-train.md#explore-model-metrics).
    Výstupní kritéria| Pokud je splněno kterékoli z těchto kritérií, úloha školení skončí před úplným dokončením. <br> *Doba úlohy školení (minuty)* : Doba, po kterou je možné spustit úlohu školení.  <br> *Maximální počet iterací*: Maximální počet kanálů (iterací), které se mají testovat v úloze školení Úloha nebude spouštět více než zadaný počet iterací. <br> *Prahová hodnota skóre metriky*:  Minimální skóre metriky pro všechny kanály. Tím zajistíte, že pokud máte definovanou cílovou metriku, která má být dostupná, nebudete věnovat více času školicím úlohám, než je potřeba.
    Předzpracování| Tuto možnost vyberte, pokud chcete povolit nebo zakázat předzpracování prováděné automatizovaným strojovým učením. Předzpracování zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce. [Přečtěte si další informace o předzpracování](#preprocess).
    Ověřování| Vyberte jednu z možností vzájemného ověření, kterou chcete použít v úloze školení. [Další informace o vzájemném ověřování](how-to-configure-auto-train.md).
    Souběžnost| Vyberte omezení více jader, která chcete použít při použití COMPUTE s více jádry.
    Blokovaný algoritmus| Vyberte algoritmy, které chcete vyloučit z úlohy školení.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiky profilace dat & souhrnu

V rámci datové sady můžete získat velké množství různých souhrnných statistik, abyste ověřili, jestli je vaše datová sada připravená na ML. Pro nečíselné sloupce obsahují jenom základní statistiky, jako je min, Max a počet chyb. Pro číselné sloupce si můžete prohlédnout i jejich statistické momenty a odhadované quantiles. Konkrétně náš datový profil zahrnuje:

>[!NOTE]
> Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

Statistický|Popis
------|------
Funkce| Název sloupce, který je sumarizován.
Profil| Vložená vizualizace na základě typu odvozeného. Například řetězce, logické hodnoty a data budou mít počty hodnot, zatímco desetinná místa (číslice) mají přibližné histogramy. To vám umožní získat rychlé porozumění distribuci dat.
Distribuce typu| Počet vložené hodnoty typů v rámci sloupce. Hodnoty null jsou jejich vlastní typ, takže tato vizualizace je užitečná pro zjištění lichých nebo chybějících hodnot.
type|Odvozený typ sloupce. Možné hodnoty jsou: řetězce, logické hodnoty, kalendářní data a desetinná místa.
Minimum| Minimální hodnota sloupce Pro funkce, jejichž typ nemá základní řazení (např. logické hodnoty), se zobrazí prázdné položky.
Maximum| Maximální hodnota sloupce 
Count| Celkový počet chybějících a nechybějících položek ve sloupci
Počet nechybějících| Počet položek ve sloupci, které nebyly nalezeny. Prázdné řetězce a chyby jsou považovány za hodnoty, takže nebudou přispívat k "nechybějícímu počtu".
Quantiles| Přibližné hodnoty na jednotlivých Quantile, které poskytují smysl distribuce dat.
střední hodnotu| Aritmetický průměr nebo průměr sloupce
Standardní odchylka| Měření množství rozptýlení nebo variace dat tohoto sloupce.
Odchylka| Měření, jak daleko rozprostření dat tohoto sloupce z průměrné hodnoty. 
Zešikmení| Měření, jak se liší data tohoto sloupce od normálního rozdělení.
Míra fluktuace| Měření, jak často se data tohoto sloupce v porovnání s normální distribucí.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Rozšířené možnosti předběžného zpracování

Při konfiguraci experimentů můžete povolit rozšířené nastavení `Preprocess`. To znamená, že následující kroky předběžného zpracování dat a featurization se provádí automaticky.

|&nbsp;Kroky předběžného zpracování| Popis |
| ------------- | ------------- |
|Velkou mohutností či žádné funkce odchylka|Odřaďte je ze školicích a ověřovacích sad, včetně funkcí se všemi chybějícími hodnotami, stejné hodnoty ve všech řádcích nebo s extrémně vysokou mohutnou (například hodnoty hash, ID nebo identifikátory GUID).|
|Dává chybějící hodnoty|Pro numerické funkce, imputac s průměrem hodnot ve sloupci.<br/><br/>V případě funkcí kategorií se imputac s nejčastěji hodnotou.|
|Generovat další funkce|Pro funkce DateTime: Rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodina, minuta, sekunda.<br/><br/>Pro funkce textu: Četnost termínů založená na unigrams, mezigramech a Tri-Character-gramech.|
|Transformace a kódování |Číselné funkce s malým počtem jedinečných hodnot jsou transformovány do funkcí kategorií.<br/><br/>Pro nízkou mohutnost se provádí kódování s jedním aktivním kategorií; v případě vysoké mohutnosti je kódování One-Hot-hash.|
|Vkládání slov|Featurizer textu, který převede vektory textových tokenů na vektory vět pomocí předem připraveného modelu. Vektory vložení každého slova v dokumentu jsou agregovány dohromady, aby se vytvořil vektor funkce dokumentu.|
|Cílová kódování|V případě funkcí kategorií mapuje každou kategorii s průměrnou cílovou hodnotou pro regresní problémy a pravděpodobností třídy pro jednotlivé třídy pro problémy s klasifikací. Pro snížení velikosti mapování a hluku způsobených kategoriemi zhuštěných dat se použije vyvážení na základě frekvencí a k přeložení.|
|Kódování cílového textu|V případě textového vstupu se pro generování pravděpodobnosti každé třídy používá skládaný lineární model s použitím typu penalta-slova.|
|Váha důkazů (WoE)|Vypočítá WoE jako míru korelace kategorií sloupců do cílového sloupce. Počítá se jako protokol poměru mezi třídou a pravděpodobnosti mimo třídu. Tento krok provede výstup jednoho sloupce číselné funkce na každou třídu a odstraní nutnost explicitně imputace chybějících hodnot a izolované zpracování.|
|Vzdálenost clusteru|Vlaky a k – znamenají model clusteringu ve všech číselných sloupcích.  Výstupy k novým funkcím, jedna nová číselná funkce na cluster, která obsahuje vzdálenost jednotlivých vzorků k těžiště každého clusteru.|

## <a name="run-experiment-and-view-results"></a>Spuštění experimentu a zobrazení výsledků

Vyberte **začít** a spusťte experiment. Proces přípravy procesu trvá několik minut.

### <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Po dokončení fáze přípravy experimentu se zobrazí obrazovka s podrobnostmi o spuštění, která začne naplnit. Tato obrazovka vám poskytne úplný seznam vytvořených modelů. Model, který vychází z nejvyšší úrovně podle zvolené metriky, je ve výchozím nastavení v horní části seznamu. Když se úloha školení pokusí o další modely, přidají se do seznamu iterací a do grafu. Pomocí grafu iterace získáte rychlé porovnání metrik pro modely, které byly doposud vyprodukovány.

Školicí úlohy můžou nějakou dobu trvat, než se dokončí spuštění každého kanálu.

[![Spustit řídicí panel podrobností](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Zobrazit podrobnosti o školicím běhu

Projděte si podrobné informace o všech výstupních modelech a podívejte se na podrobnosti o školicích běhůch, jako jsou metriky výkonu a distribuční grafy. [Přečtěte si další informace o grafech](how-to-understand-automated-ml.md).

[![Podrobnosti iterace](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Nasazení modelu

Jakmile budete mít nejlepší model na ruce, je čas ho nasadit jako webovou službu pro předpověď na nová data.

Automatizované ML vám pomůže s nasazením modelu bez psaní kódu:

1. Máte několik možností nasazení. 

    + Možnost 1: Pokud chcete nasadit nejlepší model (podle definovaných kritérií metriky), vyberte nasadit nejlepší model ze stránky podrobností o spuštění.

    + Možnost 2: Chcete-li nasadit konkrétní model iterace z tohoto experimentu, přejděte k podrobnostem v modelu a otevřete stránku s podrobnostmi o spuštění a vyberte nasadit model.

1. Naplňte podokno **nasazení modelu** .

    Pole| Value
    ----|----
    Název nasazení| Zadejte jedinečný název pro vaše nasazení.
    Popis nasazení| Zadejte popis, který bude lépe identifikovat, pro které nasazení probíhá.
    Skript bodování| Vygeneruje nebo nahraje vlastní soubor bodování. [Další informace o hodnoticím skriptu](how-to-deploy-and-where.md#script)
    Skript prostředí| Vygeneruje nebo nahraje vlastní soubor prostředí.
    >[!Important]
    > Názvy souborů musí být pod 32 znaků a musí začínat a končit alfanumerickými znaky. Může obsahovat pomlčky, podtržítka, tečky a alfanumerické znaky mezi. Mezery nejsou povoleny.

1. Vyberte **Nasadit**. Dokončení nasazení může trvat přibližně 20 minut.

    Po úspěšném dokončení nasazení se zobrazí následující zpráva.

    ![Nasazení dokončeno](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Nyní máte provozní webovou službu, která generuje předpovědi.

## <a name="next-steps"></a>Další postup

* Vyzkoušejte si kompletní [kurz tvorby prvního automatizovaného experimentu v Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md) a Azure Machine Learning.
* [Pochopte automatizované výsledky strojového učení](how-to-understand-automated-ml.md).
* [Naučte se využívat webovou službu](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
