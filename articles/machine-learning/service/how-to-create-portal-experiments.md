---
title: Vytváření a průzkum experimentů na portálu
titleSuffix: Azure Machine Learning service
description: Naučte se vytvářet a spravovat automatizované experimenty strojového učení na portálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 5eb3e94ff65e8a8b74f357a4cb8a517fd3837c5a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871813"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Vytvářejte a zkoumejte automatizované experimenty strojového učení v Azure Portal (Preview)

 V tomto článku se naučíte, jak vytvořit, spustit a prozkoumat automatizované experimenty strojového učení v Azure Portal bez jediného řádku kódu. Automatizované Machine Learning automatizuje proces výběru nejlepšího algoritmu, který se má použít pro vaše konkrétní data, takže můžete model strojového učení rychle vygenerovat. [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md).

 Pokud dáváte přednost více prostředím založeným na kódu, můžete [v Pythonu nakonfigurovat i automatizované experimenty strojového učení](how-to-configure-auto-train.md) s [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Začínáme

Přejděte do levého podokna pracovního prostoru. V části vytváření obsahu (Preview) vyberte automatizované Machine Learning.

![Azure Portal navigační podokno](media/how-to-create-portal-experiments/nav-pane.png)

 Pokud provádíte jakékoli experimenty s automatizovaným Machine Learning, zobrazí se následující:

![Cílová stránka experimentu Azure Portal](media/how-to-create-portal-experiments/landing-page.png)

V opačném případě uvidíte na automatizovaném řídicím panelu Machine Learning přehled všech automatických experimentů strojového učení, včetně těch, které se vytvořily pomocí sady SDK. Tady můžete filtrovat a zkoumat své běhy podle data, experimentu a stavu spuštění.

![Řídicí panel experimentování Azure Portal](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Vytvoření experimentu

Kliknutím na tlačítko vytvořit experiment naplňte následující formulář.

![Vytvořit formulář experimentu](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Zadejte název experimentu.

1. Pro úlohu profilace a školení pro data vyberte výpočetní prostředky. V rozevíracím seznamu jsou k dispozici seznam vašich stávajících výpočtů. Pokud chcete vytvořit nový výpočetní výkon, postupujte podle pokynů v kroku 3.

1. Kliknutím na tlačítko vytvořit nový COMPUTE otevřete podokno níže a nakonfigurujte výpočetní kontext pro tento experiment.

    ![Vytvoření nových výpočetních prostředků pro experiment](media/how-to-create-portal-experiments/create-new-compute.png)

    Pole|Popis
    ---|---
    Název výpočtu| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.
    Další nastavení| *Minimální uzel*: Zadejte minimální počet uzlů pro výpočetní výkon. Minimální počet uzlů pro výpočetní výkon AML je 0. Chcete-li povolit profilaci dat, je nutné mít nejméně jeden uzel. <br> *Maximální počet uzlů*: Zadejte maximální počet uzlů pro výpočetní výkon. Výchozí hodnota je 6 uzlů pro AML Compute.

      Pokud chcete začít vytvářet nové výpočetní prostředky, vyberte **vytvořit**. Toto může chvíli trvat.

      >[!NOTE]
      > Váš název COMPUTE určí, jestli je povolená možnost *profilace*, kterou vybíráte nebo vytváříte. (Další podrobnosti o profilaci dat najdete v článku 7b).

1. Vyberte účet úložiště pro vaše data. Verze Public Preview podporuje jenom místní nahrávání souborů a účty Azure Blob Storage.

1. Vyberte kontejner úložiště.

1. Vyberte datový soubor z kontejneru úložiště nebo nahrajte soubor z místního počítače do kontejneru.

    ![Vybrat datový soubor pro experimentování](media/how-to-create-portal-experiments/select-file.png)

1. Pomocí karet náhled a profil můžete dále konfigurovat data pro tento experiment.

    1. Na kartě náhled určete, jestli data obsahují záhlaví, a vyberte funkce (sloupce) pro školení pomocí integrovaných **tlačítek přepínače v** jednotlivých sloupcích funkce.

        ![Náhled dat](media/how-to-create-portal-experiments/data-preview.png)

    1. Na kartě Profil můžete zobrazit [datový profil](#profile) podle funkcí a také rozdělení, typ a souhrn statistik (střední, střední, Max/min atd.) každého.

        ![Karta datový profil](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Pokud váš výpočetní **kontext není** profilace povolený, zobrazí se následující chybová zpráva: *Profilace dat je k dispozici pouze pro cíle výpočetní služby, které jsou již spuštěny*.

1. Vyberte typ úlohy školení: klasifikace, regrese nebo prognózování.

1. Vyberte cílový sloupec. Sloupec, na kterém se má předpovědi zapnout

1. Pro prognózování:
    1. Vyberte sloupec čas: Tento sloupec obsahuje časová data, která se mají použít.
    1. Vybrat horizont předpovědi: Určete, kolik časových jednotek (minuty/hodiny/dny/týdny/měsíce/roky) bude model moci předpovědět budoucímu. Dalším modelem se vyžaduje předpověď do budoucna, tím méně přesné bude. [Přečtěte si další informace o prognózování a horizontu předpovědi](how-to-auto-train-forecast.md).

1. Volitelné Rozšířená nastavení: Další nastavení, která můžete použít k lepšímu řízení úlohy školení.

    Pokročilá nastavení|Popis
    ------|------
    Primární metrika| Hlavní metrika použitá pro vyhodnocování modelu. [Přečtěte si další informace o metrikách modelů](how-to-configure-auto-train.md#explore-model-metrics).
    Výstupní kritéria| Pokud je splněno kterékoli z těchto kritérií, úloha školení skončí před úplným dokončením. <br> *Doba úlohy školení (minuty)* : Doba, po kterou je možné spustit úlohu školení.  <br> *Maximální počet iterací*: Maximální počet kanálů (iterací), které se mají testovat v úloze školení Úloha nebude spouštět více než zadaný počet iterací. <br> *Prahová hodnota skóre metriky*:  Minimální skóre metriky pro všechny kanály. Tím zajistíte, že pokud máte definovanou cílovou metriku, která má být dostupná, nebudete věnovat více času školicím úlohám, než je potřeba.
    Předzpracování| Tuto možnost vyberte, pokud chcete povolit nebo zakázat předzpracování prováděné automatizovaným strojovým učením. Předzpracování zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce. [Přečtěte si další informace o předzpracování](#preprocess).
    Ověření| Vyberte jednu z možností vzájemného ověření, kterou chcete použít v úloze školení. [Další informace o vzájemném ověřování](how-to-configure-auto-train.md).
    Souběžnost| Vyberte omezení více jader, která chcete použít při použití COMPUTE s více jádry.
    Blokovaný algoritmus| Vyberte algoritmy, které chcete vyloučit z úlohy školení.

   ![Formulář Upřesnit nastavení](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Chcete-li získat další informace o polích, klikněte na popis informačního nástroje.

<a name="profile"></a>

### <a name="data-profiling"></a>Profilace dat

V rámci datové sady můžete získat velké množství různých souhrnných statistik, abyste ověřili, jestli je vaše datová sada připravená na ML. Pro nečíselné sloupce obsahují jenom základní statistiky, jako je min, Max a počet chyb. Pro číselné sloupce si můžete prohlédnout i jejich statistické momenty a odhadované quantiles. Konkrétně náš datový profil zahrnuje:

* **Funkce**: název sloupce, který se shrnuje.

* **Profile**: vložená vizualizace na základě typu odvozeného. Například řetězce, logické hodnoty a data budou mít počty hodnot, zatímco desetinná místa (číslice) mají přibližné histogramy. To vám umožní získat rychlé porozumění distribuci dat.

* **Distribuce typů**: počet vložených hodnot typů v rámci sloupce. Hodnoty null jsou jejich vlastní typ, takže tato vizualizace je užitečná pro zjištění lichých nebo chybějících hodnot.

* **Typ**: odvozený typ sloupce. Možné hodnoty jsou: řetězce, logické hodnoty, kalendářní data a desetinná místa.

* **Min**: minimální hodnota sloupce Pro funkce, jejichž typ nemá základní řazení (např. logické hodnoty), se zobrazí prázdné položky.

* **Max**: maximální hodnota sloupce. Například "min", prázdné položky se zobrazí pro funkce s nepodstatnými typy.

* **Count (počet**): celkový počet chybějících a nechybějících položek ve sloupci.

* **Počet**nechybějících položek: počet položek ve sloupci, které nebyly nalezeny. Všimněte si, že prázdné řetězce a chyby jsou považovány za hodnoty, takže nebudou přispívat k "nechybějícímu počtu".

* **Quantiles** (v 0,1, 1, 5, 25, 50, 75, 95, 99 a 99,9% intervalů): přibližné hodnoty na jednotlivých Quantile, které poskytují smysl distribuce dat. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

* **Střední hodnota**: aritmetický průměr sloupce. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

* Směrodatná **Odchylka**: směrodatná odchylka sloupce. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

* **Variance**: odchylka sloupce. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

* **Asymetrie**: zešikmení sloupce. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

* **Fluktuace**: špičatost sloupce. Pro funkce s nepodstatnými typy se zobrazí prázdné položky.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Pokročilý předzpracování

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

Chcete-li spustit experiment, klikněte na tlačítko Spustit. Proces přípravy procesu trvá několik minut.

### <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Po dokončení fáze přípravy experimentu se zobrazí obrazovka s podrobnostmi o spuštění. Tím získáte úplný seznam vytvořených modelů. Ve výchozím nastavení je model, který vyrovnává nejvyšší hodnoty na základě vašich parametrů, v horní části seznamu. Když se úloha školení pokusí o další modely, přidají se do seznamu iterací a do grafu. Pomocí grafu iterace získáte rychlé porovnání metrik pro modely, které byly doposud vyprodukovány.

Školicí úlohy můžou nějakou dobu trvat, než se dokončí spuštění každého kanálu.

![Spustit řídicí panel podrobností](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Zobrazit podrobnosti o školicím běhu

Projděte si podrobné informace o všech výstupních modelech a podívejte se na podrobnosti o školicích běhůch, jako jsou metriky výkonu a distribuční grafy. [Přečtěte si další informace o grafech](how-to-track-experiments.md#understanding-automated-ml-charts).

![Podrobnosti iterace](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Nasazení modelu

Jakmile budete mít nejlepší model na ruce, je čas ho nasadit jako webovou službu pro předpověď na nová data.

Automatizované ML vám pomůže s nasazením modelu bez psaní kódu:

1. Máte k dispozici několik možností nasazení. 
    1. Pokud chcete nasadit nejlepší model na základě kritérií metriky, které jste pro experiment nastavili, vyberte **nasadit nejlepší model** ze stránky **podrobností o spuštění** .

        ![Tlačítko nasadit model](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Pokud chcete nasadit určitou iteraci modelu, přejděte k podrobnostem v modelu a otevřete její konkrétní stránku s podrobnostmi o spuštění a vyberte **nasadit model**.

        ![Tlačítko nasadit model](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Prvním krokem je registrace modelu do služby. Vyberte registrovat model a počkejte na dokončení procesu registrace.

    ![Okno nasadit model](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Po registraci modelu budete moct stáhnout skript bodování (scoring.py) a skript prostředí (condaEnv. yml), který se použije při nasazení.

1. Po stažení skriptu bodování a skriptu prostředí v levém navigačním podokně vyberte okno **assety** a vyberte **modely**.

    ![Modely navigačního podokna](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Vyberte model, který jste zaregistrovali, a vyberte vytvořit bitovou kopii.

    Model můžete identifikovat podle jeho popisu, který bude obsahovat ID běhu, číslo iterace v následujícím formátu: *< Run_ID > _ < Iteration_number > _Model*

    ![Vzor Vytvořit bitovou kopii](media/how-to-create-portal-experiments/model-create-image.png)

1. Zadejte název obrázku. 
1. Kliknutím na tlačítko **Procházet** vedle pole "soubor bodování" nahrajte soubor bodování (scoring.py), který jste předtím stáhli.

1. Pokud chcete nahrát soubor prostředí (condaEnv. yml), který jste předtím stáhli, vyberte tlačítko **Procházet** vedle pole "soubor conda".

    Můžete použít svůj vlastní hodnoticí skript a soubor conda a také nahrávat další soubory. [Přečtěte si další informace o skriptu bodování](how-to-deploy-and-where.md#script).

      >[!Important]
      > Názvy souborů musí být pod 32 znaků a musí začínat a končit alfanumerickými znaky. Může obsahovat pomlčky, podtržítka, tečky a alfanumerické znaky mezi. Mezery nejsou povoleny.

    ![Vytvořit bitovou kopii](media/how-to-create-portal-experiments/create-image.png)

1. Kliknutím na tlačítko vytvořit spusťte vytvoření bitové kopie. Dokončení této akce bude trvat několik minut. po dokončení se zobrazí zpráva na horním panelu.
1. Přejdete na kartu images, zaškrtněte políčko vedle image, kterou chcete nasadit, a vyberte vytvořit nasazení. [Přečtěte si další informace o nasazeních](how-to-deploy-and-where.md).

    Pro nasazení existují 2 možnosti.
     + Azure Container instance (ACI) – používá se pro účely testování spíše více než provozní nasazení ve velkém měřítku. Nezapomeňte vyplnit hodnoty aspoň pro jednu jader pro _kapacitu kapacity procesoru_a aspoň jeden GIGABAJT (GB) pro _kapacitu rezervy paměti_ .
     + Služba Azure Kubernetes (AKS)) – Tato možnost je určena pro nasazení ve velkém měřítku. Budete muset mít připravený výpočetní výkon založený na AKS.

     ![Fotografií Vytvoření nasazení](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Až budete hotovi, vyberte **Vytvořit**. Nasazení modelu může trvat několik minut, než se každý kanál dokončí.

1. A to je vše! Máte provozní webovou službu, která generuje předpovědi.

## <a name="next-steps"></a>Další postup

* [Přečtěte si další informace o automatizovaném strojovém učení](concept-automated-ml.md) a Azure Machine Learning.
* [Naučte se využívat webovou službu](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
