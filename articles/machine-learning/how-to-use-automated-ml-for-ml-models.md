---
title: Použití automatického ml k vytvoření modelů & nasazení
titleSuffix: Azure Machine Learning
description: Vytvářejte, kontrolujte a nasazujte automatizované modely strojového učení pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257227"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Vytvářejte, kontrolujte a nasazujte automatizované modely strojového učení pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit, prozkoumat a nasadit automatizované modely strojového učení bez jediného řádku kódu ve studiovém rozhraní Azure Machine Learning. Automatizované strojové učení je proces, ve kterém je vybrán nejlepší algoritmus strojového učení, který se má použít pro vaše konkrétní data. Tento proces umožňuje rychle generovat modely strojového učení. [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md).
 
Pro příklad konce na konec vyzkoušejte [kurz pro vytvoření klasifikačního modelu s automatizovaným rozhraním ML Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Pro prostředí založené na kódu Pythonu [nakonfigurujte automatizované experimenty strojového učení](how-to-configure-auto-train.md) pomocí sady Azure Machine Learning SDK.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní prostor Azure Machine Learning s typem **edice Enterprise**. Viz [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).  Informace o upgradu existujícího pracovního prostoru na edici Enterprise najdete v [tématu Upgrade na edici Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Začínáme

1. Přihlaste se k https://ml.azure.comAzure Machine Learning na . 

1. Vyberte předplatné a pracovní prostor. 

1. Přejděte do levého podokna. V části **Autor** vyberte **Automatizovaná ml.**

[![Navigační podokno Studia Azure strojového učení](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Pokud experimenty provádíte poprvé, zobrazí se prázdný seznam a odkazy na dokumentaci. 

V opačném případě se zobrazí seznam nedávných automatizovaných experimentů strojového učení, včetně těch vytvořených pomocí sady SDK. 

## <a name="create-and-run-experiment"></a>Vytvořit a spustit experiment

1. Vyberte **+ Nové automatické ml spustit** a naplnit formulář.

1. Vyberte datovou sadu z kontejneru úložiště nebo vytvořte novou datovou sadu. Datové sady lze vytvářet z místních souborů, webových adres URL, datových úložišť nebo otevřených datových sad Azure. 

    >[!Important]
    > Požadavky na údaje o školení:
    >* Data musí být v tabulkové podobě.
    >* Hodnota, kterou chcete předpovědět (cílový sloupec) musí být přítomna v datech.

    1. Chcete-li vytvořit novou datovou sadu ze souboru v místním počítači, vyberte **procházet** a vyberte soubor. 

    1. Pojmenujte datovou sadu jedinečným názvem a poskytněte volitelný popis. 

    1. Výběrem **možnosti Další** otevřete **formulář Datastore a výběr souborů**. V tomto formuláři vyberete, kam chcete nahrát datovou sadu; výchozí kontejner úložiště, který se automaticky vytvoří s vaším pracovním prostorem, nebo zvolte kontejner úložiště, který chcete použít pro experiment. 

    1. Zkontrolujte **nastavení a náhled** formuláře pro přesnost. Formulář je inteligentně naplněn na základě typu souboru. 

        Pole| Popis
        ----|----
        Formát souboru| Definuje rozložení a typ dat uložených v souboru.
        Oddělovač| Jeden nebo více znaků pro určení hranice mezi samostatnými nezávislými oblastmi ve formátu prostého textu nebo jiných datových proudech.
        Kódování| Určuje, jaký bit do tabulky schématu znaků použít ke čtení datové sady.
        Záhlaví sloupců| Označuje, jak bude zpracováno záhlaví datové sady, pokud existuje.
        Přeskočit řádky | Označuje, kolik řádků, pokud existuje, je v datové sadě přeskočeno.
    
        Vyberte **další**.

    1. Formulář **Schéma** je inteligentně naplněn na základě výběrů ve formuláři **Nastavení a náhledu.** Zde nakonfigurujte datový typ pro každý sloupec, zkontrolujte názvy sloupců a vyberte sloupce, které chcete pro experiment **zahrnout.** 
            
        Vyberte **Další.**

    1. Formulář **Potvrdit podrobnosti** je souhrninformací, které byly dříve vyplněny ve formulářích **Základní informace** a Nastavení a **náhled.** Máte také možnost vytvořit datový profil pro datovou sadu pomocí výpočetních prostředků s povoleným profilováním. Další informace o [profilování dat](#profile).

        Vyberte **další**.
1. Jakmile se zobrazí, vyberte nově vytvořenou datovou sadu. Můžete také zobrazit náhled datové sady a ukázkové statistiky. 

1. Ve formuláři **Konfigurovat spuštění** zadejte jedinečný název experimentu.

1. Vyberte cílový sloupec; Toto je sloupec, na který chcete provést předpovědi.

1. Vyberte výpočetní prostředky pro profilování dat a školení úlohy. Seznam existujících výpočtů je k dispozici v rozevíracím seznamu. Chcete-li vytvořit nový výpočetní výkon, postupujte podle pokynů v kroku 7.

1. Vyberte **Vytvořit nový výpočetní výkon** a nakonfigurujete výpočetní kontext pro tento experiment.

    Pole|Popis
    ---|---
    Název výpočetního výkonu| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro vaše výpočty.
    Min / Max uzly (v pokročilých nastaveních)| Chcete-li daty profilu, musíte zadat 1 nebo více uzlů. Zadejte maximální počet uzlů pro vaše výpočty. Výchozí hodnota je 6 uzlů pro výpočetní výkon AML.
    
    Vyberte **Vytvořit**. Vytvoření nového výpočetního výkonu může trvat několik minut.

    >[!NOTE]
    > Název výpočetní ho výpočtu bude označovat, jestli je povoleno vybrané nebo *vytvářené výpočty*. (Další podrobnosti naleznete v [části profilování dat).](#profile)

    Vyberte **další**.

1. Ve formuláři **Typ úkolu a nastavení** vyberte typ úkolu: klasifikace, regrese nebo prognózování. Další informace naleznete [v tématu Definování typů úkolů.](how-to-define-task-type.md)

    1. Pro klasifikaci můžete také povolit hloubkové učení, které se používá pro textové featurizations.

    1. Pro prognózování:
        1. Vybrat sloupec času: Tento sloupec obsahuje časová data, která mají být použita.

        1. Vyberte horizont prognózy: Uveďte, kolik časových jednotek (minut/hodin/dnů/týdnů/měsíců/let) bude model schopen předpovědět do budoucna. Čím dále je model nutný k předpovídání do budoucna, tím méně přesný se stane. [Další informace o prognózování a horizontu prognóz](how-to-auto-train-forecast.md).

1. (Nepovinné) Zobrazit nastavení konfigurace přidání: další nastavení, které můžete použít k lepšímu řízení úlohy školení. V opačném případě jsou výchozí hodnoty použity na základě výběru experimentu a dat. 

    Další konfigurace|Popis
    ------|------
    Primární metrika| Hlavní metrika používaná pro vyhodnocování modelu. [Další informace o metrikách modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Automatická featurizace| Tuto možnost vyberte, chcete-li povolit nebo zakázat předběžné zpracování provedené automatizovaným strojovým učením. Předběžné zpracování zahrnuje automatické čištění dat, přípravu a transformaci za účelem generování syntetických funkcí. Pro typ úkolu prognózy časových řad není podporován. [Další informace o předběžném zpracování](#featurization). 
    Vysvětlete nejlepší model | Výběrem této možnosti povolíte nebo zakážete, chcete-li zobrazit vysvětlitelnost doporučeného nejlepšího modelu.
    Blokovaný algoritmus| Vyberte algoritmy, které chcete vyloučit z úlohy školení.
    Kritérium ukončení| Pokud jsou splněna některá z těchto kritérií, je úloha školení zastavena. <br> *Doba školení (hodiny):* Jak dlouho má být úloha školení spuštěna. <br> *Prahová hodnota metriky:* Minimální metrické skóre pro všechny kanály. Tím zajistíte, že pokud máte definovanou cílovou metriku, kterou chcete dosáhnout, nestrávíte více času na školení, než je nutné.
    Ověřování| Vyberte jednu z možností křížového ověření, kterou chcete použít v úlohě školení. [Další informace o křížovém ověřování](how-to-configure-auto-train.md).
    Souběžnost| *Maximální počet souběžných iterací:* Maximální počet kanálů (iterací) k testování v úlohě školení. Úloha nebude spuštěna více než zadaný počet iterací.

1. (Nepovinné) Zobrazení nastavení featurization: Pokud se rozhodnete povolit **automatické featurization** ve formuláři **Další nastavení konfigurace,** tento formulář je místo, kde určíte sloupce, ve kterých chcete provést tyto featurizations na a vyberte, která statistická hodnota má použít pro chybějící imputace hodnoty.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Souhrnné statistiky & profilování dat

Můžete získat širokou škálu souhrnných statistik v celé sadě dat a ověřit, zda je vaše datová sada připravená na ml. Pro nečíselné sloupce obsahují pouze základní statistiky, jako je min, max a počet chyb. U číselných sloupců můžete také zkontrolovat jejich statistické momenty a odhadované kvantily. Konkrétně náš datový profil zahrnuje:

>[!NOTE]
> Prázdné položky se zobrazí pro prvky s irelevantní typy.

Statistika|Popis
------|------
Funkce| Název sloupce, který je sumarizován.
Profil| In-line vizualizace na základě typu odvodit. Například řetězce, logické hodnoty a data budou mít hodnoty, zatímco desetinná místa (číselníky) mají aproximované histogramy. To vám umožní získat rychlé pochopení distribuce dat.
Rozložení typu| Počet in-line hodnot typů ve sloupci. Hodnoty Null jsou jejich vlastní typ, takže tato vizualizace je užitečná pro detekci lichých nebo chybějících hodnot.
Typ|Odvozený typ sloupce. Možné hodnoty zahrnují: řetězce, logické hodnoty, data a desetinná místa.
Minimum| Minimální hodnota sloupce. Prázdné položky se zobrazí pro funkce, jejichž typ nemá vlastní řazení (např. logické hodnoty).
Maximum| Maximální hodnota sloupce. 
Počet| Celkový počet chybějících a nechybějících položek ve sloupci.
Nechybí počet| Počet položek ve sloupci, které nechybí. Prázdné řetězce a chyby jsou považovány za hodnoty, takže nebudou přispívat k "nechybí počet."
Kvantily| Přibližné hodnoty v každém kvantitu poskytnout pocit distribuce dat.
Mean| Aritmetický průměr nebo průměr sloupce.
Směrodatná odchylka| Měření množství rozptylu nebo změny dat tohoto sloupce.
Rozptyl| Měření, jak daleko jsou data tohoto sloupce, pochází z jeho průměrné hodnoty. 
Šikmost| Měření, jak se liší data tohoto sloupce od normální distribuce.
Špičatost| Měření toho, jak silně sledoval data tohoto sloupce je porovnán s normální rozdělení.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Pokročilé možnosti featurization

Automatizované strojové učení nabízí automatické předběžné zpracování a svodidla dat, která vám pomohou identifikovat a spravovat potenciální problémy s vašimi daty. 

### <a name="preprocessing"></a>Předzpracování

> [!NOTE]
> Pokud plánujete exportovat modely vytvořené automatickým ML do [modelu ONNX](concept-onnx.md), jsou ve formátu ONNX podporovány pouze možnosti featurization označené * . Další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx). 

|Kroky&nbsp;předběžného zpracování| Popis |
| ------------- | ------------- |
|Pokles vysoké mohutnosti nebo žádné funkce rozptylu* |Drop tyto z trénovací a ověřovací sady, včetně funkcí se všemi hodnotami chybí, stejnou hodnotu ve všech řádcích nebo s extrémně vysokou mohutnost (například hodnoty hash, ID nebo GUID).|
|Impute chybějící hodnoty* |Pro číselné funkce, impute s průměrem hodnot ve sloupci.<br/><br/>Pro kategorické funkce, impute s nejčastější hodnotou.|
|Generovat další funkce* |Funkce DateTime: Rok, Měsíc, Den, Den v týdnu, Den v roce, Čtvrtletí, Týden v roce, Hodina, Minuta, Sekunda.<br/><br/>Funkce textu: Frekvence termínů na základě unigramů, dvougramů a triznakových gramů.|
|Transformace a kódování *|Číselné prvky s několika málo jedinečnými hodnotami jsou transformovány do kategorických prvků.<br/><br/>Jednohorké kódování se provádí pro nízkou mohutnost kategorické; pro vysokou mohutnost, jedno-hot-hash kódování.|
|Vkládání do Wordu|Text featurizer, který převádí vektory textových tokenů na vektory věty pomocí předem trénovaného modelu. Vektor vkládání každého slova v dokumentu je agregován dohromady a vytváří vektor prvku dokumentu.|
|Cílové kódování|Pro kategorické funkce mapuje každou kategorii s průměrnou cílovou hodnotou pro regresní problémy a pravděpodobnost třídy pro každou třídu pro problémy klasifikace. K omezení montáže mapování a šumu způsobeného řídkými datovými kategoriemi se používá vážení na základě frekvence a křížové ověření k-fold.|
|Kódování cíle textu|Pro zadávání textu se ke generování pravděpodobnosti jednotlivých tříd používá skládaný lineární model s vakem slov.|
|Průkazová váha (WoE)|Vypočítá woe jako měřítko korelace kategorických sloupců s cílovým sloupcem. Vypočítá se jako protokol poměru pravděpodobností ve třídě vs mimo třídu. Tento krok vyvede jeden sloupec číselných prvků na třídu a odstraní potřebu explicitně přičítat chybějící hodnoty a odlehlé ošetření.|
|Vzdálenost clusteru|Trénuje model clusteringu k-znamená ve všech číselných sloupcích.  Výstupy k nové funkce, jeden nový číselný prvek na clusteru, obsahující vzdálenost každého vzorku do centraktoid každého clusteru.|

### <a name="data-guardrails"></a>Datová svodidla

Svodidla dat se používají, když je povolena automatická featurization nebo ověření je nastavena na auto. Svodidla dat vám pomohou identifikovat potenciální problémy s vašimi daty (např. chybějící hodnoty, nerovnováha ve třídě) a pomáhají přijmout nápravná opatření pro zlepšení výsledků. 

Uživatelé mohou zkontrolovat svodidla dat ve studiu v záložce Datové ```show_output=True``` **svodidla** automatického spuštění ml nebo nastavením při odeslání experimentu pomocí sady Python SDK. 

#### <a name="data-guardrail-states"></a>Stavy svodidla dat

Svodidla dat zobrazí jeden ze tří stavů: **Passed**, **Done**, nebo **Alerted**.

Stav| Popis
----|----
Předán| Nebyly zjištěny žádné problémy s daty a není vyžadována žádná akce uživatele. 
Hotovo| Změny byly použity u vašich dat. Doporučujeme uživatelům, aby přezkoumali nápravná opatření, která automatizovaná hodnota ML provedla, aby bylo zajištěno, že změny budou v souladu s očekávanými výsledky. 
Upozorněni| Byl zjištěn problém s daty, který nebylo možné odstranit. Doporučujeme uživatelům, aby problém revidovali a opravili. 

>[!NOTE]
> Předchozí verze automatizovaných experimentů ML zobrazovaly čtvrtý stav: **Opraveno**. Novější experimenty se nezobrazí tento stav, a všechna svodidla, která se zobrazí **pevný** stav se nyní zobrazí **Hotovo**.   

Následující tabulka popisuje aktuálně podporovaná svodidla dat a přidružené stavy, se kterými se uživatelé mohou sejít při odesílání experimentu.

Svodidla|Status|Podmínka&nbsp;&nbsp;pro aktivační událost
---|---|---
Chybějící přinikace hodnot prvků |**Předán** <br><br><br> **hotovo**| V trénovacích datech nebyly zjištěny žádné chybějící hodnoty prvků. Další informace o [chybějící imputaci hodnoty.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> V trénovacích datech byly zjištěny chybějící hodnoty funkcí a byly imputovány.
Zpracování prvků s vysokou mohutností |**Předán** <br><br><br> **hotovo**| Vaše vstupy byly analyzovány a nebyly zjištěny žádné funkce vysoké mohutnosti. Další informace o [detekci funkcí vysoké mohutnosti.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Funkce vysoké mohutnosti byly zjištěny ve vašich vstupech a byly zpracovány.
Zpracování rozdělení ověření |**hotovo**| *Konfigurace ověření byla nastavena na "auto" a trénovací data obsahovala **méně** než 20 000 řádků.* <br> Každá iterace trénovaného modelu byla ověřena pomocí křížového ověření. Přečtěte si další informace o [ověřovacích datech.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *Konfigurace ověření byla nastavena na "auto" a trénovací data obsahovala **více** než 20 000 řádků.* <br> Vstupní data byla rozdělena do trénovací datové sady a ověřovací datové sady pro ověření modelu.
Detekce vyvažování tříd |**Předán** <br><br><br><br> **Upozorněni** | Vaše vstupy byly analyzovány a všechny třídy jsou vyvážené v trénovacích datech. Datová sada je považována za vyváženou, pokud každá třída má v datové sadě dobrou reprezentaci, měřeno počtem a poměrem vzorků. <br><br><br> Ve vstupech byly zjištěny nevyvážené třídy. Chcete-li opravit zkreslení modelu opravit problém vyvažování. Přečtěte si další informace o [nevyvážených datech.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Zjišťování problémů s pamětí |**Předán** <br><br><br><br> **hotovo** |<br> Vybraná hodnota {horizont, zpoždění, rolovací okno} byla analyzována a nebyly zjištěny žádné potenciální problémy s nepaměti. Přečtěte si další informace o [konfiguracích prognóz časových řad.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>Vybrané hodnoty {horizont, zpoždění, postupné okno} byly analyzovány a potenciálně způsobí, že experiment bude mít nedostatek paměti. Konfigurace lagnebo rolling window byly vypnuty.
Detekce frekvence |**Předán** <br><br><br><br> **hotovo** |<br> Časové řady byly analyzovány a všechny datové body jsou zarovnány s detekotorní frekvencí. <br> <br> Byly analyzovány časové řady a byly zjištěny datové body, které nejsou v souladu s detekotorní frekvencí. Tyto datové body byly odebrány z datové sady. Přečtěte si další informace o [přípravě dat pro prognózování časových řad.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Spuštění experimentu a zobrazení výsledků

Chcete-li experiment spustit, vyberte **možnost Dokončit.** Proces přípravy experimentu může trvat až 10 minut. U každého kanálu může další 2 až 3 minuty trvat, než se dokončí úlohy trénování.

### <a name="view-experiment-details"></a>Zobrazení podrobností o experimentu

Na kartě **Podrobnosti** se otevře obrazovka **Podrobnosti.** Na této obrazovce se zobrazí souhrn experimentu, včetně stavového řádku v horní části vedle čísla spuštění. 

Na kartě **Modely** je seznam vytvořených modelů seřazený podle skóre metriky. Ve výchozím nastavení se na prvním místě seznamu zobrazí model, který na základě zvolené metriky získá nejvyšší skóre. Když trénovací úloha vyzkouší další modely, přidají se do seznamu. Tady můžete rychle porovnat metriky pro zatím vytvořené modely.

[![Řídicí panel Podrobnosti spuštění](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Zobrazit podrobnosti o tréninkovém běhu

Chcete-li zobrazit podrobnosti o spuštění školení, například spustit metriky na kartě **Podrobnosti modelu** nebo grafy výkonu na kartě Vizualizace, přejděte k podrobnostem [o](how-to-understand-automated-ml.md)všech **dokončených modelech.**

[![Podrobnosti o iteraci](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Nasazení modelu

Jakmile budete mít nejlepší model po ruce, je čas nasadit jako webovou službu předpovědět na nová data.

Automatizované ML vám pomůže s nasazením modelu bez psaní kódu:

1. Máte několik možností pro nasazení. 

    + Možnost 1: Chcete-li nasadit nejlepší model (podle kritérií metriky, která jste definovali), vyberte tlačítko **Nasadit nejlepší model** na kartě **Podrobnosti.**

    + Možnost 2: Chcete-li nasadit konkrétní iteraci modelu z tohoto experimentu, přejděte k podrobnostem modelu a otevřete kartu **Podrobnosti modelu** a vyberte **Nasadit model**.

1. Naplňte podokno **nasadit model.**

    Pole| Hodnota
    ----|----
    Name (Název)| Zadejte jedinečný název pro nasazení.
    Popis| Zadejte popis, který lépe určí, k čemu je toto nasazení určen.
    Typ výpočtu| Vyberte typ koncového bodu, který chcete nasadit: *Azure Kubernetes Service (AKS)* nebo *Azure Container Instance (ACI)*.
    Název výpočetního výkonu| *Platí pouze pro AKS:* Vyberte název clusteru AKS, do který chcete nasadit.
    Povolení ověřování | Výběrem vyberte, chcete-li povolit ověřování založené na tokenech nebo klíči.
    Použití vlastních prostředků nasazení| Tuto funkci povolte, pokud chcete nahrát vlastní bodovací skript a soubor prostředí. [Další informace o vyhodnocování skriptů](how-to-deploy-and-where.md#script).

    >[!Important]
    > Názvy souborů musí být pod 32 znaky a musí začínat a končit alfanumerickými znaky. Mezi nimi mohou obsahovat pomlčky, podtržítka, tečky a alfanumerické znaky. Mezery nejsou povoleny.

    Nabídka *Upřesnit* nabízí výchozí funkce nasazení, jako je [shromažďování dat](how-to-enable-app-insights.md) a nastavení využití prostředků. Pokud chcete přepsat tyto výchozí hodnoty, uvažte v této nabídce.

1. Vyberte **Nasadit**. Nasazení může trvat přibližně 20 minut.

Nyní máte provozní webové služby pro generování předpovědí! Předpovědi můžete otestovat dotazem na službu z [integrované podpory Azure Machine Learning u Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak využívat webovou službu](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Seznamte se s výsledky automatizovaného strojového učení](how-to-understand-automated-ml.md).
* [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md) a Azure Machine Learningu.
