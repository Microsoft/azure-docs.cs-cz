---
title: 'PCA-Based detekce anomálií: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat PCA-Based modul pro detekci anomálií k vytvoření modelu detekce anomálií založeného na analýze základní součásti (DPS).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898458"
---
# <a name="pca-based-anomaly-detection-module"></a>Modul detekce anomálií PCA-Based

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul pro detekci anomálií PCA-Based, který umožňuje vytvořit model detekce anomálií založený na analýze základní součásti (DPS).

Tento modul vám pomůže sestavit model ve scénářích, kde je snadné získat školicí data z jedné třídy, jako jsou například platné transakce, ale obtížné získat dostatečné vzorky cílových anomálií. 

Například pro detekci podvodných transakcí často nemusíte mít k dispozici dostatek příkladů podvodů ke školení. Je ale možné, že máte spoustu příkladů dobrých transakcí. Modul pro detekci anomálií PCA-Based vyřeší problém analýzou dostupných funkcí a určí, co představuje "normální" třídu. Modul pak použije metriky vzdálenosti k identifikaci případů, které reprezentují anomálie. Tento přístup umožňuje vytvořit model pomocí stávajících nevyvážených dat.

## <a name="more-about-principal-component-analysis"></a>Další informace o analýze základních komponent

DPS je vytvořená technika ve strojovém učení. Často se používá při analýze dat pro průzkumné účely, protože odhalí vnitřní strukturu dat a vysvětluje odchylku v datech.

Funkce DPS funguje při analýze dat, která obsahují více proměnných. Vyhledá korelaci mezi proměnnými a určí kombinaci hodnot, které nejlépe zachytí rozdíly ve výsledcích. Tyto kombinované hodnoty funkcí slouží k vytvoření kompaktnějšího prostoru funkcí označovaného jako *hlavní komponenty*.

Pro detekci anomálií se analyzují všechny nové vstupy. Algoritmus detekce anomálií počítá svou projekci na eigenvectors společně s normalizovanou chybou rekonstrukce. Normalizovaná chyba se používá jako skóre anomálií. Čím vyšší je chyba, tím více neobvyklé instance je.

Další informace o tom, jak funguje DPS a o implementaci pro detekci anomálií, najdete v těchto dokladech:

