---
title: Sestavení & nasazení automatizovaných modelů ML
titleSuffix: Azure Machine Learning
description: Vytvářejte, spravujte a nasaďte automatizované experimenty strojového učení v Azure Machine Learning Studiu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: a2bf15c8778a6ff549284b1053cf0978d182b802
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355309"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Vytvářejte, Zkoumejte a nasaďte automatizované experimenty strojového učení pomocí Azure Machine Learning studia
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 V tomto článku se naučíte, jak vytvářet, zkoumat a nasazovat automatizované experimenty strojového učení v Azure Machine Learning Studiu bez jediného řádku kódu. Automatizované Machine Learning automatizuje proces výběru nejlepšího algoritmu, který se má použít pro vaše konkrétní data, takže můžete model strojového učení rychle vygenerovat. [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md).

 Pokud dáváte přednost více prostředím založeným na kódu, můžete [v Pythonu nakonfigurovat i automatizované experimenty strojového učení](how-to-configure-auto-train.md) s [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Azure Machine Learning pracovní prostor s typem edice **Enterprise**. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).  Pokud chcete upgradovat stávající pracovní prostor na edici Enterprise, přečtěte si téma [upgrade na Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Začínáme

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com). 

1. Vyberte své předplatné a pracovní prostor. 

1. Přejděte do levého podokna. V části **Autor** vyberte **automatizovaná ml** .

