---
title: 'Upravit metadata: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Upravit metadata v Azure Machine Learning změnit metadata, která je přidružená ke sloupcům v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064245"
---
# <a name="edit-metadata-module"></a>Modul Upravit metadata

Tento článek popisuje modul zahrnutý v návrháři Azure Machine Learning (preview).

Pomocí modulu Upravit metadata můžete změnit metadata, která jsou přidružená ke sloupcům v datové sadě. Hodnota a datový typ datové sady se po použití modulu Upravit metadata změní.

Typické změny metadat mohou zahrnovat:
  
+ Považovat logické nebo číselné sloupce za kategorické hodnoty.
  
+ Označuje sloupec, který obsahuje popisek **třídy** nebo obsahuje hodnoty, které chcete kategorizovat nebo předpovědět.
  
+ Označení sloupců jako prvků.
  
+ Změna hodnot data a času na číselné hodnoty nebo naopak.
  
+ Přejmenování sloupců.
  
 Upravit metadata, kdykoli budete potřebovat upravit definici sloupce, obvykle tak, aby splňovala požadavky na modul pro příjem dat. Některé moduly například pracují pouze s určitými datovými typy `IsFeature` `IsCategorical`nebo vyžadují příznaky ve sloupcích, například nebo .  
  
 Po provedení požadované operace můžete obnovit metadata do původního stavu.
  
## <a name="configure-edit-metadata"></a>Konfigurace metadat úprav
  
1. V návrháři Azure Machine Learning přidejte modul Upravit metadata do kanálu a připojte datovou sadu, kterou chcete aktualizovat. Modul najdete v kategorii **Transformace dat.**
  
1. V pravém panelu modulu klikněte na **Upravit sloupec** a zvolte sloupec nebo sadu sloupců, se kterými chcete pracovat. Sloupce můžete vybrat jednotlivě podle názvu nebo indexu nebo můžete zvolit skupinu sloupců podle typu.  
  
1. Pokud potřebujete vybraným sloupcům přiřadit jiný datový typ, vyberte možnost **Datový typ.** Možná budete muset změnit datový typ pro určité operace. Pokud má například zdrojová datová sada čísla zpracována jako text, musíte je před použitím matematických operací změnit na číselný datový typ.

    + Podporované datové typy jsou **String**, **Integer**, **Double**, **Boolean**a **DateTime**.

    + Pokud vyberete více sloupců, je nutné použít změny metadat na *všechny* vybrané sloupce. Řekněme například, že zvolíte dva nebo tři číselné sloupce. Můžete je všechny změnit na datový typ řetězce a přejmenovat je v jedné operaci. Jeden sloupec však nelze změnit na datový typ řetězce a jiný sloupec z floatu na celé číslo.
  
    + Pokud nezadáte nový datový typ, metadata sloupce se nezmění.

    + Typ sloupce a hodnoty se po provedení operace Upravit metadata změní. Původní datový typ můžete kdykoli obnovit pomocí upravit metadata k obnovení datového typu sloupce.  

    > [!NOTE]
    > Pokud změníte libovolný typ čísla na typ **DateTime,** ponechejte pole **Formát DateTime** prázdné. V současné době není možné zadat cílový formát dat.  

1. Vyberte možnost **Kategorie, chcete-li** určit, že hodnoty ve vybraných sloupcích mají být považovány za kategorie.

    Můžete mít například sloupec obsahující čísla 0, 1 a 2, ale víte, že čísla ve skutečnosti znamenají "Kuřák", "Nekuřák" a "Neznámý". V takovém případě označením sloupce jako kategorického zajistíte, že hodnoty budou použity pouze k seskupení dat a nikoli v číselných výpočtech.
  
1. Pokud chcete změnit způsob, jakým Azure Machine Learning používá data v modelu, použijte možnost **Pole.**

    + **Funkce**: Tuto možnost použijte k označení sloupce jako prvku v modulech, které pracují pouze ve sloupcích prvků. Ve výchozím nastavení jsou všechny sloupce zpočátku považovány za prvky.  
  
    + **Popisek**: Tuto možnost použijte k označení popisku, který je také označován jako předvídatelný atribut nebo cílová proměnná. Mnoho modulů vyžaduje, aby přesně jeden sloupec popisku je k dispozici v datové sadě.

        V mnoha případech azure machine learning můžete odvodit, že sloupec obsahuje popisek třídy. Nastavením těchto metadat můžete zajistit, že sloupec je identifikován správně. Nastavení této možnosti nezmění hodnoty dat. Změní pouze způsob, jakým některé algoritmy strojového učení zpracovávají data.
  
    > [!TIP]
    > Máte data, která nezapadají do těchto kategorií? Datová sada může například obsahovat hodnoty, jako jsou jedinečné identifikátory, které nejsou užitečné jako proměnné. Někdy taková ID mohou způsobit problémy při použití v modelu.
    >
    > Azure Machine Learning naštěstí zachová všechna vaše data, takže není nutné tyto sloupce z datové sady odstraňovat. Když potřebujete provádět operace s nějakou speciální sadou sloupců, stačí dočasně odebrat všechny ostatní sloupce pomocí modulu [Vybrat sloupce v datové sadě.](select-columns-in-dataset.md) Později můžete sloučit sloupce zpět do datové sady pomocí modulu [Přidat sloupce.](add-columns.md)  
  
1. Pomocí následujících možností můžete vymazat předchozí výběry a obnovit metadata na výchozí hodnoty.  
  
    + **Vymazat funkci**: Tuto možnost použijte k odebrání příznaku prvku.  
  
         Všechny sloupce jsou zpočátku považovány za funkce. U modulů, které provádějí matematické operace, může být nutné použít tuto možnost, aby se zabránilo tomu, že číselné sloupce budou považovány za proměnné.
  
    + **Vymazat popisek**: Tuto možnost použijte k odebrání metadat **popisku** ze zadaného sloupce.  
  
    + **Jasné skóre**: Tuto možnost použijte k odebrání metadat **skóre** ze zadaného sloupce.  
  
         V současné době nelze explicitně označit sloupec jako skóre v Azure Machine Learning. Některé operace však za následek sloupec je označen jako skóre interně. Vlastní modul R může také výstup hodnoty skóre.

1. V **části Nové názvy sloupců**zadejte nový název vybraného sloupce nebo sloupců.  
  
    + Názvy sloupců mohou používat pouze znaky, které jsou podporovány kódováním UTF-8. Prázdné řetězce, null nebo názvy, které se skládají výhradně z mezer, nejsou povoleny.  
  
    + Chcete-li přejmenovat více sloupců, zadejte názvy jako seznam oddělený čárkami v pořadí podle indexů sloupců.  
  
    + Všechny vybrané sloupce musí být přejmenovány. Sloupce nelze vynechat ani přeskočit.  
  
1. Odešlete potrubí.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.
