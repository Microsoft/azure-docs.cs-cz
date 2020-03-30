---
title: Extrahování n-gramových funkcí z odkazu textového modulu
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Extract N-Gram v Azure Machine Learning featurize textová data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477608"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrahování n-gramových funkcí z odkazu textového modulu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview). Pomocí funkce Extrahovat n-gram z textového modulu *featurize* nestrukturovaných textových dat. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfigurace prvků Extrahovat N-Gram z textového modulu

Modul podporuje následující scénáře pro použití slovníku n-gram:

* [Vytvořte nový slovník n-gram](#create-a-new-n-gram-dictionary) ze sloupce volného textu.

* [Použijte existující sadu textových funkcí](#use-an-existing-n-gram-dictionary) featurize sloupec volného textu.

* [Skóre nebo publikovat model,](#score-or-publish-a-model-that-uses-n-grams) který používá n-gramů.

### <a name="create-a-new-n-gram-dictionary"></a>Vytvoření nového slovníku n-gram

1.  Přidejte extrahujte n-gramové funkce z textového modulu do kanálu a připojte datovou sadu, která obsahuje text, který chcete zpracovat.

1.  Pomocí **sloupce Text** vyberte sloupec typu řetězce, který obsahuje text, který chcete extrahovat. Vzhledem k tomu, že výsledky jsou podrobné, můžete zpracovat pouze jeden sloupec najednou.

1. Nastavte **režim slovní zásoby** na **Vytvořit,** abyste označili, že vytváříte nový seznam funkcí n-gram. 

1. Nastavte **n-gramů velikost** označuje *maximální* velikost n-gramů extrahovat a ukládat. 

    Pokud například zadáte 3, vytvoří se jednogramy, bigramy a trigramy.

1. **Funkce vážení** určuje, jak vytvořit vektor funkce dokumentu a jak extrahovat slovní zásobu z dokumentů.

    * **Binární hmotnost**: Přiřadí binární hodnotu přítomnosti extrahovanénce n-gramů. Hodnota pro každý n-gram je 1, pokud existuje v dokumentu a 0 jinak.

    * **Hmotnost TF**: Přiřadí skóre frekvence termínu (TF) extrahovanému n-gramů. Hodnota pro každý n-gram je četnost výskytu v dokumentu.

    * **Hmotnost IDF**: Přiřadí extrahovanému n-gramu inverzní skóre frekvence dokumentu (IDF). Hodnota pro každý n-gram je protokol velikosti korpusu dělený četností výskytu v celém korpusu.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Hmotnost TF-IDF**: Přiřadí skóre frekvence/inverzní frekvence dokumentu (TF/IDF) extrahovanénce n-gramů. Hodnota pro každý n-gram je jeho skóre TF vynásobené jeho skóre IDF.

1. Nastavte **minimální délku slova** na minimální počet písmen, která lze použít v *libovolném jediném slově* v n-gramu.

1. Maximální **délka slova** slouží k nastavení maximálního počtu písmen, která lze použít v *libovolném jediném slově* v n-gramu.

    Ve výchozím nastavení je povoleno až 25 znaků na slovo nebo token.

1. Minimální **absolutní četnost n-gram dokumentu** slouží k nastavení minimálních výskytů požadovaných pro všechny n-gramy, které mají být zahrnuty do slovníku n-gram. 

    Pokud například použijete výchozí hodnotu 5, musí se všechny n-gramy objevit alespoň pětkrát v korpusu, aby byly zahrnuty do slovníku n-gram. 

1.  Nastavte **maximální poměr dokumentu n-gram** k maximálnímu poměru počtu řádků, které obsahují určitý n-gram, nad počtem řádků v celkovém korpusu.

    Například poměr 1 by znamenalo, že i když je v každém řádku přítomen určitý n-gram, n-gram může být přidán do slovníku n-gram. Obvykle slovo, které se vyskytuje v každém řádku by být považovány za šum slovo a bude odebrána. Chcete-li odfiltrovat šumová slova závislá na doméně, zkuste tento poměr snížit.

    > [!IMPORTANT]
    > Rychlost výskytu určitých slov není jednotná. To se liší od dokumentu k dokumentu. Pokud například analyzujete komentáře zákazníků k určitému produktu, může být název produktu velmi vysoký a blízký šumu, ale v jiných kontextech se jedná o významný termín.

1. Vyberte volbu **Normalizovat vektory n-gram prvku,** chcete-li normalizovat vektory prvku. Pokud je tato možnost povolena, každý vektor prvku n-gram je vydělen normou L2.

1. Odešlete potrubí.

### <a name="use-an-existing-n-gram-dictionary"></a>Použití existujícího slovníku n-gram

1.  Přidejte extrahovat n-gramové funkce z textového modulu do kanálu a připojte datovou sadu, která obsahuje text, který chcete zpracovat, k portu **datové sady.**

1.  Pomocí **sloupce Text** vyberte textový sloupec obsahující text, který chcete featurize. Ve výchozím nastavení modul vybere všechny sloupce typu **řetězec**. Nejlepších výsledků dosáhnete zpracováním jednoho sloupce současně.

1. Přidejte uloženou datovou sadu, která obsahuje dříve generovaný slovník n-gram, a připojte ji k portu **slovníku Input.** Můžete také připojit **výstup slovní zásoby výsledek** upstream instance extrahovat n-gram funkce z textového modulu.

1. V **režimu slovníku**vyberte možnost **Aktualizace jen pro čtení** v rozevíracím seznamu.

   Možnost **Jen pro čtení** představuje vstupní korpus pro vstupní slovní zásobu. Spíše než výpočetní termín frekvence z nové textové datové sady (na levém vstupu), n-gram váhy ze vstupní slovní zásoby jsou použity tak, jak je.

   > [!TIP]
   > Tuto možnost použijte, pokud vyhodnocujete klasifikátor textu.

1.  Všechny ostatní možnosti naleznete v popisech vlastností v [předchozí části](#create-a-new-n-gram-dictionary).

1.  Odešlete potrubí.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Skóre nebo publikování modelu, který používá n-gramů

1.  Zkopírujte **funkce Extrahovat N-Gram z modulu Text** z toku dat školení do toku dat vyhodnocování.

1.  Připojte **výstup výsledkové slovní zásoby** z toku dat školení vstupní **slovní zásobu** na tok dat bodování.

1.  V pracovním postupu hodnocení upravte funkce Extrahování N-Gram z textového modulu a nastavte parametr **režimu slovníku** na **hodnotu Jen pro čtení**. Nechte všechno ostatní stejné.

1.  Chcete-li publikovat kanál, uložte **slovní zásobu výsledků** jako datovou sadu.

1.  Připojte uloženou datovou sadu k extrahování n-gramových funkcí z textového modulu v grafu hodnocení.

## <a name="results"></a>Výsledky

Funkce Extrahovat N-Gram z textového modulu vytvoří dva typy výstupů: 

* **Výsledná datová sada**: Tento výstup je souhrnem analyzovaného textu v kombinaci s n-gramy, které byly extrahovány. Sloupce, které jste nevybrali ve **sloupci Text,** jsou předány výstupu. Pro každý sloupec textu, který analyzujete, modul generuje tyto sloupce:

  * **Matice n-gram výskytů**: Modul vygeneruje sloupec pro každý n-gram nalezený v celkovém korpusu a přidá do každého sloupce skóre označující hmotnost n-gramu pro tento řádek. 

* **Výsledek slovní zásoby**: Slovní zásoba obsahuje skutečný n-gram slovník, spolu s termínem frekvence skóre, které jsou generovány jako součást analýzy. Datovou sadu můžete uložit pro opakované použití s jinou sadou vstupů nebo pro pozdější aktualizaci. Můžete také znovu použít slovní zásobu pro modelování a bodování.

### <a name="result-vocabulary"></a>Výsledná slovní zásoba

Slovní zásoba obsahuje slovník n-gram s termínem frekvenční skóre, které jsou generovány jako součást analýzy. Skóre DF a IDF jsou generovány bez ohledu na jiné možnosti.

+ **ID**: Identifikátor generovaný pro každý jedinečný n-gram.
+ **NGram**: N-gram. Mezery nebo jiné oddělovače slov jsou nahrazeny znakem podtržítka.
+ **DF**: Termín frekvence skóre pro n-gram v původním korpusu.
+ **IDF**: Inverzní skóre frekvence dokumentu pro n-gram v původním korpusu.

Tuto datovou sadu můžete aktualizovat ručně, ale může dojít k chybám. Například:

* Pokud modul najde duplicitní řádky se stejným klíčem ve vstupním slovníku, je vyvolána chyba. Ujistěte se, že žádné dva řádky ve slovní zásobě nemají stejné slovo.
* Vstupní schéma datových sad slovníku musí přesně odpovídat, včetně názvů sloupců a typů sloupců. 
* Sloupec **ID** a sloupec **DF** musí být celého typu. 
* Sloupec **IDF** musí být typu float.

> [!Note]
> Nepřipojujte datový výstup přímo k modulu Model vlaku. Sloupce volného textu byste měli odstranit dříve, než jsou vloženy do modelu vlaku. V opačném případě budou sloupce volného textu považovány za kategorické prvky.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.