[navigační podokno ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Pokud se jedná o vaše první experimenty, zobrazí se prázdný seznam a odkazy na dokumentaci. 

V opačném případě se zobrazí seznam nedávných automatizovaných experimentů strojového učení, včetně těch, které jsou vytvořené pomocí sady SDK. 

## <a name="create-and-run-experiment"></a>Vytvoření a spuštění experimentu

1. Vyberte **+ Nový Automated ml Run** a naplňte formulář.

1. Vyberte datovou sadu z kontejneru úložiště nebo vytvořte novou datovou sadu. Datové sady je možné vytvořit z místních souborů, adres URL webu, úložiště dat nebo otevřených datových sad Azure. 

    >[!Important]
    > Požadavky na školicí data:
    >* Data musí být v tabulkovém formátu.
    >* Hodnota, kterou chcete odhadnout (cílový sloupec), musí být k dispozici v datech.

    1. Pokud chcete vytvořit novou datovou sadu ze souboru na místním počítači, vyberte **Procházet** a pak vyberte soubor. 

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. Výběrem **Další** otevřete **formulář úložiště dat a výběr souboru**. V tomto formuláři vyberte, kam chcete datovou sadu nahrát; výchozí kontejner úložiště, který se automaticky vytvoří s vaším pracovním prostorem, nebo vyberte kontejner úložiště, který chcete pro experiment použít. 

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

    1. Formulář **potvrdit podrobnosti** je souhrn informací, které byly dříve vyplněny ve formulářích **základní informace** a **nastavení a náhled** . Máte také možnost vytvořit datový profil pro datovou sadu pomocí výpočetní služby s povoleným profilací. Přečtěte si další informace o [profilování dat](#profile).

        Vyberte **Další**.
1. Jakmile se zobrazí, vyberte nově vytvořenou datovou sadu. Můžete si také zobrazit náhled datové sady a vzorových statistik. 

1. Ve formuláři **Konfigurace spuštění** zadejte jedinečný název experimentu.

1. Vyberte cílový sloupec; Toto je sloupec, na který byste chtěli předpovědi.

1. Pro úlohu profilace a školení pro data vyberte výpočetní prostředky. V rozevíracím seznamu jsou k dispozici seznam vašich stávajících výpočtů. Pokud chcete vytvořit nový výpočetní výkon, postupujte podle pokynů v kroku 7.

1. Vyberte **vytvořit novou výpočetní** prostředky a nakonfigurujte výpočetní kontext pro tento experiment.

    Pole|Popis
    ---|---
    Název výpočtu| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.
    Minimální/maximální počet uzlů (v rozšířených nastaveních)| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů. Zadejte maximální počet uzlů pro výpočetní výkon. Výchozí hodnota je 6 uzlů pro AML Compute.
    
    Vyberte **Vytvořit**. Vytváření nových výpočetních prostředků může trvat několik minut.

    >[!NOTE]
    > Váš název COMPUTE určí, jestli je *povolená možnost profilace*, kterou vybíráte nebo vytváříte. (Další podrobnosti najdete v části [profilace dat](#profile) .)

    Vyberte **Další**.

1. Ve formuláři **typ úlohy a nastavení** vyberte typ úkolu: klasifikace, regrese nebo prognózování. Další informace najdete v tématu [Definování typů úloh](how-to-define-task-type.md) .

    1. V případě klasifikace můžete také povolit obsáhlý Learning, který se používá pro text featurizations.

    1. Pro prognózování:
        1. Výběr sloupce pro čas: Tento sloupec obsahuje časová data, která se mají použít.

        1. Vybrat horizont předpovědi: Určete, kolik časových jednotek (minuty/hodiny/dny/týdny/měsíce/roky) bude model moci předpovědět do budoucna. Dalším modelem se vyžaduje předpověď do budoucna, tím méně přesné bude. [Přečtěte si další informace o prognózování a horizontu předpovědi](how-to-auto-train-forecast.md).

1. Volitelné Zobrazit nastavení konfigurace Přidání: Další nastavení, která můžete použít k lepšímu řízení úlohy školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat. 

    Další konfigurace|Popis
    ------|------
    Primární metrika| Hlavní metrika použitá pro vyhodnocování modelu. [Přečtěte si další informace o metrikách modelů](how-to-configure-auto-train.md#explore-model-metrics).
    Automaticky featurization| Tuto možnost vyberte, pokud chcete povolit nebo zakázat předzpracování prováděné automatizovaným strojovým učením. Předzpracování zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce. Není podporováno pro typ úkolu prognózy časových řad. [Přečtěte si další informace o předzpracování](#featurization). 
    Vysvětlete nejlepší model | Pokud chcete zobrazit vysvětlení doporučeného nejlepšího modelu, vyberte možnost povolit nebo zakázat.
    Blokovaný algoritmus| Vyberte algoritmy, které chcete vyloučit z úlohy školení.
    Výstupní kritérium| Při splnění kteréhokoli z těchto kritérií se školicí úloha zastaví. <br> *Čas úlohy školení (hodiny)* : dobu, po kterou je možné spustit úlohu školení. <br> *Prahová hodnota skóre metriky*: minimální skóre metriky pro všechny kanály. Tím zajistíte, že pokud máte definovanou cílovou metriku, která má být dostupná, nebudete věnovat více času školicím úlohám, než je potřeba.
    Ověření| Vyberte jednu z možností vzájemného ověření, kterou chcete použít v úloze školení. [Další informace o vzájemném ověřování](how-to-configure-auto-train.md).
    Souběžnost| *Maximální počet souběžných iterací*: maximální počet kanálů (iterací), které se mají testovat v úloze školení. Úloha nebude spouštět více než zadaný počet iterací.

1. Volitelné Zobrazit nastavení featurization: Pokud se rozhodnete povolit **Automatické featurization** ve formuláři **Další nastavení konfigurace** , v tomto formuláři budete určovat, které sloupce mají tyto featurizationsy provádět, a vybrat, která statistická hodnota se má použít pro imputace chybějících hodnot.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiky profilace dat & souhrnu

V rámci datové sady můžete získat velké množství různých souhrnných statistik, abyste ověřili, jestli je vaše datová sada připravená na ML. Pro nečíselné sloupce obsahují jenom základní statistiky, jako je min, Max a počet chyb. Pro číselné sloupce si můžete prohlédnout i jejich statistické momenty a odhadované quantiles. Konkrétně náš datový profil zahrnuje:

>[!NOTE]
> Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

Údaj|Popis
------|------
Funkce| Název sloupce, který je sumarizován.
Profil| Vložená vizualizace na základě typu odvozeného. Například řetězce, logické hodnoty a data budou mít počty hodnot, zatímco desetinná místa (číslice) mají přibližné histogramy. To vám umožní získat rychlé porozumění distribuci dat.
Distribuce typu| Počet vložené hodnoty typů v rámci sloupce. Hodnoty null jsou jejich vlastní typ, takže tato vizualizace je užitečná pro zjištění lichých nebo chybějících hodnot.
Typ|Odvozený typ sloupce. Možné hodnoty jsou: řetězce, logické hodnoty, kalendářní data a desetinná místa.
Minimum| Minimální hodnota sloupce Pro funkce, jejichž typ nemá základní řazení (např. logické hodnoty), se zobrazí prázdné položky.
Maximum| Maximální hodnota sloupce 
Počet| Celkový počet chybějících a nechybějících položek ve sloupci
Počet nechybějících| Počet položek ve sloupci, které nebyly nalezeny. Prázdné řetězce a chyby jsou považovány za hodnoty, takže nebudou přispívat k "nechybějícímu počtu".
Quantiles| Přibližné hodnoty na jednotlivých Quantile, které poskytují smysl distribuce dat.
střední hodnotu| Aritmetický průměr nebo průměr sloupce
Směrodatná odchylka| Měření množství rozptýlení nebo variace dat tohoto sloupce.
Odchylka| Měření, jak daleko rozprostření dat tohoto sloupce z průměrné hodnoty. 
Zešikmení| Měření, jak se liší data tohoto sloupce od normálního rozdělení.
Míra fluktuace| Měření, jak často se data tohoto sloupce v porovnání s normální distribucí.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Rozšířené možnosti featurization

Automatizované Machine Learning nabízí automatické zpracování a guardrails dat, které vám pomůžou identifikovat a spravovat potenciální problémy s daty. 

### <a name="preprocessing"></a>Předzpracování

|Předzpracování&nbsp;kroků| Popis |
| ------------- | ------------- |
|Velkou mohutností či žádné funkce odchylka|Odřaďte je ze školicích a ověřovacích sad, včetně funkcí se všemi chybějícími hodnotami, stejné hodnoty ve všech řádcích nebo s extrémně vysokou mohutnou (například hodnoty hash, ID nebo identifikátory GUID).|
|Dává chybějící hodnoty|Pro numerické funkce, imputac s průměrem hodnot ve sloupci.<br/><br/>V případě funkcí kategorií se imputac s nejčastěji hodnotou.|
|Generovat další funkce|Pro funkce data a času: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodinu, minutu, sekundu.<br/><br/>Pro funkce textu: četnost termínů založená na unigrams, g-gramech a Tri-Character-gramech.|
|Transformace a kódování |Číselné funkce s malým počtem jedinečných hodnot jsou transformovány do funkcí kategorií.<br/><br/>Pro nízkou mohutnost se provádí kódování s jedním aktivním kategorií; v případě vysoké mohutnosti je kódování One-Hot-hash.|
|Vkládání slov|Featurizer textu, který převede vektory textových tokenů na vektory vět pomocí předem připraveného modelu. Vektory vložení každého slova v dokumentu jsou agregovány dohromady, aby se vytvořil vektor funkce dokumentu.|
|Cílová kódování|V případě funkcí kategorií mapuje každou kategorii s průměrnou cílovou hodnotou pro regresní problémy a pravděpodobností třídy pro jednotlivé třídy pro problémy s klasifikací. Pro snížení velikosti mapování a hluku způsobených kategoriemi zhuštěných dat se použije vyvážení na základě frekvencí a k přeložení.|
|Kódování cílového textu|V případě textového vstupu se pro generování pravděpodobnosti každé třídy používá skládaný lineární model s použitím typu penalta-slova.|
|Váha důkazů (WoE)|Vypočítá WoE jako míru korelace kategorií sloupců do cílového sloupce. Počítá se jako protokol poměru mezi třídou a pravděpodobnosti mimo třídu. Tento krok provede výstup jednoho sloupce číselné funkce na každou třídu a odstraní nutnost explicitně imputace chybějících hodnot a izolované zpracování.|
|Vzdálenost clusteru|Vlaky a k – znamenají model clusteringu ve všech číselných sloupcích.  Výstupy k novým funkcím, jedna nová číselná funkce na cluster, která obsahuje vzdálenost jednotlivých vzorků k těžiště každého clusteru.|

### <a name="data-guardrails"></a>Guardrails dat

Data guardrails se automaticky aplikují, aby vám pomohly identifikovat možné problémy s daty (např. chybějící hodnoty, nerovnováha tříd) a pomáhat s nimi opravitelné akce pro lepší výsledky. K dispozici je celá řada osvědčených postupů, které je možné použít k dosažení spolehlivých výsledků. 

V následující tabulce jsou popsány aktuálně podporované datové guardrails a související stavy, které mohou uživatelé pocházet při odesílání jejich experimentů.

Guardrail|Status|Podmínka&nbsp;pro aktivační událost&nbsp;
---|---|---
Chybějící hodnoty&nbsp;&nbsp;imputace |**Předaný** <br> <br> **Určí**|    Žádná chybějící hodnota v žádném ze vstupních&nbsp;sloupců <br> <br> U některých sloupců chybí hodnoty.
Křížové ověření|**Hotovo**|Pokud není zadaná žádná explicitní ověřovací sada
&nbsp;funkce&nbsp;detekce&nbsp;mohutnosti vysoké úrovně.|  **Předaný** <br> <br>**Hotovo**|   Nezjistily se žádné funkce vysoké mohutnosti. <br><br> Zjistily se vstupní sloupce vysoké mohutnosti.
Detekce zůstatku třídy |**Předaný** <br><br><br>**Upozorněni** |Třídy jsou vyvážené do školicích dat; Datová sada je považována za vyváženou, pokud každá třída má v datové sadě dobrý reprezentace, měřená podle počtu a poměru vzorků. <br> <br> Třídy v školicích datech jsou nevyrovnané
Konzistence dat časové řady|**Předaný** <br><br><br><br> **Určí** |<br> Vybrané hodnoty {horizont, lag, kumulovaný interval} byly analyzovány a nebyly zjištěny žádné potenciální problémy způsobené nedostatkem paměti. <br> <br>Vybrané hodnoty {horizont, lag, kumulovaný interval} byly analyzovány a mohou způsobit nedostatek paměti experimentu. Zpoždění nebo posuvné okno bylo vypnuto.

## <a name="run-experiment-and-view-results"></a>Spuštění experimentu a zobrazení výsledků

Vyberte **Dokončit** pro spuštění experimentu. Proces přípravy experimentu může trvat až 10 minut. U každého kanálu může další 2 až 3 minuty trvat, než se dokončí úlohy trénování.

### <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Otevře se obrazovka s **podrobnostmi o spuštění** na kartě **Podrobnosti** . Na této obrazovce se zobrazí souhrn experimentu, včetně stavového řádku v horní části vedle čísla běhu. 

Na kartě **Modely** je seznam vytvořených modelů seřazený podle skóre metriky. Ve výchozím nastavení se na prvním místě seznamu zobrazí model, který na základě zvolené metriky získá nejvyšší skóre. Když trénovací úloha vyzkouší další modely, přidají se do seznamu. Tady můžete rychle porovnat metriky pro zatím vytvořené modely.

[řídicí panel podrobností spuštění ![](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Zobrazit podrobnosti o školicím běhu

Projděte si některé z dokončených modelů a podívejte se na podrobnosti o školicím běhu, jako jsou metriky spuštění na kartě **Podrobnosti modelu** nebo grafy výkonu na kartě **vizualizace** . další [informace o grafech](how-to-understand-automated-ml.md).

[Podrobnosti ![iterace](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Nasazení modelu

Jakmile budete mít nejlepší model na ruce, je čas ho nasadit jako webovou službu pro předpověď na nová data.

Automatizované ML vám pomůže s nasazením modelu bez psaní kódu:

1. Máte několik možností nasazení. 

    + Možnost 1: Pokud chcete nasadit nejlepší model (podle definovaných kritérií metriky), vyberte na kartě **Podrobnosti** tlačítko **nasadit nejlepší model** .

    + Možnost 2: Pokud chcete z tohoto experimentu nasadit konkrétní iteraci modelu, přejděte k podrobnostem v modelu a otevřete kartu **Podrobnosti modelu** a vyberte **nasadit model**.

1. Naplňte podokno **nasazení modelu** .

    Pole| Hodnota
    ----|----
    Název| Zadejte jedinečný název pro vaše nasazení.
    Popis| Zadejte popis, který bude lépe identifikovat, pro které nasazení probíhá.
    Typ výpočtu| Vyberte typ koncového bodu, který chcete nasadit: *Azure Kubernetes Service (AKS)* nebo *Azure Container instance (ACI)* .
    Název výpočtu| *Platí jenom pro AKS:* Vyberte název clusteru AKS, do kterého chcete nasadit.
    Povolit ověřování | Tuto možnost vyberte, pokud chcete povolení ověřování na základě tokenu nebo klíče.
    Použití vlastních prostředků nasazení| Tuto funkci povolte, pokud chcete nahrát vlastní skript bodování a soubor prostředí. [Přečtěte si další informace o skriptech bodování](how-to-deploy-and-where.md#script).

    >[!Important]
    > Názvy souborů musí být pod 32 znaků a musí začínat a končit alfanumerickými znaky. Může obsahovat pomlčky, podtržítka, tečky a alfanumerické znaky mezi. Mezery nejsou povoleny.

    Nabídka *Upřesnit* nabízí výchozí funkce pro nasazení, jako je například [shromažďování dat](how-to-enable-app-insights.md) a nastavení využití prostředků. Pokud si přejete přepsat tato výchozí nastavení, udělejte to v této nabídce.

1. Vyberte **Nasadit**. Dokončení nasazení může trvat přibližně 20 minut.

Nyní máte provozní webovou službu, která generuje předpovědi. Předpovědi můžete testovat pomocí dotazování služby z [integrované Azure Machine Learning podpory v Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si kompletní [kurz tvorby prvního automatizovaného experimentu s Azure Machine Learning studia](tutorial-first-experiment-automated-ml.md). 
* [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md) a Azure Machine Learning.
* [Pochopte automatizované výsledky strojového učení](how-to-understand-automated-ml.md).
* [Naučte se využívat webovou službu](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
