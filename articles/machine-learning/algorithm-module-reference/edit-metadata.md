---
title: 'Úprava metadat: Reference k modulu'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu upravit Metadata ve službě Azure Machine Learning, chcete-li změnit metadata, která je přidružená k sloupců v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072707"
---
# <a name="edit-metadata-module"></a>Upravit Metadata modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Chcete-li změnit metadata, která je přidružená k sloupců v datové sadě pomocí modulu upravit Data. Po použití modulu upravit Metadata se změní hodnota a datový typ datové sady.

Typické metadat změny mohou zahrnovat:
  
+ Zpracuje logická nebo číselné sloupce jako hodnoty zařazené do kategorií.
  
+ Určující, který sloupec obsahuje **třídy** popisek nebo obsahuje hodnoty, které chcete kategorizovat nebo předpovědět.
  
+ Označení sloupce jako funkce.
  
+ Změna hodnoty data a času na číselné hodnoty nebo naopak.
  
+ Přejmenování sloupců.
  
 Upravit Metadata použijte, kdykoli potřebujete změnit definici sloupce, obvykle podle požadavků pro příjem dat modul. Například některé moduly fungovat jenom s konkrétní datové typy nebo vyžadují příznaky u sloupců, jako například `IsFeature` nebo `IsCategorical`.  
  
 Po provedení požadované operace, můžete obnovit metadata do původního stavu.
  
## <a name="configure-edit-metadata"></a>Konfigurace upravit Metadata
  
1. Ve službě Azure Machine Learning přidejte modul upravit Metadata do experimentu a připojte datovou sadu, kterou chcete aktualizovat. Můžete najít datovou sadu v rámci **transformace dat** v **manipulovat** kategorie.
  
1. Vyberte **spusťte selektor sloupců** a zvolte sloupec nebo sadu sloupců pro práci s. Můžete zvolit sloupce jednotlivě podle názvu nebo indexu, nebo můžete vybrat skupinu sloupců podle typu.  
  
1. Vyberte **datový typ** možnost, pokud je potřeba přiřadit jiný datový typ vybraného sloupce. Může být potřeba změnit datový typ pro určité operace. Například pokud vaše zdrojová datová sada má čísla zpracována jako text, musíte změnit je na číselný datový typ před použitím matematických operací.

    + Podporované datové typy jsou **řetězec**, **celé číslo**, **Double**, **logická**, a **data a času**.

    + Pokud vyberete více sloupců, musíte použít změny metadat *všechny* vybrané sloupce. Řekněme například, že vyberete dva nebo tři číselné sloupce. Můžete je změnit všechny na řetězcový datový typ a přejmenovat v rámci jedné operace. Z plovoucí na celé nelze však změnit jeden sloupec pro datový typ řetězce a další sloupec.
  
    + Pokud nezadáte nového datového typu, sloupec metadat se nezmění.

    + Typ sloupce a hodnoty se změní po provedení operace upravit Metadata. Kdykoli můžete obnovit původního datového typu pomocí upravit Metadata obnovení datový typ sloupce.  

    > [!NOTE]
    > Pokud změníte jakýkoli typ číslo **data a času** zadejte, ponechte **formátu data a času** prázdné pole. Aktuálně není možné určit cílový formát data.  

1. Vyberte **zařazené do kategorií** možnost určit, že hodnoty ve vybraných sloupcích považovat za kategorií.

    Například můžete mít sloupec obsahující čísla 0, 1 a 2, ale vědět, že čísla ve skutečnosti rozumí "Smoker", "Non-smoker" a "Neznámý". V takovém případě označením sloupce jako zařazené do kategorií zajistíte tím, že hodnoty slouží pouze k seskupení dat a ne v číselné výpočty.
  
1. Použití **pole** možnost, pokud chcete změnit způsob, Azure Machine Learning používá data v modelu.

    + **Funkce**: Tuto možnost použijte k nastavení příznaku sloupec jako funkci v modulech, které pracují pouze na sloupce. Ve výchozím nastavení jsou považovány za funkce původně všechny sloupce.  
  
    + **Popisek**: Tuto možnost použijte k označení popisku, který je také označován jako předvídatelný atribut nebo Cílová proměnná. Mnoho modulů vyžadují že tento sloupec právě jeden popisek je k dispozici v datové sadě.

        V mnoha případech můžete odvodit Azure Machine Learning, že sloupec obsahuje popisek třídy. Nastavením těchto metadat, můžete zajistit, že se správně identifikuje sloupce. Nastavení této možnosti nezmění datové hodnoty. Změní jen způsob, kterým několik algoritmů strojového učení zpracovávají data.
  
    > [!TIP]
    > Máte dat, který se nevejde do následujících kategorií? Vaše datová sada může například obsahovat hodnoty, jako je jedinečné identifikátory, které nejsou užitečné jako proměnné. Tyto identifikátory v některých případech může způsobovat problémy při použití v modelu.
    >
    > Naštěstí Azure Machine Learning uchovává všechna vaše data, takže není nutné odstranit tyto sloupce z datové sady. Když budete potřebovat k provádění operací na některé speciální sadu sloupců, odeberte všechny ostatní sloupce dočasně pomocí [výběr sloupců v datové sadě](select-columns-in-dataset.md) modulu. Později můžete sloučit sloupce zpět do datové sady s použitím [přidat sloupce](add-columns.md) modulu.  
  
1. Pomocí následujících možností obnovení metadat na výchozí hodnoty a vymažte předchozích výběrů.  
  
    + **Vymazat funkce**: Tuto možnost použijte, chcete-li odebrat příznak funkce.  
  
         Všechny sloupce jsou zpočátku považovány za funkce. Pro moduly, které provádějí matematické operace může být nutné pro tuto možnost použijte, aby se zabránilo číselné sloupce jsou považovány za proměnné.
  
    + **Vymazat popisek**: Tuto možnost použijte, chcete-li odebrat **popisek** metadata ze zadaného sloupce.  
  
    + **Vymazat skóre**: Tuto možnost použijte, chcete-li odebrat **skóre** metadata ze zadaného sloupce.  
  
         Momentálně nelze označit explicitně sloupec jako bodů ve službě Azure Machine Learning. Některé operace však způsobit sloupec se označí jako skóre interně. Vlastní modul R může také výstupní hodnoty skóre.

1. Pro **nové názvy sloupců**, zadejte název nového vybraný sloupec nebo sloupce.  
  
    + Názvy sloupců můžete použít jenom znaky, které podporuje UTF-8 kódování. Prázdné řetězce, hodnoty Null nebo názvy, které sestávat jen z mezery nejsou povolené.  
  
    + Chcete-li přejmenovat více sloupců, zadejte názvy jako čárkami oddělený seznam v pořadí podle sloupce indexů.  
  
    + Všechny vybrané sloupce musí být přejmenován. Nejde vynechat nebo přeskočit sloupce.  
  
1. Spusťte experiment.  

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) ke službě Azure Machine Learning.
