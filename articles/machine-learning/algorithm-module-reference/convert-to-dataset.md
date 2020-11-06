---
title: 'Převést na datovou sadu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul převést na datovou sadu v Návrháři Azure Machine Learning k převodu vstupu dat do formátu interní datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 671a8f7c6fa4c20ef4fc88f57d4a946a84614389
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420865"
---
# <a name="convert-to-dataset"></a>Převod na datovou sadu

Tento článek popisuje, jak použít modul převést na datovou sadu v Návrháři Azure Machine Learning k převedení libovolných dat kanálu do interního formátu návrháře.
  
Ve většině případů není převod vyžadován. Azure Machine Learning implicitně převádí data na formát nativní datové sady, když se v datech provede nějaká operace. 

Pokud jste provedli určitý druh normalizace nebo čištění na sadě dat a chcete zajistit, aby se změny použily v jiných kanálech, doporučujeme ukládat data do formátu datové sady.  
  
> [!NOTE]
> Převod na datovou sadu mění pouze formát dat. Neuloží novou kopii dat v pracovním prostoru. Datovou sadu uložte dvojitým kliknutím na výstupní port, vyberte **Uložit jako datovou sadu** a zadejte nový název.  
  
## <a name="how-to-use-convert-to-dataset"></a>Jak použít převod na datovou sadu  

Než použijete převod na datovou sadu, doporučujeme použít modul [Upravit metadata](edit-metadata.md) a připravit datovou sadu. Můžete přidat nebo změnit názvy sloupců, upravit typy dat a podle potřeby provádět další změny.

1.  Přidejte do svého kanálu modul převést do datové sady. Tento modul můžete najít v kategorii **transformace dat** v návrháři. 

2. Připojte ho ke všem modulům, které výstupují datovou sadu.   

    Pokud jsou data [tabulková](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py), můžete je převést na datovou sadu. To zahrnuje data načtená pomocí [importu dat](import-data.md), data vytvořená prostřednictvím [ručního zadání dat](enter-data-manually.md)nebo datové sady [transformované pomocí transformace použít](apply-transformation.md).

3.  V rozevíracím seznamu **Akce** určete, zda chcete provést čištění dat před uložením datové sady:  
  
    - **Žádné** : použijte data tak, jak je.  
  
    - **SetMissingValue** : nastavte určitou hodnotu na chybějící hodnotu v datové sadě. Výchozí zástupný symbol je znak otazníku (?), ale můžete použít možnost  **vlastní chybějící hodnota** a zadat jinou hodnotu. Pokud například zadáte **taxislužby** pro **vlastní chybějící hodnotu** , všechny instance **taxislužby** v datové sadě budou změněny na chybějící hodnotu.
  
    - **ReplaceValues** : tuto možnost použijte, pokud chcete zadat jedinou hodnotu, která se má nahradit jakoukoliv jinou přesnou hodnotu. Chybějící hodnoty nebo vlastní hodnoty můžete nahradit nastavením metody **Replace** :

      - **Chybějící** : tuto možnost vyberte, pokud chcete nahradit chybějící hodnoty vstupní datovou sadou. V poli **Nová hodnota** zadejte hodnotu pro nahrazení chybějících hodnot.
      - **Vlastní** : tuto možnost vyberte, pokud chcete nahradit vlastní hodnoty vstupní datovou sadou. Do pole **vlastní hodnota** zadejte hodnotu, kterou chcete najít. Pokud například data obsahují řetězec `obs` použitý jako zástupný symbol pro chybějící hodnoty, zadáte `obs` . Pro **novou hodnotu** zadejte novou hodnotu, kterou má původní řetězec nahradit.
  
    Všimněte si, že operace **ReplaceValues** se vztahuje pouze na přesné shody. Například tyto řetězce nebudou ovlivněny: `obs.` , `obsolete` .  
 
  
5.  Odešlete kanál.  

## <a name="results"></a>Výsledky

+  Výslednou datovou sadu uložte s novým názvem tak, že na kartě **výstup** v pravém panelu modulu vyberete ikonu pro **datovou sadu registru** ikony.  
  
## <a name="technical-notes"></a>Technické poznámky  

-   Libovolný modul, který přebírá datovou sadu jako vstup, může také přebírat data v souboru CSV nebo souboru TSV. Před spuštěním jakéhokoliv kódu modulu jsou vstupy zpracovány. Předzpracování je ekvivalentem spuštění modulu převést na datovou sadu na vstupu.  
  
-   Z formátu SVMLight nelze převést na datovou sadu.  
  
-   Když zadáváte vlastní operaci nahrazení, bude se operace hledání a nahrazení týkat úplných hodnot. Částečné shody nejsou povoleny. Například můžete nahradit 3 znakem-1 nebo s 33, ale nemůžete nahradit 3 za dvoumístné číslo, například 35.  
  
-   U vlastních operací nahrazení dojde k tiché chybě, pokud použijete jako náhradu libovolný znak, který není v souladu s aktuálním datovým typem sloupce.  

  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.