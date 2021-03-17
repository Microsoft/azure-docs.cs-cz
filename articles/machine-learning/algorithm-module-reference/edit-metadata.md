---
title: 'Upravit metadata: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu upravit metadata v Azure Machine Learning změnit metadata, která jsou přidružená ke sloupcům v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/10/2020
ms.openlocfilehash: e279a7c9f6810ece482b043284f0be0719a3dafe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908032"
---
# <a name="edit-metadata-module"></a>Upravit modul metadat

Tento článek popisuje modul zahrnutý v Návrháři Azure Machine Learning.

Pomocí modulu upravit metadata můžete změnit metadata, která jsou přidružená ke sloupcům v datové sadě. Hodnota a datový typ datové sady se po použití modulu upravit metadata změní.

Typické změny metadat mohou zahrnovat:
  
+ Zpracovávání logických nebo číselných sloupců jako hodnot kategorií
  
+ Určuje, který sloupec obsahuje popisek **třídy** nebo obsahuje hodnoty, které chcete zařadit do kategorií nebo odhadnout.
  
+ Označení sloupců jako funkcí
  
+ Změna hodnot data a času na číselné hodnoty, nebo naopak.
  
+ Přejmenování sloupců.
  
 Použijte úpravu metadat kdykoli potřebujete upravit definici sloupce, obvykle pro splnění požadavků na modul pro příjem dat. Například některé moduly fungují pouze s konkrétními datovými typy nebo vyžadují příznaky na sloupcích, například `IsFeature` nebo `IsCategorical` .  
  
 Po provedení požadované operace můžete metadata obnovit do původního stavu.
  
## <a name="configure-edit-metadata"></a>Konfigurace úprav metadat
  
1. V Návrháři Azure Machine Learning přidejte do svého kanálu modul úprav metadat a připojte datovou sadu, kterou chcete aktualizovat. Modul můžete najít v kategorii **transformace dat** .
  
1. Klikněte na **Upravit sloupec** v pravém panelu modulu a vyberte sloupec nebo sadu sloupců, se kterými chcete pracovat. Sloupce můžete zvolit jednotlivě podle názvu nebo indexu nebo můžete zvolit skupinu sloupců podle typu.  
  
1. Vyberte možnost **datového typu** , pokud pro vybrané sloupce potřebujete přiřadit jiný datový typ. Pro určité operace může být nutné změnit datový typ. Pokud má vaše zdrojová datová sada například čísla zpracovaná jako text, musíte je před použitím matematických operací změnit na číselný datový typ.

    + Podporované datové typy jsou **řetězec**, **celé číslo**, **Double**, **Boolean**a **DateTime**.

    + Pokud vyberete více sloupců, je nutné použít změny metadat u *všech* vybraných sloupců. Řekněme například, že vyberete dva nebo tři číselné sloupce. Můžete je změnit na datový typ String a přejmenovat je v jedné operaci. Nelze však změnit jeden sloupec na datový typ String a jiný sloupec z typu float na celé číslo.
  
    + Pokud nezadáte nový datový typ, metadata sloupce se nezmění.

    + Typ sloupce a hodnoty se po provedení operace Upravit metadata změní. Původní datový typ můžete kdykoli obnovit pomocí Upravit metadata a obnovit datový typ sloupce.  

    > [!NOTE]
    > **Formát data a času** odpovídá [vestavěnému formátu data a času Pythonu](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior).  
    > Pokud změníte libovolný typ čísla na typ **DateTime** , ponechte pole **formát data a času** prázdné. V současné době není možné zadat cílový formát dat.

1. Vyberte možnost **kategorií** a určete tak, že hodnoty ve vybraných sloupcích by měly být považovány za kategorie.

    Například můžete mít sloupec, který obsahuje čísla 0, 1 a 2, ale víte, že ve skutečnosti znamenají hodnoty "Kouřer", "jiný než kouřový" a "Neznámý". V takovém případě označením sloupce příznakem jako kategorií zajistíte, aby se hodnoty používaly pouze k seskupení dat a nikoli k číselným výpočtům.
  
1. Možnost **pole** použijte, pokud chcete změnit způsob, jakým Azure Machine Learning používá data v modelu.

    + **Funkce**: tuto možnost použijte k označení sloupce jako funkce v modulech, které pracují pouze na sloupcích funkcí. Ve výchozím nastavení se všechny sloupce považují za funkce.  
  
    + **Label**: tuto možnost použijte k označení popisku, který se označuje také jako předvídatelný atribut nebo cílová proměnná. Mnoho modulů vyžaduje, aby datová sada obsahovala přesně jeden sloupec popisku.

        V mnoha případech Azure Machine Learning může odvodit, že sloupec obsahuje popisek třídy. Nastavením těchto metadat můžete zajistit správnou identifikaci sloupce. Nastavením této možnosti nedojde ke změně hodnot dat. Změní se jenom na to, jak některé algoritmy strojového učení zpracovávají data.
  
    > [!TIP]
    > Máte data, která se nevejdou do těchto kategorií? Například vaše datová sada může obsahovat hodnoty jako jedinečné identifikátory, které nejsou užitečné jako proměnné. Někdy taková ID můžou způsobit problémy při použití v modelu.
    >
    > Naštěstí Azure Machine Learning udržuje všechna vaše data, takže nemusíte tyto sloupce z datové sady odstraňovat. Pokud potřebujete provést operace s určitou zvláštní sadou sloupců, stačí odebrat všechny ostatní sloupce dočasně pomocí modulu [Výběr sloupců v datové sadě](select-columns-in-dataset.md) . Později můžete sloučit sloupce zpátky do datové sady pomocí modulu [Přidat sloupce](add-columns.md) .  
  
1. Pomocí následujících možností smažete předchozí výběry a obnovíte výchozí hodnoty metadat.  
  
    + **Clear – funkce**: tuto možnost použijte, pokud chcete odebrat příznak funkce.  
  
         Všechny sloupce se zpočátku považují za funkce. U modulů, které provádějí matematické operace, možná budete muset tuto možnost použít, chcete-li zabránit tomu, aby se číselné sloupce považovat za proměnné.
  
    + **Vymazat popisek**: pomocí této možnosti můžete odebrat metadata **popisku** ze zadaného sloupce.  
  
    + **Vymazat skóre**: pomocí této možnosti můžete ze zadaného sloupce odebrat metadata **skóre** .  
  
         V tuto chvíli nemůžete explicitně označit sloupec jako skóre v Azure Machine Learning. Některé operace ale mají za následek, že se sloupec označí jako skóre interně. Také vlastní modul R může výstupní hodnoty skóre.

1. Pro **nové názvy sloupců**zadejte nový název vybraného sloupce nebo sloupce.  
  
    + Názvy sloupců můžou používat jenom znaky, které podporuje kódování UTF-8. Prázdné řetězce, hodnoty null nebo názvy, které jsou tvořeny pouze mezerami, nejsou povoleny.  
  
    + Chcete-li přejmenovat více sloupců, zadejte názvy jako seznam oddělený čárkami v pořadí indexů sloupců.  
  
    + Všechny vybrané sloupce musí být přejmenovány. Nemůžete vynechat nebo přeskočit sloupce.  
  
1. Odešlete kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.
