---
title: 'Převést na datovou sadu: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul převést na datovou sadu ve službě Azure Machine Learning k převodu vstupu dat na formát interní datové sady, který používá Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516250"
---
# <a name="convert-to-dataset"></a>Převést na datovou sadu

Tento článek popisuje, jak použít modul [převést na datovou sadu](convert-to-dataset.md) v Návrháři Azure Machine Learning (Preview) k převedení libovolných dat kanálu do interního formátu používaného návrhářem.
  
Ve většině případů není převod vyžadován, protože Azure Machine Learning implicitně převádí data na formát nativní datové sady, když je provedena jakákoli operace s daty. 

Ukládání dat do formátu datové sady je však doporučeno, pokud jste provedli určitý druh normalizace nebo čištění na základě sady dat a chcete zajistit, aby se změny používaly v dalších kanálech.  
  
> [!NOTE]
> [Převod na datovou sadu](convert-to-dataset.md) mění pouze formát dat. Neuloží novou kopii dat v pracovním prostoru. Datovou sadu uložte dvojitým kliknutím na výstupní port, vyberte **Uložit jako datovou sadu**a zadejte nový název.  
  
## <a name="how-to-use-convert-to-dataset"></a>Jak použít převod na datovou sadu  

Doporučujeme, abyste před použitím rutiny [převést na datovou](convert-to-dataset.md)sadu připravili datovou sadu pomocí modulu [Upravit metadata](edit-metadata.md) .  Můžete přidávat nebo měnit názvy sloupců, upravovat typy dat a tak dále.

1.  Přidejte do svého kanálu modul [převést do datové sady](convert-to-dataset.md) . Tento modul můžete najít v kategorii **transformace dat** v návrháři. 

2. Připojte ho ke všem modulům, které výstupují datovou sadu.   

    Pokud jsou data [tabulková](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), můžete je převést na datovou sadu. To zahrnuje data načtená pomocí [importovaná](import-data.md)data, data vytvořená pomocí [ručního zadání dat](enter-data-manually.md)nebo datových sad [transformovaná pomocí transformace použít](apply-transformation.md).

3.  V rozevíracím seznamu **Akce** určete, zda chcete provést čištění dat před uložením datové sady:  
  
    - **Žádné**: použijte data tak, jak je.  
  
    - **SetMissingValue**: v datové sadě nastavte určitou hodnotu na chybějící hodnotu. Výchozí zástupný symbol je znak otazníku (?), ale můžete použít možnost **vlastní chybějící hodnota** k zadání jiné hodnoty. Pokud například zadáte "taxislužby" pro **vlastní chybějící hodnotu**, pak všechny "taxislužby" v datové sadě budou změněny na chybějící hodnotu.
  
    - **ReplaceValues**: tuto možnost použijte, pokud chcete zadat jedinou hodnotu, která se má nahradit jakoukoliv jinou přesnou hodnotu.  Chybějící hodnoty nebo vlastní hodnoty můžete nahradit nastavením metody **Replace** :- **Missing**: tuto možnost vyberte, pokud chcete nahradit chybějící hodnoty vstupní datovou sadou. V poli **Nová hodnota**zadejte hodnotu, která má chybějící hodnoty nahradit hodnotou.
            - **vlastní**: tuto možnost vyberte, pokud chcete nahradit vlastní hodnoty vstupní datovou sadou. Do pole **vlastní hodnota**zadejte hodnotu, kterou chcete najít. Například za předpokladu, že data obsahují řetězec `obs` pro chybějící hodnoty použit jako zástupný symbol, zadáte `obs`. V poli **Nová hodnota**zadejte novou hodnotu, která má původní řetězec nahradit hodnotou.
  
    Všimněte si, že operace **ReplaceValues** se vztahuje pouze na přesné shody. Například by tyto řetězce nebyly ovlivněny: `obs.`, `obsolete`.  
 
  
5.  Spusťte kanál, nebo klikněte pravým tlačítkem na modul [převést na datovou sadu](convert-to-dataset.md) a vyberte **Spustit vybrané**.  

## <a name="results"></a>Výsledky

+  Chcete-li uložit výslednou datovou sadu s novým názvem, klikněte pravým tlačítkem na výstup [převést na datovou sadu](convert-to-dataset.md) a vyberte **Uložit jako datovou sadu**.  
  
## <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

-   Libovolný modul, který přebírá datovou sadu jako vstup, může také přebírat data ve formátu CSV nebo TSV. Před provedením jakéhokoli kódu modulu je proveden předzpracování vstupů, což je ekvivalentem spuštění modulu [převést na datovou sadu](convert-to-dataset.md) na vstupu.  
  
-   Z formátu SVMLight nelze převést na DataSet.  
  
-   Při určování vlastní operace nahrazení se operace vyhledávání a nahrazování vztahují na úplné hodnoty; částečné shody nejsou povoleny. Například můžete nahradit 3 znakem-1 nebo s 33, ale nemůžete nahradit 3 za dvoumístné číslo, například 35.  
  
-   U vlastních operací nahrazení dojde k tiché chybě, pokud použijete jako náhradu libovolný znak, který není v souladu s aktuálním datovým typem sloupce.  

  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