- [Náhodný algoritmus pro analýzu hlavních komponent](https://arxiv.org/abs/0809.2274), od Rokhlin, Szlan a Tygert

- [Hledání struktury s náhodností: pravděpodobnostní algoritmy pro vytváření přibližných dekompozicí matice](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (stažení PDF), podle Halko, Martinsson a Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Jak nakonfigurovat PCA-Based detekci anomálií

1. Přidejte modul pro **detekci anomálií založený na DPS** do kanálu v návrháři. Tento modul můžete najít v kategorii **detekce anomálií** .

2. V pravém panelu modulu vyberte možnost **školicí režim** . Určete, zda chcete model naučit pomocí konkrétní sady parametrů, nebo použijte k nalezení nejlepších parametrů použití Sweep parametrů.

    Pokud víte, jak chcete model konfigurovat, vyberte možnost **jeden parametr** a poskytněte konkrétní sadu hodnot jako argumenty.

3. **Počet komponent, které se mají použít v DPS**, určíte tak, že zadáte počet výstupních funkcí nebo součástí.

    Rozhodnutí o tom, kolik součástí se má zahrnout, je důležitou součástí návrhu experimentu, který používá DPS. Obecné pokyny je, že byste neměli zahrnovat stejný počet součástí DPS, jako jsou proměnné. Místo toho byste měli začít s menším počtem komponent a zvyšovat je, dokud nesplní určité kritérium.

    Nejlepších výsledků je dosaženo, pokud je počet výstupních komponent *menší než* počet sloupců funkcí dostupných v datové sadě.

4. Zadejte množství převzorkování, které se má provést během náhodného školení DPS. Při potížích s detekcí anomálií nevyvážená data obtížně používají standardní techniky DPS. Zadáním určité míry převzorkování můžete zvýšit počet cílových instancí.

    Zadáte-li hodnotu **1**, není provedeno žádné převzorkování. Pokud zadáte libovolnou hodnotu větší než **1**, vygenerují se další vzorky, které se použijí při výuce modelu.

    Existují dvě možnosti v závislosti na tom, zda používáte parametr Sweep nebo nikoli:

    - **Parametr převzorkování pro náhodný DPS**: Zadejte jedno celé číslo, které představuje poměr převzorkování třídy menšiny přes normální třídu. (Tato možnost je k dispozici, pokud používáte metodu školení s **jedním parametrem** .)

    > [!NOTE]
    > Nemůžete zobrazit sadu dat s převzorkováním. Další informace o tom, jak se převzorkování používá s DPS, najdete v tématu [technické poznámky](#technical-notes).

5. Chcete-li normalizovat všechny vstupní funkce na střední hodnotu nuly, vyberte možnost **Povolit funkci pro zadání střední hodnoty** pro normalizaci. Normalizace nebo škálování na nulu se obecně doporučuje pro DPS, protože cílem DPS je maximalizovat odchylku mezi proměnnými.

    Tato možnost je ve výchozím nastavení zaškrtnutá. Zrušte výběr, pokud hodnoty již byly normalizovány pomocí jiné metody nebo měřítka.

6. Připojte datovou sadu s tagovaným školením a jeden ze školicích modulů.

   Pokud nastavíte možnost **vytvořit režim Trainer** na **jeden parametr**, použijte modul [detekce anomálií pro vlaky](train-anomaly-detection-model.md) .

7. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení můžete vyškolený model Uložit. Nebo ho můžete propojit s modulem [bodového modelu](score-model.md) pro předpověď výsledků anomálií.

Pro vyhodnocení výsledků modelu detekce anomálií:

1. Ujistěte se, že je sloupec skóre v obou datových sadách k dispozici.

    Pokud se pokusíte vyhodnotit model detekce anomálií a zobrazí se chyba "neexistuje žádný sloupec skóre v sadě dat s skóre, která by se měla porovnat", "používáme typickou zkušební datovou sadu, která obsahuje sloupec popisku, ale žádné výsledky nepravděpodobností. Vyberte datovou sadu, která odpovídá výstupu schématu pro modely detekce anomálií, které zahrnují **popisky skóre** a sloupce **pravděpodobnosti skóre** .

2. Ujistěte se, že jsou označeny popisky sloupců.

    Někdy se Metadata přidružená k sloupci popisek v grafu kanálu odeberou. Pokud k tomu dojde, když použijete modul [vyhodnocení modelu](evaluate-model.md) k porovnání výsledků dvou modelů detekce anomálií, může se zobrazit chyba "neexistuje žádný sloupec popisku v datové sadě s skóre". Nebo se může zobrazit chyba "neexistuje žádný sloupec popisku v datové sadě s hodnocením k porovnání".

    Těmto chybám se můžete vyhnout přidáním modulu [Upravit metadata](edit-metadata.md) před modulem [vyhodnocení modelu](evaluate-model.md) . K výběru sloupce třída použijte selektor sloupců a v seznamu **pole** vyberte možnost **popisek**.

3. Pomocí modulu [spuštění skriptu Pythonu](execute-python-script.md) můžete upravit kategorie sloupců popisku na hodnotu **1 (kladné, normální)** a **0 (záporná,** neobvyklá).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technické poznámky

Tento algoritmus používá DPS k aproximaci podprostoru, který obsahuje normální třídu. Podprostor je rozložený podle eigenvectors přidruženého k hornímu eigenvalues matice kovariance dat. 

U každého nového vstupu detektor anomálií nejprve vypočítá svou projekci na eigenvectors a pak vypočítá normalizovanou chybu rekonstrukce. Tato chyba je skóre anomálie. Čím vyšší je chyba, tím více neobvyklé instanci. Podrobnosti o tom, jak se počítá normální místo, najdete v tématu Wikipedii: [Analýza hlavní komponenty](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly návrháře naleznete v tématu [výjimky a kódy chyb pro návrháře](designer-error-codes.md) .