---
title: Extrahovat N-gram funkcí z odkazu na modul textu
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul extrakce N-gramů ve službě Azure Machine Learning k zpracování textových dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 0803627b8d2e9fb3db2c7c96d7dd74e9b275f5d8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170997"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrahovat N-gram funkcí z odkazu na modul textu

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning. K *zpracování* nestrukturovaných textových dat použijte z modulu text extrakci N-gram funkcí. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfigurace funkcí extrakce N-gramů z modulu textu

Modul podporuje následující scénáře pro použití slovníku n-gramů:

* [Vytvoří nový slovník n-gramů](#create-a-new-n-gram-dictionary) ze sloupce s volným textem.

* [Pomocí existující sady textových funkcí](#use-an-existing-n-gram-dictionary) zpracování volný textový sloupec.

* [Skóre nebo publikování modelu](#score-or-publish-a-model-that-uses-n-grams) , který používá n-gramů.

### <a name="create-a-new-n-gram-dictionary"></a>Vytvořit nový slovník n-gramů

1.  Přidejte funkce extrahovat N-gram z modulu textu do experimentu a připojte datovou sadu, která obsahuje text, který chcete zpracovat.

1.  Pomocí **textového sloupce** můžete zvolit sloupec typu String, který obsahuje text, který chcete extrahovat. Vzhledem k tomu, že výsledky jsou podrobné, můžete současně zpracovat pouze jeden sloupec.

1. Nastavte **režim slovníku** , který se má **vytvořit** , aby označoval, že vytváříte nový seznam funkcí n-gramů. 

1. **Velikost n-gramů** nastavte tak, aby označovala *maximální* velikost n-gramů k extrakci a uložení. 

    Pokud například zadáte 3, unigrams, bigrams a trigrams, vytvoří se.

1. **Funkce váhy** určuje, jak se má vytvořit vektor funkce dokumentu a jak z dokumentů extrahovat slovník.

    * **Binární váha**: Přiřadí binární hodnotu přítomnosti extrahovaných n-gramů. Hodnota pro každý n-gram je 1, pokud v dokumentu existuje, a 0 jinak.

    * **TF Weight**: Přiřadí skóre termínu (TF) do extrahovaných n-gramů. Hodnota pro každý n-gram je jeho četnost výskytů v dokumentu.

    * **Hmotnost IDF**: Přiřadí skóre inverzního dokumentu (IDF) k extrahovaným n-gramům. Hodnota pro každý n-gram je protokol velikosti corpus dělené četností výskytů v celém corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Váha TF-IDF**: Přiřadí skóre termínu/inverzní funkce četnosti dokumentů (TF/IDF) na extrahované n-gramy. Hodnota pro každé n-gram je jeho skóre TF vynásobené skóre IDF.

1. Nastavte **minimální délku slova** na minimální počet písmen, která se dají použít v jakémkoli *jediném slově* v n-gramu.

1. Použijte možnost **Maximální délka slova** pro nastavení maximálního počtu písmen, která lze použít v libovolném *slově* v n-gramu.

    Ve výchozím nastavení je povolený až 25 znaků na jedno slovo nebo token.

1. Chcete-li nastavit minimální počet výskytů, které jsou požadovány pro všechny n-gramy ve slovníku n-gram, použijte **Minimální četnost dokumentů n-gram** . 

    Pokud například použijete výchozí hodnotu 5, všechna n-gram se musí v Corpus objevit alespoň pětkrát, aby bylo možné zahrnout do slovníku n-gramů. 

1.  Nastaví **maximální poměr dokumentů n-gram** na maximální poměr počtu řádků, které obsahují konkrétní n-gram, za počet řádků v celkové corpus.

    Například poměr 1 znamená, že i v případě, že je v každém řádku k dispozici konkrétní n-gram, nelze do slovníku n-gramů přidat n-gram. Obvykle se slovo, které se vyskytuje v každém řádku, považuje za neobsažných slov a bude odebráno. Pokud chcete vyfiltrovat neobsažných slov závislých na doméně, zkuste tento poměr omezit.

    > [!IMPORTANT]
    > Míra výskytu konkrétních slov není jednotná. Liší se od dokumentu k dokumentu. Pokud například analyzujete připomínky zákazníků k určitému produktu, může být název produktu velmi vysoký frekvencí a blízko neobsažných slov, ale v jiných kontextech se jedná o významný termín.

1. Vyberte možnost **normalizovat vektory funkcí n-gramů** pro normalizaci vektorů funkcí. Pokud je tato možnost povolená, každý n-gram funkce Vector se vydělí jeho normou L2.

1. Spusťte experiment.

### <a name="use-an-existing-n-gram-dictionary"></a>Použít existující slovník n-gramů

1.  Přidejte funkce extrakce N-gramů z modulu textu do experimentu a připojte datovou sadu, která obsahuje text, který chcete zpracovat na port **datové sady** .

1.  Pomocí **textového sloupce** vyberte sloupec text, který obsahuje text, který chcete zpracování. Ve výchozím nastavení modul vybere všechny sloupce typu **řetězec**. Nejlepších výsledků dosáhnete, když najednou zpracujete jeden sloupec.

1. Přidejte uloženou datovou sadu, která obsahuje dříve generovaný slovník n-gramů, a připojte ho ke **vstupnímu portu slovníku** . Můžete také propojit výstup **slovníku výsledků** z nadřazené instance extrahovat N-gram funkcí z modulu textu.

1. V části **režim slovníku**v rozevíracím seznamu vyberte možnost aktualizace **jen pro čtení** .

   Možnost **ReadOnly** reprezentuje vstupní corpus pro vstupní slovník. Místo výpočetní frekvence z nové datové sady textu (na levém vstupu) se použije n-gram závaží ze vstupního slovníku, jak je.

   > [!TIP]
   > Tuto možnost použijte při bodování klasifikátoru textu.

1.  Všechny ostatní možnosti najdete v popisech vlastností v [předchozí části](#create-a-new-n-gram-dictionary).

1.  Spusťte experiment.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Skóre nebo publikování modelu, který používá n-gramů

1.  Zkopírujte **funkce extrakce N-gramů z modulu textu** z školicího toku dat do toku dat bodování.

1.  Připojte výstup **slovníku výsledků** z školicího toku dat ke **vstupnímu slovníku** v toku dat bodování.

1.  V pracovním postupu bodování upravte funkce extrakce N-gramů z textového modulu a nastavte parametr **režimu slovníku** na **jen pro čtení**. Všechny ostatní ponechte stejné.

1.  Chcete-li publikovat experiment, uložte **slovník výsledků** jako datovou sadu.

1.  Připojte uloženou datovou sadu k extrakci N-gram funkcí z modulu textu v grafu bodování.

## <a name="results"></a>Výsledky

Extrahování N-gramů funkcí z textového modulu vytvoří dva typy výstupu: 

* **Výsledná sada výsledků**: Tento výstup je souhrnem analyzovaného textu v kombinaci s n-gramy, které byly extrahovány. Sloupce, které jste nevybrali v možnosti **textový sloupec** , se předávají do výstupu. Pro každý sloupec textu, který analyzujete, modul vygeneruje tyto sloupce:

  * **Matice výskytů n-gramů**: Modul generuje sloupec pro každý n-gram, který se nachází v celkovém corpus, a do každého sloupce přidá skóre, které určuje váhu n-gramu pro daný řádek. 

* **Slovník výsledků**: Slovník obsahuje skutečný slovník n-gramů spolu s termínem četnosti, která jsou generována jako součást analýzy. Datovou sadu můžete uložit pro opakované použití s jinou sadou vstupů nebo pro pozdější aktualizace. Slovník můžete také použít pro modelování a bodování.

### <a name="result-vocabulary"></a>Slovník výsledků

Slovník obsahuje slovník n-gramů s termínem četnosti, která se generují jako součást analýzy. Skóre DF a IDF jsou vygenerována bez ohledu na jiné možnosti.

+ **ID**: Identifikátor generovaný pro každý jedinečný n-gram.
+ **NGram**: N-gram. Mezery nebo jiné oddělovače slov jsou nahrazeny znakem podtržítka.
+ **DF**: Skóre termínu pro n-gram v původní Corpus
+ **IDF**: Skóre četnosti inverzních dokumentů pro n-gram v původní Corpus

Tuto datovou sadu můžete ručně aktualizovat, ale můžete uvést chyby. Příklad:

* Pokud modul nalezne v vstupním slovníku duplicitní řádky se stejným klíčem, vyvolá se chyba. Ujistěte se, že žádné dva řádky ve slovníku nemají stejné slovo.
* Vstupní schéma datových sad slovníku se musí přesně shodovat, včetně názvů sloupců a typů sloupců. 
* Sloupec **ID** a sloupec **DF** musí být typu Integer. 
* Sloupec **IDF** musí být typu float.

> [!Note]
> Nepřipojujte výstup dat přímo modulu vlakového modelu. Před odesláním do modelu vlaků byste měli odstranit sloupce s volnými textem. V opačném případě se sloupce s volnými textem budou zacházet jako s funkcemi kategorií.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro službu Azure Machine Learning. 
