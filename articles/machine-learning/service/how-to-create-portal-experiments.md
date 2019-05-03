---
title: Vytvoření a prozkoumejte experimenty v portálu
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvářet a spravovat automatizované se strojovým učením na portálu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 96abef29c5290770d296fb5053007e36d1eaf537
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035449"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Vytvoření a prozkoumejte automatizované se strojovým učením na webu Azure Portal (Preview)

 V tomto článku se dozvíte, jak vytvořit, spustit a prozkoumat automatizované se strojovým učením na webu Azure Portal bez jediný řádek kódu. Automatizované strojového učení automatizuje proces výběru nejlepší algoritmus určený pro konkrétní data, takže můžete rychle vygenerovat model strojového učení. [Další informace o automatické machine learningu](concept-automated-ml.md).

 Pokud dáváte přednost prostředí více založený na kódu, můžete si také [konfigurovat vaše automatizované experimentů machine learningu v jazyce Python](how-to-configure-auto-train.md) s [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní prostor služby Azure Machine Learning. Zobrazit [vytvořit pracovní prostor služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Začínáme

Přejděte do levého podokna pracovního prostoru. Vyberte automatického Machine Learning v části vytváření obsahu (Preview).

![Azure portal navigační podokno](media/how-to-create-portal-experiments/nav-pane.png)

 Pokud je to poprvé způsobem všechny experimenty s automatizované Machine Learning, se zobrazí následující:

![Azure portal experiment cílová stránka](media/how-to-create-portal-experiments/landing-page.png)

V opačném případě se zobrazí řídicí panel automatizovaná machine learning s přehledem všechny vaše automatizované experimentů machine learningu, včetně těch, které spustit pomocí sady SDK. Tady můžete filtrovat a prozkoumejte spuštěních podle data, experimentování název a stav spuštění.

![Řídicí panel Azure portal experimentu](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Vytvoření experimentu

Vyberte tlačítko Vytvořit Experiment k naplnění následující formulář.

![Vytvoření experimentu formuláře](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Zadejte název vašeho testu.

1. Vyberte výpočetní prostředí pro profilování dat a trénovací úlohu. Seznam vaší existující výpočetní prostředí je k dispozici v rozevírací nabídce. Pokud chcete vytvořit nový výpočet, postupujte podle pokynů v kroku 3.

1. Vyberte vytvořit nové výpočetní tlačítko Otevřít pod podoknem a nakonfigurovat výpočetní kontext pro tento experiment.

    ![Vytvořit nový výpočet pro experiment](media/how-to-create-portal-experiments/create-new-compute.png)

    Pole|Popis
    ---|---
    Název výpočetních prostředků služby Machine Learning| Zadejte jedinečný název, který identifikuje váš výpočetní kontext.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetních prostředků.
    Další nastavení| *Uzel min*: Zadejte minimální počet uzlů pro vaše výpočetní prostředky. Minimální počet uzlů pro službu AML compute je 0. Pokud chcete povolit profilaci dat, musí mít 1 nebo více uzlů. <br> *Maximální počet uzlů*: Zadejte maximální počet uzlů pro vaše výpočetní prostředky. Výchozí hodnota je 6 uzly Compute AML.

      Chcete-li začít vytvářet nový výpočet, vyberte **vytvořit**. Může to chvíli trvat.

      >[!NOTE]
      > Název vašeho výpočetní označí, pokud je tak výpočetní prostředky, můžete vybrat nebo vytvořit *profilace povolena*. (Viz 7b podrobné informace o data profilace).

1. Vyberte účet úložiště pro vaše data. Ve verzi Public preview podporuje pouze účty úložiště objektů Blob v Azure a nahrání místního souboru.

1. Vyberte kontejner úložiště.

1. Vyberte soubor dat z vašeho kontejneru úložiště, nebo nahrát soubor ze svého místního počítače do tohoto kontejneru.

    ![Vyberte datový soubor pro experiment](media/how-to-create-portal-experiments/select-file.png)

1. Pomocí karty ve verzi preview a profil dále konfigurovat data pro tento experiment.

    1. Na kartě Preview označují, pokud data obsahují záhlaví a vyberte příznaky (sloupce) pro používání školení **zahrnuté** přepnout tlačítka v každém sloupci funkce.

        ![Náhled dat](media/how-to-create-portal-experiments/data-preview.png)

    1. Na kartě profil můžete zobrazit [profil dat](#profile) podle funkce, jakož i distribuční, typu a souhrnné statistiky (střední, střední, max nebo min atd.) jednotlivých.

        ![Karta data profilu](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Zobrazí se následující chybová zpráva, pokud výpočetní kontext je **není** profilace povolena: *Data profilace je dostupná jenom pro cílových výpočetních prostředí, které jsou již spuštěny*.

1. Vyberte typ úlohy trénování: klasifikace, regrese nebo Prognózování.

1. Vyberte cílový sloupec. Sloupec, který byste chtěli provést předpovědi na.

1. U prognózy:
    1. Vyberte sloupec, čas: Tento sloupec obsahuje časové údaje, který se má použít.
    1. Vyberte prognózy horizontu: Označuje, kolik jednotek doba (minut/hodin nebo dnů/týdny/měsíců nebo let) modelu bude moct předvídat budoucnost. Dál modelu se vyžaduje k předpovědi do budoucna, tím méně přesné se tak stane. [Další informace o vytváření prognóz a prognózy horizontu](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Volitelné) Upřesňující nastavení: Další nastavení, které vám umožní lepší kontrolu nad trénovací úlohu.

    Upřesnit nastavení|Popis
    ------|------
    Primární metriku| Hlavní metriku pro vyhodnocení modelu. [Další informace o metrikách modelu](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Výstupní kritéria| Pokud se splní některá z těchto kritérií, trénovací úlohu ukončí před dokončením úplné. <br> *Trénovací úlohu doba (v minutách)*: Jak dlouho se má povolit spuštění trénovací úlohy.  <br> *Maximální počet opakování*: Maximální počet kanálů (iterací) k testování v trénovací úlohu. Úloha se vůbec nespustí větší než zadaný počet iterací. <br> *Metriky skóre prahová hodnota*:  Minimální skóre metriky pro všechny kanály. Tím se zajistí, že pokud máte definovanému cíli metriky, které chcete dosáhnout, není strávíte víc času na trénovací úlohu, než je nutné.
    Předzpracování| Vyberte k povolení nebo zakázání předběžného zpracování v automatizovaných strojové učení. Předběžné zpracování zahrnuje čištění dat, přípravě a transformace ke generování syntetické funkce. [Další informace o předběžném zpracování](#preprocess).
    Ověření| Vyberte jednu z možností křížového ověření pro použití v trénovací úlohu. [Další informace o křížové ověření](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Souběžnost| Vyberte vícejádrových limity, které chcete použít při používání vícejádrové výpočetní.
    Blokované algoritmus| Výběr algoritmů, které chcete vyloučit z trénovací úlohu.

   ![Upřesňující nastavení formuláře](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Další informace o polích klikněte na popis tlačítka informace.

<a name="profile"></a>

### <a name="data-profiling"></a>Data profilování

Můžete získat obrovského množství různých souhrnné statistiky napříč datovou sadu chcete ověřit, jestli vaši datovou sadu je připravený pro ML. Pro jiné než číselné sloupce obsahují pouze základní statistiky, jako například min, max a počet chyb. Číselné sloupce můžete zkontrolovat také jejich statistické okamžiků a odhadované quantiles. Konkrétně zahrnuje naše data profilu:

* **Funkce**: název sloupce, který je automaticky shrnutý.

* **Profil**: vizualizaci v řádku na základě typu odvozen. Například řetězce, logické hodnoty a data se mají četnost hodnot desetinná čísla (číslice) mají sblížit histogramy. To umožňuje získat rychlý přehled o distribuci data.

* **Zadejte distribuční**: hodnoty v řádku počet typů ve sloupci. Hodnoty Null jsou vlastní typ, takže tuto vizualizaci je užitečné pro zjišťování liché nebo chybějící hodnoty.

* **Typ**: odvozený typ sloupce. Možné hodnoty zahrnují: řetězce, logické hodnoty, kalendářní data a desetinných míst.

* **Min**: minimální hodnota sloupce. Pro funkce, jejíž typ nemá žádné vlastní řazení (třeba logické hodnoty) se zobrazí prázdné položky.

* **Maximální počet**: maximální hodnota sloupce. Jako "minimální", se zobrazí prázdné položky pro funkce s irelevantní typy.

* **Počet**: Celkový počet chybějících a chybějící položky ve sloupci.

* **Nechybí počet**: počet položek ve sloupci, které nebyly nalezeny. Všimněte si, že prázdné řetězce a chyby jsou považovány za hodnot, tak nebude přispívat na "Ne chybí počet."

* **Quantiles** (intervalech 0.1, 1, 5, 25, 50, 75, 95, 99 a 99,9 %): přibližně hodnoty v jednotlivých quantile poskytnout představu o distribuci data. Pro funkce s irelevantní typy se zobrazí prázdné položky.

* **Znamenají**: aritmetický průměr sloupce. Pro funkce s irelevantní typy se zobrazí prázdné položky.

* **Směrodatná odchylka**: směrodatná odchylka sloupce. Pro funkce s irelevantní typy se zobrazí prázdné položky.

* **Variance**: variance sloupce. Pro funkce s irelevantní typy se zobrazí prázdné položky.

* **Zkreslení**: hodnoty ve sloupci. Pro funkce s irelevantní typy se zobrazí prázdné položky.

* **Míra fluktuace**: míra fluktuace sloupce. Pro funkce s irelevantní typy se zobrazí prázdné položky.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Pokročilé předběžného zpracování

Při konfiguraci své experimenty můžete povolit upřesňující nastavení `Preprocess`. To uděláte tak znamená, že následující kroky předběžného zpracování a snadné dat probíhají automaticky.

|Předzpracování&nbsp;kroky| Popis |
| ------------- | ------------- |
|Velkou mohutností či žádné funkce odchylka|Vyřaďte z školení a ověření sady, včetně funkcí se všemi hodnotami chybí, stejnou hodnotu napříč všemi řádky nebo s velmi vysokou kardinalitu (například hodnoty hash, ID a GUID).|
|Dává chybějící hodnoty|Numerické funkce dává s průměrem hodnot ve sloupci.<br/><br/>Zařazené do kategorií funkcí dává se nejčastěji se vyskytující hodnotu.|
|Generovat další funkce|Datum a čas funkcí: Rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodinu, minutu, sekundu.<br/><br/>Pro textové funkce: Frekvence termín, na základě unigrams, bi gramy a tri znak g.|
|Transformace a kódování |Číselné funkce s malým počtem jedinečných hodnot se transformují na zařazené do kategorií funkce.<br/><br/>Jedna hot kódování se provádí s nízkou kardinality zařazené do kategorií; pro vysokou kardinalitu jeden horkou hash kódování.|
|Vkládání slov|Featurizer text, který převádí vektory text tokenů vektory větu pomocí předem vytrénovaných modelu. Vektor vkládání jednotlivých slov v dokumentu je pro vytvoření vektoru funkce dokumentu agregovány dohromady.|
|Cíl kódování|Funkce zařazené do kategorií mapuje jednotlivých kategorií s průměrné cílovou hodnotu pro regresní problémy a pravděpodobnosti třídy pro každou třídu klasifikaci problémy. Na základě frekvence váhu a k přeložení křížové ověření se použije ke snížení přes přizpůsobování mapování a šumu způsobené Zhuštěná data kategorií.|
|Kódování textu cílové verze|Pro textové zadání skládaný lineární model se kontejner objektů a dat slova slouží ke generování pravděpodobnost, že každá třída.|
|Váha důkazů (WoE)|Vypočítá WoE jako míru korelace zařazené do kategorií sloupců do cílového sloupce. Počítá se jako protokol poměr pravděpodobnosti na více instancí třídy ve své třídě vs. Tento krok výstup jeden sloupec číselné funkce na třídu a eliminuje nutnost explicitně dává chybějící hodnoty a zpracování pravdu.|
|Vzdálenost clusteru|Trénovat k-means model clusteringu pro všechny číselné sloupce.  Výstupy k nové funkce, nová číselné funkce na cluster, obsahující vzdálenost každý vzorek těžiště každý cluster.|

## <a name="run-experiment-and-view-results"></a>Spusťte experiment a zobrazte výsledky

Spusťte experiment, klikněte na Start. Experiment Příprava proces trvá několik minut.

### <a name="view-experiment-details"></a>Zobrazit podrobnosti o testu

Po dokončení fáze přípravy experiment, zobrazí se vám na obrazovku s podrobnostmi spustit. Získáte úplný seznam modelů vytvořených. Ve výchozím nastavení, model, který určuje největší skóre na základě vašich parametrů se v horní části seznamu. Jak si další modely pokusí trénovací úlohu, se přidají do seznamu iterace a graf. Pomocí grafu iterace můžete získat rychlý porovnání metrik pro modely vytvořené zatím.

Školení úlohy může trvat nějakou dobu každý kanál na dokončení.

![Řídicí panel podrobnosti o spuštění](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Podrobnosti o spuštění zobrazit školení

K podrobnostem na žádném z modelů výstup zobrazíte podrobnosti o spuštění jako grafy metrik a distribuci výkonu školení. [Další informace o grafech](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Podrobnosti iterace](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="next-steps"></a>Další postup

* [Další informace o automatické machine learningu](concept-automated-ml.md) a Azure Machine Learning.
